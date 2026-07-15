# SRA Human Scrubber — Troubleshooting Runbook

This document is the procedure for handling FASTQ files with a`FAILED`
status (or sit in `PROCESSING` too long) because of the SRA human read scrubber. It
exists because the scrubber is the slowest, most resource intensive step during
ingest, and its failures span three different subsystems (GCP Batch, GCP
Workflows, and the Django poll/watchdog), so "the file failed" is never enough
to know where to look.

## TL;DR — the three ways a scrubber file fails

A FASTQ file lists as `FAILED` for one of three reasons. They are diagnosed
differently, so identify which one you have **before** changing anything.

1. **The Batch job failed.** The scrub VM died mid-run. The overwhelmingly
   common cause is out of disk space, not memory. The boot disk carries the
   container image (~13 GB, including the ~10 GB `human_filter.db`) plus up to
   three coexisting copies of the FASTQ during decompression/scrub. A file that
   decompresses into a size larger than estimated can overflow the disk. The workflow sees
   Batch `FAILED` and Django marks the file `FAILED`.
2. **The workflow succeeded but the file is `FAILED` anyway.** The Django-side
   poll budget (and the file's `processing_deadline`) was too short for this
   file's size, so the `cleanup_stuck_files` watchdog flipped it to `FAILED`
   while the workflow was still running or right after it finished. This is a
   budget/deadline race, not a real scrub failure.
3. **The workflow itself failed or timed out.** Rare with the current YAML,
   which polls Batch manually for up to 24h. If you see a workflow-level
   timeout at ~30 min, the **deployed** revision is older than the repo (see
   "Deployed-revision drift" below).

## Architecture recap

The scrubber runs **only for FASTQ uploads**, and only when
`ENABLE_SRA_SCRUBBER_WORKFLOW` is on and `SRA_SCRUBBER_WORKFLOW_ID` /
`SRA_SCRUBBER_WORKFLOW_OUTPUT_BUCKET` are set. Non-FASTQ files skip it entirely.

Trigger and monitoring live in `asu_apgap/uploads/celery_tasks/dlp_scan.py`.
After DLP clears, `_dispatch_sra_scrubber(...)` launches the GCP Workflow
`sra-human-scrubber-{env}`, passing per-file `disk_size_gb` / `cpu_milli` /
`memory_mib` computed by `asu_apgap/utils/resource_calculator.py`. Completion is
handled one of two ways depending on `INGEST_EVENT_DRIVEN_SCRUBBER`:

- **Event-driven** (`True`): the workflow publishes a completion notification;
  `_handle_scrubber_completion_notification(upload_id, status_value)` maps
  `SUCCEEDED` → promote/`DRAFT`, `FAILED` → file `FAILED`.
- **Polling** (`False`): a self-retrying Celery task
  (`poll_sra_scrubber_workflow`) polls the execution until terminal.

Either way, a per-file `processing_deadline` is stamped at ingest from the DLP
budget plus the file-size-scaled scrubber poll budget plus a buffer. The
`cleanup_stuck_files` watchdog (`asu_apgap/files/celery_tasks/dlp_scan.py`)
sweeps any `PROCESSING` file past its deadline to `FAILED` and notifies the
uploader. That watchdog is the backstop that prevents silent hangs, and it is
also the source of failure mode 2.

The workflow itself (`gcp-workflow/sra-human-scrubber-{env}.yaml` in the
scrubber repo) creates a Batch job with `skip_polling: true`, then loops
`googleapis.batch.v1...jobs.get` with exponential backoff until the Batch state
is `SUCCEEDED` (→ publish success) or `FAILED` (→ raise). Batch `maxRunDuration`
and the workflow `timeout_seconds` are both 86400s (24h).

## Fast triage (first 5 minutes)

Work from the file back to the Batch task. You need the lab's GCP project
(`SRA_SCRUBBER_WORKFLOW_PROJECT_ID`, else `HOST_GCP_PROJECT_ID`) and location
(`SRA_SCRUBBER_WORKFLOW_LOCATION`).

**1. Get the file and its upload.** From the Django shell or admin, find the
`File` and its `Upload` (the `Upload` carries `sra_execution_name`, the workflow
execution resource, and `processing_deadline` is on the `File`).

```
File.objects.get(id=<file_id>).status
Upload.objects.get(file_id=<file_id>).sra_execution_name
File.objects.get(id=<file_id>).processing_deadline
```

**2. Get the workflow execution state.** Use the `sra_execution_name`:

```
gcloud workflows executions describe <execution_name> \
  --project=<lab_project> --location=<location> \
  --format='value(state, error.payload)'
```

**3. Get the Batch job state and the failing task.** From Cloud Logging or the
workflow result, find the Batch job name, then:

```
gcloud batch jobs describe <job_name> --project=<lab_project> --location=<location> \
  --format='value(status.state, status.statusEvents)'
gcloud batch tasks describe <task_name> --job=<job_name> \
  --project=<lab_project> --location=<location> --task-group=group0
```

**4. Read the scrub logs.** The Batch job logs to Cloud Logging:

```
gcloud logging read \
  'labels."batch.googleapis.com/job_id"="<job_name>" AND severity>=WARNING' \
  --project=<lab_project> --limit=100 --freshness=1d
```

The state pair from steps 2 and 3 tells you which failure mode you have:

| Batch state | Workflow state | File | Failure mode |
| --- | --- | --- | --- |
| `FAILED` | `FAILED` | `FAILED` | Mode 1 — the scrub VM died. Read logs for out of disk space / OOM / image error |
| `SUCCEEDED` | `SUCCEEDED` | `FAILED` | Mode 2 — budget/deadline race. The watchdog failed it despite success |
| `SUCCEEDED` | `SUCCEEDED` | `PROCESSING` | Completion signal lost. Event-driven notification didn't land, or poller stopped |
| any | `FAILED` at ~30 min | `FAILED` | Mode 3 — deployed-revision drift. Deployed YAML predates `skip_polling` |
| `RUNNING` | `ACTIVE` | `FAILED` | Mode 2 — watchdog fired while the job was still legitimately running |

## Failure modes and fixes

### Mode 1a: Disk out of space (most common)

**Signature.** Batch `FAILED`; scrub logs show `No space left on device`, usually during `gunzip`/decompression or the scrub write.

**Why.** `resource_calculator.py` sizes the boot disk from the *estimated*
decompressed size (compressed size × a gz ratio, × a 2.5 peak multiplier, +
16 GB fixed overhead, floor 150 GB). If a file's true compression ratio exceeds
the estimate, the disk overflows. There is a documented precedent: an 8 GB gz
input estimated at ~29 GB decompressed actually expanded to ~54 GB (~6.5x) and
overflowed a ~130 GB disk.

**Fix now.** Re-run the upload; the size estimate is per-file and a retry alone
won't help if the ratio is genuinely high. To force more disk, re-launch the
workflow with an explicit larger `disk_size_gb` argument, or bump the fixed
overhead / ratio assumptions in `resource_calculator.py` if you're seeing a
class of files exceed the model. Prefer over-provisioning disk (cheap, prorated)
over tuning tight.

### Mode 1b: Memory or CPU

**Signature.** Batch `FAILED`; logs show OOM-kill or the process killed by the
kernel with no out of disk space error.

**Why.** Defaults are `cpu_milli: 8000` (8 vCPU) and `memory_mib: 15360`
(~15 GB), overridable per file. `aligns_to` memory-maps the filter DB rather
than loading it wholesale, so pure OOM is uncommon. When it happens it's usually
an unusually large or unusual-content FASTQ.

**Fix.** Re-launch with a larger `memory_mib`. If a whole class of inputs needs
more, raise the memory the calculator emits.

### Mode 1c: Image or entrypoint error

**Signature.** Batch `FAILED` almost immediately; logs show image pull failure,
`scrub.sh: not found`, a bad flag, or a missing mount.

**Why.** A bad scrubber image tag, a broken `scrub.sh`, or a
`SRA_SCRUBBER_WORKFLOW_ID` pointing at the wrong workflow. Note the fail-closed
guard: if the configured workflow id contains `parallel`
(`PARALLEL_WORKFLOW_MARKER`), the trigger refuses to run, because the parallel
scatter/gather workflow is still draft and carries placeholder values.

**Fix.** Confirm the deployed image tag and the workflow id. Confirm the
`ingest`/`staging` bucket mounts exist and the service account can read/write
them.

### Mode 2: Budget/deadline race (workflow succeeded, file FAILED)

**Signature.** Table row `SUCCEEDED / SUCCEEDED / FAILED` or
`RUNNING / ACTIVE / FAILED`. The scrubbed output exists in the output bucket but
the file is `FAILED`.

**Why.** The file's `processing_deadline` (or the poller's retry budget) was
shorter than the actual scrub time. `estimate_scrubber_poll_minutes` scales the
budget by file size, but a slow or oversized file can still exceed it, and
`cleanup_stuck_files` then flips it. The `SRA_SCRUBBER_POLL_MAX_RETRIES` default
(120 × 60s = 2h) is far shorter than the workflow's 24h ceiling, so on the
polling path a legitimately long scrub can outrun the poller.

**Fix now.** The scrub output is good, so recover the file rather than
re-scrubbing: promote it manually if metadata is complete, or re-point it at the
scrubbed object. **Fix the class:** widen the poll budget
(`SRA_SCRUBBER_POLL_MAX_RETRIES` / `SRA_SCRUBBER_POLL_INTERVAL`) and/or the
`processing_deadline` buffer so it tracks the 24h workflow ceiling for large
files. This is the highest-value structural fix if you see mode 2 repeatedly.

### Mode 3: Deployed-revision drift

**Signature.** Workflow fails at ~30 min with a connector/polling timeout rather
than a Batch failure.

**Why.** The **deployed** workflow is an older revision that still used the
connector's blocking poll (default ~30 min) instead of `skip_polling: true` +
manual poll.

**Fix.** Diff deployed vs repo and redeploy:

```
gcloud workflows describe sra-human-scrubber-<env> \
  --project=<lab_project> --location=<location> --format='value(revisionId, updateTime)'
gcloud workflows describe sra-human-scrubber-<env> \
  --project=<lab_project> --location=<location> --format='value(sourceContents)' \
  | grep -A2 connector_params
```

If `skip_polling: true` is absent, redeploy the current YAML.

### Completion signal lost (file stuck PROCESSING after SUCCEEDED)

**Signature.** Batch and workflow `SUCCEEDED`, file still `PROCESSING`.

**Why.** On the event-driven path the completion notification didn't reach the
handler (Pub/Sub delivery, or `_handle_scrubber_completion_notification` skipped
it for a missing/unrecognized `uploadId`/`status`). On the polling path the
poller stopped early.

**Fix.** Check the ingest worker logs for the completion handler warning
("missing uploadId/status" or "unrecognized status"). Re-emit the completion or
promote the file manually. The watchdog will otherwise sweep it to `FAILED` at
its deadline, converting this into mode 2.

## Configuration reference

All are Django settings (env-driven). Verify against the running pod, not the
repo defaults.

| Setting | Purpose |
| --- | --- |
| `ENABLE_SRA_SCRUBBER_WORKFLOW` | Master on/off for scrubbing |
| `INGEST_EVENT_DRIVEN_SCRUBBER` | `True` = Pub/Sub completion; `False` = Celery polling |
| `SRA_SCRUBBER_WORKFLOW_ID` | Deployed workflow name. Must not contain `parallel` |
| `SRA_SCRUBBER_WORKFLOW_LOCATION` | Workflow/Batch region |
| `SRA_SCRUBBER_WORKFLOW_PROJECT_ID` | Falls back to `HOST_GCP_PROJECT_ID` |
| `SRA_SCRUBBER_WORKFLOW_OUTPUT_BUCKET` | Scrubbed-FASTQ landing bucket |
| `SRA_SCRUBBER_POLL_INTERVAL` | Seconds between polls (polling path) |
| `SRA_SCRUBBER_POLL_MAX_RETRIES` | Poll retry ceiling. Default 2h — shorter than the 24h workflow |
| `STUCK_FILE_CLEANUP_HOURS` | Fallback age for files with no `processing_deadline` |

## Known sharp edges

- **The poll ceiling (2h) is much shorter than the workflow ceiling (24h).** Any
  scrub that legitimately runs longer than the file's scaled budget becomes a
  mode-2 false failure. If large files are common, this is the first thing to
  widen.
- **Disk estimation is a heuristic.** Unusual compression ratios cause out of disk space errors.
  The calculator errs toward over-provisioning on purpose; if you tighten it,
  you will trade cost for mode-1a failures.
- **The parallel workflow is draft.** Never point `SRA_SCRUBBER_WORKFLOW_ID` at
  it; the fail-closed guard will refuse, which itself surfaces as an immediate
  trigger failure.
- **Verify deployed vs repo after any workflow change.** Mode 3 only exists
  because the two can drift.
