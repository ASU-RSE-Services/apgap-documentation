+++
title = 'tostadas'
weight = 20
date = 2026-08-21
bookCollapseSection = true
+++

# tostadas

**tostadas** takes a finished viral genome and packages it into a submission-ready bundle for NCBI GenBank. It handles annotation, validation, and file assembly. It does not submit for you unless you explicitly tell it to; the default path stops one step short.

## When to use it

Reach for tostadas when you have a **consensus FASTA** (usually produced upstream by [notebook 03 (viralrecon)](../../notebook-templates/#03-launch-a-pipeline)) and want to prepare it for submission to public databases. Typical use cases:

- Publishing an assembled viral genome to GenBank.
- Producing a dry-run submission package for internal review before a public release.
- Building a repeatable submission workflow that fits inside a bioinformatics pipeline (not one-off manual submissions).

If you only need raw reads (FASTQ) submitted to SRA, or want to update fields on an existing BioSample, tostadas supports those workflows too via the `--workflow biosample_sra` and `--workflow biosample_update` flags. This documentation focuses on the `genbank` workflow, which is the most common path and the one nb 06 (the tostadas launch notebook shipped in the Workbench) demonstrates.

## How it works (customs office analogy)

Think of tostadas as a customs office for genomic data. You hand it a passport (the consensus FASTA) and a form (the metadata spreadsheet). It checks everything, stamps it, and hands back a sealed envelope ready for mailing. The actual mailing to NCBI is a separate step you control.

Under the hood, each run performs:

1. **VADR annotation.** NCBI's own annotation tool identifies gene features on the genome (where each gene starts and stops, what its function is).
2. **table2asn packaging.** NCBI's packager combines the annotated genome with your metadata into an ASN.1 file (`.sqn`), which is what GenBank actually accepts.
3. **Validation.** The pipeline runs the submission through NCBI's rules and flags anything that would be rejected before the file ever leaves your system.

Compute runs on Google Cloud Batch via Nextflow. See the [Seqera Platform primer](../../seqera/) for how Nextflow-on-Batch fits into APGAP.

## Inputs

| Input | Format | Notes |
| --- | --- | --- |
| Consensus FASTA | `.fasta` | The assembled viral genome. Typically comes from viralrecon (notebook 03) but any assembler works. |
| Metadata spreadsheet | `.xlsx` | Filled with NCBI submission fields (BioProject accession, sample IDs, collection info, and so on). The `fasta_path` column points at the FASTA above. |
| NCBI credentials | Username + password | Only required for a real submission. Dry-runs do not need them. Sign up at https://account.ncbi.nlm.nih.gov. |

Deployments usually ship a pre-loaded demo dataset (a consensus genome + a matching metadata spreadsheet with the NCBI fields filled in) so users can run tostadas end-to-end without their own data first. See the tutorial pages for how to point at your deployment's demo dataset.

## Outputs

Every run writes to the `outdir` you set at launch time (typically a dated prefix like `gs://<your-bucket>/tostadas-run-<YYYY-MM-DD>/`). Inside that outdir, the notable artifacts land at:

- `submission_outputs/genbank/batch_1/<sample>/<sample>.sqn`: the validated submission package.
- `vadr/<sample>/<sample>_mev.<ext>`: VADR annotation outputs (`.tbl`, `.alt`, `.mdl`, etc.), one directory per sample.
- `validation_outputs/`, `vadr_clean_outputs/`: supporting reports.

| Output | What it is | Why it matters |
| --- | --- | --- |
| `*.sqn` | The validated submission package. One per sample. | This is the actual file NCBI would accept. Useful for human review before a real submission. |
| VADR reports (`*.vadr.*`) | Annotation output: which gene features were detected. | Lets you confirm the annotation matches what you expect for the organism. |
| Validation summaries | Per-sample reports on whether the metadata and genome pass NCBI's rules. | If a field is missing or malformed, this tells you exactly what to fix. |

## Two ways to launch it

**From Vertex AI Workbench (notebook 06).** Open `06-launch-tostadas.ipynb` in your Workbench. The notebook installs Nextflow, writes a Google Batch config for your project, clones the pipeline, and launches. Best fit when you want the launch flow visible in a notebook alongside your other analytical work.

**From Seqera Platform Launchpad.** Click Launch on your workspace's pre-registered `tostadas-measles-vadr` pipeline row, verify the inherited defaults, set `outdir`, and launch through the Seqera UI. Prefer this when you want a persistent launch definition and centralized run history across the workspace. On APGAP deployments the pipeline is usually pre-registered with all necessary profiles and config baked in; the [Launch from Seqera](tutorial-seqera/) tutorial covers both the everyday launch flow and the admin flow to register it from scratch.

Step-by-step walkthroughs for both paths land in the tutorial pages under this section.

## Parameters

The parameters below are the ones you commonly override at launch time. The pipeline exposes many more; see the [tostadas repo](https://github.com/azpathogens/tostadas) for the full reference.

| Parameter | Default | What it controls |
| --- | --- | --- |
| `--workflow` | `genbank` | Which submission workflow to run. Options: `genbank`, `biosample_sra`, `biosample_update`. |
| `--updated_meta_path` | (required) | GCS URI or local path to the metadata `.xlsx`. |
| `--submission_config` | `tostadas/conf/submission_config.yaml` | Path to the YAML that holds NCBI credentials and organization defaults. |
| `--outdir` | `results/` | Where outputs go. Set this to your Seqera output bucket URI to keep outputs off the Workbench VM disk. |
| `--dry_run` | tostadas default is `false`; nb 06 passes `--dry_run true` explicitly at launch | If `true`, the pipeline validates and packages the genome but never contacts NCBI. Flip to `false` only for a real submission. |
| `--submission` | `false` | If `true` and `--dry_run` is `false`, the pipeline pushes to NCBI's endpoints. |
| `--prod_submission` | `false` | If `true`, uses NCBI's production submission endpoint rather than the test one. |

## Troubleshooting

<details>
<summary>VADR annotation task fails with "Killed" or "found no QC'd data"</summary>

Almost always a container memory limit. VADR's `cmalign` step is memory-hungry against the measles covariance model. If your compute environment allots less than 64 GB to the VADR container, the container gets `SIGKILL`ed by the cgroup and VADR reports its safety-net message about running out of memory. Bump the VADR task's memory allocation. If you are launching from a Workbench notebook, the notebook's `nextflow.config` sets this explicitly; if you have overridden that config, restore the `withName: 'VADR_ANNOTATION' { memory = '64 GB' }` block.
</details>

<details>
<summary>PREP_SUBMISSION or UPDATE_SUBMISSION fails with FileNotFoundError on cloud executors</summary>

Known issue in CDC's upstream `feature/measles-vadr` branch: those two steps thread file paths through a `val`-typed samples map, which means Nextflow does not stage the referenced files into task containers on GCP Batch. The fork at [`azpathogens/tostadas`](https://github.com/azpathogens/tostadas) fixes this by lifting the file inputs into `path`-typed declarations. Point your pipeline at the fork's `feature/measles-vadr` branch. A PR upstream to CDC is tracked at [CDCgov/tostadas#361](https://github.com/CDCgov/tostadas/pull/361).
</details>

<details>
<summary>Dry-run completes but no `.sqn` file appears in the output bucket</summary>

Check that Nextflow exited cleanly (the launch cell should print `Nextflow exited with code 0`). If the pipeline errored earlier, the `.sqn` is not produced. Check `.nextflow.log` for the failing task's work directory and read that task's `.command.log` for the actual error. If the workflow succeeded but no `.sqn` files landed, verify you ran with `--workflow genbank`; the other workflows produce different artifact types.
</details>

<details>
<summary><code>WARN: Failed to publish file: ... CloudStoragePseudoDirectoryException</code> on re-runs</summary>

Known Nextflow + Google Cloud Storage NIO bug: when publishDir tries to publish a DIRECTORY output (like tostadas' `batch_1/` or `AF266288_mev/`) to a target that already exists, it calls `deleteIfExists()` on the target. GCS "directories" are pseudo (just object prefixes), and `deleteIfExists()` throws `CloudStoragePseudoDirectoryException` instead of no-oping. The pipeline still completes with exit 0 and outputs land in the work bucket; only some publishDir copies to the results bucket are skipped.

**Workaround**: use a fresh `outdir` per run (e.g. dated prefix like `tostadas-run-2026-08-26`). First-run publishDir to any fresh target works cleanly. Or `gsutil rm -r <outdir>/<sample>/` before re-launching.

Fires on both notebook launches and Seqera launches. Long-term fix is upstream in Nextflow's `nf-google` plugin; workaround is stable.
</details>

<details>
<summary>Nextflow launch cell in the notebook keeps "running" after the pipeline finishes</summary>

Known interaction: Nextflow's JVM keeps Batch-polling threads alive after the main process exits, which keeps the notebook cell's subprocess pipe open. The launch cell in nb 06 has an auto-terminate that watches `.nextflow.log` for `Execution complete -- Goodbye` and force-terminates the subprocess 3 min after the last stdout line. If that doesn't fire (edge case), interrupt the kernel manually. The outputs are already written; nothing is lost. Same behavior in nb 03.
</details>

<details>
<summary>Nextflow monitor stall: tasks stay "Submitted" forever despite the Batch job succeeding</summary>

Rare on Prod: Nextflow's monitor thread stops picking up state transitions from Batch. Tasks show as `SUBMITTED` in `.nextflow.log` indefinitely even though `gcloud batch jobs describe` shows the job as `SUCCEEDED` and the work dir has `.exitcode` = `0`.

**Diagnostic**: check the work directory for the stuck task:
```bash
gsutil cat gs://<work-bucket>/<hash>*/.exitcode
```

If it prints `0`, the Batch job actually succeeded and Nextflow just missed it.

**Workaround**: interrupt the launch cell, then re-run. Nextflow's `-resume` will see the cached `.exitcode` and skip the "stuck" task. If the stall reproduces, worth checking with your Platform Admin whether `nf-google` plugin needs a bump or whether the workspace's compute env has a batch-polling permissions issue.
</details>

<details>
<summary>Nextflow launch errors with <code>caller does not have permission to act as service account</code></summary>

The notebook's service account can submit Batch jobs but is missing `iam.serviceAccounts.actAs` on itself. Batch requires the caller to be able to `actAs` the SA it's assigning to jobs, even if that's the same SA (self-actAs).

**Fix**: your Platform Admin grants the notebook SA `roles/iam.serviceAccountUser` on itself. On APGAP Prod projects this is usually baked into the Terraform module; if a specific project is missing it, that's a project-level IAM patch.
</details>

<details>
<summary>Nextflow launch errors with <code>storage.objects.get denied</code> on a shared bucket (Seqera path)</summary>

The Seqera compute environment's service account (typically `seqera-sa@<project>`) doesn't have read access to the bucket your metadata or FASTA lives in. Common when the notebook path (using `notebook-sa`) can read a shared cross-project bucket but the Seqera path (using `seqera-sa`) cannot.

**Workaround**: stage a copy of the test data into a bucket the Seqera compute env owns. Typically the workspace's own `seqera-output` bucket, under a `tostadas-test-data/` prefix.

**Longer fix**: your Platform Admin grants the compute env's SA `roles/storage.objectViewer` on the source bucket, ideally baked into the Terraform module.
</details>

<details>
<summary>Seqera launch errors with <code>Cannot find a file system provider for scheme: outdir</code></summary>

The value of the `outdir` field contains a literal `outdir:` prefix, so Nextflow parses `outdir` as a URI scheme and aborts.

**Fix**: edit the `outdir` field in the Run parameters step. Value should be just the `gs://` URL, no prefix. The field label already says "outdir"; the value doesn't need to repeat it.
</details>

<details>
<summary>Seqera launch errors with <code>mol_type: null</code> or another param unexpectedly null</summary>

If you launched via Params file view with a partial YAML, Seqera treats params-not-mentioned as null (overriding the profile defaults). The `measles` profile normally sets these values, but the params file overrides win.

**Fix**: stay on the **Input form view** at the Run parameters step. Only override `outdir`; leave every other field on its inherited default from the pipeline registration + profile. See the [Launch from Seqera](tutorial-seqera/) tutorial for the intended flow.

If it happens on a legitimately-authored full params file (e.g. Admin flow), also include `mol_type: viral cRNA` and `strip_pub_block: true` explicitly.
</details>

<details>
<summary>Vertex kernel picker doesn't show <code>Python 3 (Local)</code></summary>

APGAP Prod Workbench images ship `Python 3 (Local)` + `R (Local)` kernels; the pre-loaded notebooks are written against `Python 3 (Local)` and won't find their dependencies (`gcsfs`, etc.) on other kernels.

Some UAT images have been observed with only `Python 3 (ipykernel)` after Google image updates; that's an image regression on UAT, not the intended state. If you don't see `Python 3 (Local)` on your Workbench, contact your Platform Admin; the standard Prod image ships both.
</details>

<details>
<summary>Notebook cell errors with <code>ModuleNotFoundError: No module named 'gcsfs'</code></summary>

The kernel you selected is missing `gcsfs`. On the standard APGAP Prod Workbench image, this happens if you picked `Python 3 (ipykernel)` instead of `Python 3 (Local)`. The `(Local)` kernel has APGAP's scientific stack preinstalled, `(ipykernel)` doesn't.

**Fix**: switch to `Python 3 (Local)` via **Kernel → Change Kernel** in JupyterLab. If `Python 3 (Local)` is genuinely absent from the picker, see the previous entry.
</details>

<details>
<summary>Real submission cell errors with "NCBI authentication failed"</summary>

Two most likely causes: (1) the credentials in the parameter cell are wrong or expired, or (2) `--prod_submission` was set to `true` but the account is not authorized to write to the production endpoint. Verify your credentials work by logging into https://account.ncbi.nlm.nih.gov. If they do, keep `--prod_submission false` while testing so the pipeline uses the NCBI test endpoint.
</details>

## Source

- Pipeline fork used by APGAP: [`azpathogens/tostadas`](https://github.com/azpathogens/tostadas), branch `feature/measles-vadr`.
- Upstream: [`CDCgov/tostadas`](https://github.com/CDCgov/tostadas).
- Related notebook: [notebook 06 (`06-launch-tostadas`)](../../notebook-templates/#06-launch-tostadas).
- Upstream consensus source: [notebook 03 (`03-launch-a-pipeline`)](../../notebook-templates/#03-launch-a-pipeline) runs viralrecon and produces the consensus FASTA that tostadas takes as input.
