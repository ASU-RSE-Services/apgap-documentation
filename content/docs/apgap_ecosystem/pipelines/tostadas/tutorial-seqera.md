+++
title = 'Launch from Seqera Platform'
weight = 20
date = 2026-08-21
+++

# Launch tostadas from Seqera Platform

This walkthrough runs tostadas from your project's Seqera Launchpad. On most APGAP deployments the pipeline is already registered as `tostadas-measles-vadr`; if it is not, a separate admin-flow section at the end covers first-time registration. Best fit when you want a persistent launch definition, a centralized run history, or when several people on the same lab will launch runs.

## Prerequisites

Before starting, confirm the items on the shared [Before you begin](../../../prerequisites/) checklist. Beyond those, this tutorial specifically needs:

- **A Seqera Platform workspace on your project.** If your project was created with the "Deploy Seqera workspace" toggle enabled at project-creation time, this is already in place. If not, create a new project with the toggle on, or ask your Platform Admin.
- **A bound compute environment on the workspace.** The Portal provisions this alongside the workspace at project creation. You should see it under **Compute Environments** on the workspace.
- **A pre-registered `tostadas-measles-vadr` pipeline on your workspace's Launchpad.** APGAP deployments usually ship this pre-registered; check the Launchpad first. If it's missing, follow the [Registering tostadas from scratch](#registering-tostadas-from-scratch-admin-flow) section below (admin-flow, not needed for most users).
- **Test data staged in a bucket your compute environment's service account can read.** APGAP deployments usually stage a small demo dataset under `gs://<your-workspace-seqera-output-bucket>/tostadas-test-data/`. Cross-project analytical-dataset buckets (the ones the Workbench notebook reads) typically are NOT readable by the compute environment's service account (`seqera-sa@…` on APGAP). Data has to live in a bucket the Seqera compute env's SA owns.
- **NCBI account only if you plan a real submission.** Dry-runs need nothing.

If you want a primer on how the Seqera workspace fits alongside your Vertex Workbench, see the [Seqera Platform primer](../../../seqera/).

## Opening the workspace

From the APGAP Portal, click the **WORKSPACE LINK** in your project's Seqera Workspace card. Seqera Platform opens in a new browser tab, dropped into your workspace's Launchpad.

![APGAP Portal project page showing the Seqera Workspace card with the WORKSPACE LINK button visible](/images/shared/portal-project-workspace-link-button.png)

![Seqera Launchpad view showing registered pipelines including tostadas-measles-vadr, with the Add pipeline button in the top-right for reference](/images/tostadas/seqera-01-add-pipeline-button.png)

If `tostadas-measles-vadr` is already listed, skip to [Launching a run](#launching-a-run). If it is not, jump to [Registering tostadas from scratch](#registering-tostadas-from-scratch-admin-flow) first.

## Launching a run

Because Config profiles, Work directory, and the Nextflow config file are baked into the `tostadas-measles-vadr` registration, launching is mostly verification with one override. Every field described here should be pre-populated; you only need to set `outdir`.

1. On the Launchpad, click **Launch** on the `tostadas-measles-vadr` row. The launch form opens in a four-step stepper.

2. On the **General config** step, verify:
   - **Config profiles** shows `measles,docker` (chips). These come from the registration.
   - **Compute environment** shows your workspace's compute env.
   - **Work directory** shows a `gs://` path in your workspace's seqera-output bucket.

![Seqera launch form General config step with Config profiles measles and docker pre-populated as chips, the project's compute environment pre-selected, and Work directory set to the workspace's seqera-output bucket](/images/tostadas/seqera-03b-general-config-profiles.png)

Click **Next**.

3. On the **Run parameters** step, stay on the default **Input form view**. Do not switch to Params file view; that path overrides the baked-in defaults with null and produces cascading errors. Verify:
   - `workflow` shows `genbank`
   - `meta_path` shows the metadata `.xlsx` staged in your workspace bucket
   - `updated_meta_path` shows the same path
   - `dry_run` toggle is ON (further down the form)

Set `outdir` to a fresh dated path:

```
gs://<your-workspace-seqera-output-bucket>/tostadas-run-<YYYY-MM-DD>
```

Just the URL. **Do NOT prefix it with `outdir:`**. The field label already says "outdir", and typing `outdir: gs://...` inside the value makes Nextflow try to parse `outdir` as a URI scheme and abort with `Cannot find a file system provider for scheme: outdir`.

Increment the date suffix per run (or add a run-number suffix) so each launch writes to a fresh prefix. Same-prefix re-runs trip the GCS pseudo-directory bug in Nextflow's publishDir and produce noisy WARN messages (see [tostadas overview troubleshooting](../)).

![Seqera launch form Run parameters step in Input form view, showing workflow=genbank, meta_path and outdir populated with gs:// paths, with the outdir field containing just a gs:// URL and no outdir: prefix](/images/tostadas/seqera-04-launch-form-params.png)

Click **Next**.

4. On the **Advanced settings** step, verify the **Nextflow config file** field shows the VADR memory override block:

```groovy
process {
    withName: 'VADR_ANNOTATION' {
        cpus   = 4
        memory = '64 GB'
        afterScript = 'find . -name "*.sh.err" -print -exec cat {} \\; 2>&1 || true'
    }
}
```

This is baked into the registration; if it's missing, launches will silently run out of memory on VADR annotation. See the [Registering tostadas from scratch](#registering-tostadas-from-scratch-admin-flow) section for how to add it if it disappeared.

![Seqera launch form Summary view showing the Advanced settings block with the Nextflow config file field containing the VADR_ANNOTATION memory override block](/images/tostadas/seqera-04b-advanced-nextflow-config.png)

Click **Next**.

5. On the **Summary** step, verify pipeline URL (`https://github.com/azpathogens/tostadas`), revision (`feature/measles-vadr`), compute environment, profiles (`measles, docker`), and Work directory. Then scroll to the Run parameters section and verify the params look right.

![Seqera launch form Summary step showing General config section with pipeline URL, revision feature/measles-vadr, config profiles measles and docker, compute environment, and Work directory](/images/tostadas/seqera-05-launch-form-summary.png)

Click **Launch**. Seqera redirects you to the run detail page immediately.

## What success looks like

The run detail page progresses through: `Submitted` → `Running` → `Succeeded`. The progress bar hits 100% and the 8 process rows all turn green. Each task also gets a row in the Tasks table with its own status, container, and native ID.

![Seqera run detail page for a completed tostadas run showing Succeeded state, 100% workflow run progress, 8 succeeded task counter, all 8 process rows with green completion bars, and the Tasks table listing each task with Succeeded status](/images/tostadas/seqera-06-run-detail-succeeded.png)

![Seqera run detail overview showing Succeeded state and 8 process rows all completed for the tostadas GenBank workflow](/images/tostadas/seqera-06b-run-detail-overview.png)

Expected wall-clock: 15-30 min cold on a fresh outdir, 5-10 min when a cached `-resume` hits. Most of the cold time is GCP Batch scheduling VMs and pulling the VADR container (large, ~1 GB).

Outputs land in the `outdir` you set. Navigate to that GCS path (either through the APGAP Portal, the GCP console, or a terminal with `gsutil ls`) to find `.sqn` files and per-sample VADR annotation reports.

## Verification checklist

<details>
<summary>Click through these before considering the run complete</summary>

- [ ] Run detail page shows **Succeeded** state and 100% workflow progress.
- [ ] All 8 process rows are green; Tasks table shows 8/8 Succeeded.
- [ ] No red Failed rows and no Aborted rows.
- [ ] At least one `.sqn` file appears in the `outdir` under `submission_outputs/genbank/`.
- [ ] Per-sample VADR annotation outputs are present under a `vadr/` prefix in the outdir.
- [ ] If you plan a real submission next, review at least one `.sqn` file (via the NCBI submission portal or a local tool like `asn2gb`) before flipping `dry_run` to `false`.

</details>

## Moving from dry-run to a real submission

Same pipeline, different parameters. In a new run, override:

```yaml
dry_run: false
submission: true
prod_submission: false   # keep false until you have run against NCBI's test endpoint successfully
```

You will also need NCBI credentials wired in. The tostadas pipeline reads them from `conf/submission_config.yaml` inside the pipeline repository. Options for injecting them without publishing credentials to a public repo include:

- Fork the tostadas fork into a **private** repo, commit the edited YAML with your credentials, and register a second pipeline on the Launchpad pointing at your private fork.
- Use a Seqera Pipeline Secret to inject the credentials as environment variables and modify the config or launch parameters to read from those.

The exact wiring is deployment-specific; ask your Platform Admin whether one of these patterns is already in use at your deployment before rolling your own.

Flip `prod_submission` to `true` only once a test submission has worked end-to-end and produced the accession you expected. A production submission creates a permanent public record.

## Registering tostadas from scratch (admin flow)

Only needed if `tostadas-measles-vadr` isn't already on your workspace's Launchpad. Most APGAP deployments ship it pre-registered; this section is for the Platform Admin doing initial setup.

Important: Config profiles, Work directory, and the Nextflow config file must be filled in at REGISTRATION time (Advanced options section of the Add Pipeline form), not at launch time. Baking them in lets every subsequent launch use a minimal params form. Setting them only at launch time makes Seqera's params validator override profile defaults with null, and the pipeline errors on missing values like `mol_type` and `strip_pub_block`.

1. On the Launchpad, click **Add pipeline** in the top-right. You land on the New pipeline form.

2. Basic fields:
   - **Name:** `tostadas-measles-vadr`
   - **Description:** e.g. `CDC tostadas fork on the measles VADR branch. Submits assembled measles genomes to NCBI GenBank.`
   - **Compute environment:** your workspace's compute env (usually pre-selected)
   - **Pipeline to launch:** `https://github.com/azpathogens/tostadas`
   - **Revision:** `feature/measles-vadr`
   - **Pull latest:** ON

![Seqera Add pipeline form filled in with Name tostadas-measles-vadr, a short description, the workspace's compute environment, Pipeline to launch pointing at the azpathogens/tostadas repo, Revision feature/measles-vadr, Pull latest toggled on, and Work directory set to the workspace's seqera-output bucket](/images/tostadas/seqera-02-add-pipeline-form-filled.png)

3. Scroll down to **Advanced options** (the critical part):
   - **Work directory:** `gs://<your-workspace-seqera-output-bucket>` (Seqera writes per-task scratch data here)
   - **Config profiles:** `measles,docker` (comma-separated, no spaces)
   - **Nextflow config file:** paste the VADR memory override block:

```groovy
process {
    withName: 'VADR_ANNOTATION' {
        cpus   = 4
        memory = '64 GB'
        afterScript = 'find . -name "*.sh.err" -print -exec cat {} \\; 2>&1 || true'
    }
}
```

4. Set default pipeline parameters (users then only override `outdir` at launch time):

```yaml
workflow: genbank
dry_run: true
meta_path: gs://<your-workspace-seqera-output-bucket>/tostadas-test-data/measles_test_metadata_gs.xlsx
updated_meta_path: gs://<your-workspace-seqera-output-bucket>/tostadas-test-data/measles_test_metadata_gs.xlsx
```

Both `meta_path` and `updated_meta_path` should point at the same test-data spreadsheet.

5. Click **Add**. The pipeline now appears as a row on the Launchpad.

### Staging the test data (once per workspace)

Copy the fork's default test data into your workspace's seqera-output bucket so it's readable by the compute env's service account:

```bash
# From a machine with gsutil configured and the fork cloned
git clone -b feature/measles-vadr https://github.com/azpathogens/tostadas
cd tostadas

# 1. Upload the reference FASTA to the workspace bucket.
gsutil cp assets/sample_fastas/measles/AF266288.fasta \
  gs://<your-workspace-seqera-output-bucket>/tostadas-test-data/AF266288.fasta

# 2. Edit the metadata spreadsheet BEFORE uploading:
#    - Open assets/sample_metadata/measles_test_metadata.xlsx in Excel or LibreOffice.
#    - In the fasta_path column, replace the local path with the gs:// URI
#      of the FASTA you just uploaded, e.g.:
#      gs://<your-workspace-seqera-output-bucket>/tostadas-test-data/AF266288.fasta
#    - Save as measles_test_metadata_gs.xlsx (the _gs suffix marks it as the
#      GCS-friendly variant).

# 3. Upload the edited spreadsheet.
gsutil cp measles_test_metadata_gs.xlsx \
  gs://<your-workspace-seqera-output-bucket>/tostadas-test-data/measles_test_metadata_gs.xlsx
```

Once the pipeline is registered and the data is staged, users can follow the [Launching a run](#launching-a-run) section unchanged.

## Where to go next

- Back to the [tostadas overview](../) for parameters, troubleshooting, and the customs office analogy.
- For the equivalent flow launched from a Jupyter notebook on your Workbench, see [Launch tostadas from Vertex AI Workbench](../tutorial-vertex/).
- For a primer on Seqera Platform generally, including the launchpad concept and per-project workspace model, see the [Seqera Platform primer](../../../seqera/).
- For the upstream step that produces the consensus FASTA tostadas takes as input, see [notebook 03](../../../notebook-templates/#03-launch-a-pipeline).
