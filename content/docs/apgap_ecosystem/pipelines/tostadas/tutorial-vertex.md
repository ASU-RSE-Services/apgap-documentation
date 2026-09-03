+++
title = 'Launch from Vertex AI Workbench'
weight = 10
date = 2026-08-21
+++

# Launch tostadas from Vertex AI Workbench

This walkthrough runs the tostadas pipeline end-to-end from [notebook 06](../../../notebook-templates/#06-launch-tostadas) on your Vertex AI Workbench. The default path is a dry-run on pre-loaded demo data. No NCBI credentials required.

## Prerequisites

Before starting, confirm the items on the shared [Before you begin](../../../prerequisites/) checklist. Beyond those, this tutorial specifically needs:

- **A Workbench that opens cleanly and can list your project's buckets.** If notebook 01 runs without errors, you are in good shape.
- **Input data**: either the deployment's pre-loaded measles demo data (whatever `TEST_BUCKET` and `METADATA_FILE` default to in the notebook's parameter cell) or your own consensus FASTA + metadata `.xlsx` in a GCS bucket you can read from the Workbench.
- **NCBI account only if you plan a real submission.** The dry-run needs nothing.

If your deployment ships pre-loaded demo data, its location is already set as the notebook's default `TEST_BUCKET` and `METADATA_FILE`. If it does not, ask your Platform Admin where a demo dataset lives, or point those parameters at your own data.

## Opening notebook 06

1. In the APGAP Portal, open your project's page and click **NOTEBOOK LINK** in the Vertex AI Workbench card. JupyterLab opens in a new browser tab, authenticated as the `jupyter` user on your project's Workbench.

![APGAP Portal project page showing the Vertex AI Workbench card with the NOTEBOOK LINK button visible](/images/shared/portal-project-notebook-link-button.png)

2. In the JupyterLab file browser (left sidebar), navigate to `apgap-notebooks/notebooks/` and double-click `06-launch-tostadas.ipynb`.

![JupyterLab file browser open at /home/jupyter/apgap-notebooks/notebooks/ with 06-launch-tostadas.ipynb highlighted](/images/shared/jupyterlab-filebrowser-nb06.png)

3. When JupyterLab prompts you to select a kernel, pick **`Python 3 (Local)`** under "Start python Kernel". Do not pick `Python 3 (ipykernel)`, `PyTorch`, or `TensorFlow` if you see them; those environments are missing libraries this notebook needs and will fail on the first GCS read with `ModuleNotFoundError: No module named 'gcsfs'`. If a kernel is already selected in the top-right of the notebook and it says `Python 3 (Local)`, you are set.

![JupyterLab Select Kernel dialog open with the dropdown expanded, showing Python 3 (Local) highlighted under Start python Kernel](/images/shared/jupyterlab-kernel-selector.png)

## Parameter setup

The parameter cell is the first code cell in the notebook. It has four blocks with header comments explaining each:

| Block | What to do |
| --- | --- |
| 1. Auto-detected | Leave blank. The next cell fills these in from your Workbench environment (project ID, region, buckets, VPC). Override only if auto-detect picks something wrong. |
| 2. NCBI credentials | Blank for a dry-run. Fill only if you plan a real submission later. |
| 3. Test data | Defaults point at the deployment's pre-loaded demo data. Change `TEST_BUCKET` and `METADATA_FILE` to point at your own inputs when you are ready. When bringing your own metadata, edit the `fasta_path` column in the spreadsheet to point at a `gs://` URI, not a local path. |
| 4. Pipeline source | Points at the APGAP-supported fork and branch. Leave alone unless debugging. |

Run the parameter cell and the next cell (environment auto-detect). The auto-detect cell should print your project ID, region, output bucket, and detected VPC and subnetwork. That output is the signal that everything downstream will work.

![Tail of the auto-detect cell's helper function above its printed output, which reports GCP project, region, identity, auto-selected RESULTS_BUCKET and WORK_BUCKET, and the detected network and subnetwork](/images/tostadas/vertex-01-param-cell-postdetect.png)

## Running the pipeline

1. In the JupyterLab menu, click **Kernel → Restart Kernel and Run All Cells**. This runs every cell in sequence.
2. The install and config cells take about 1-3 minutes cold, or near-zero if you ran notebook 03 in the same session (both notebooks share the same Nextflow install, so nb 06 skips it if it is already there).
3. The dry-run cell (`-preview`) parses the pipeline and config without launching any Batch tasks; it should complete in about 10 seconds and print the resolved parameters.

![Dry-run cell output showing the tostadas pipeline resolved with parameters and no task submissions](/images/tostadas/vertex-02-dry-run-output.png)

4. The launch cell submits each pipeline step as a GCP Batch task and streams progress line-by-line. Expected wall-clock:
   - **First run cold:** 15-30 minutes. Most of the time is Batch scheduling VMs and pulling the VADR container image (large, around 1 GB).
   - **Subsequent runs with `-resume`:** 5-10 minutes. Cached tasks skip.

You will see 8 `Submitted process` lines as tasks start on Batch. If a task gets Spot-preempted mid-run, Nextflow's `errorStrategy = retry` transparently re-submits it and you see a `NOTE: Process ... terminated for an unknown reason -- ... Execution is retried (1)` line followed by a fresh `Re-submitted process`. That is normal and the pipeline recovers.

![Launch cell in progress: several Submitted process lines visible in the notebook output](/images/tostadas/vertex-03-launch-in-progress.png)

## What success looks like

The launch cell ends with `Nextflow exited with code 0`. That is the definitive success signal. Above it you will see the 8 Submitted-process lines but no fancy stats block. The launch is configured for line-per-event output, which suits the notebook better than Nextflow's redraw-in-place table, so the exit line is what to look for.

![Launch cell output showing the 8 Submitted process lines followed by Nextflow exited with code 0](/images/tostadas/vertex-04-pipeline-success.png)

If the cell keeps showing "running" for several minutes after the final task, interrupt the kernel and continue. Nextflow's JVM sometimes keeps background threads alive after the main process has finished; the outputs are already written. The notebook has an auto-terminate that catches this on most images, but if it fails to fire, manual interrupt is safe. See the troubleshooting section on the [tostadas overview](../) for details.

You may also see `WARN: Failed to publish file: ... CloudStoragePseudoDirectoryException`. This is a known Nextflow-GCS bug that fires when re-running to an `--outdir` that already contains outputs from a previous run. The pipeline still succeeds (all `.exitcode` values are `0`); only some `publishDir` copies to the results bucket are skipped. If you want a clean run without the WARN, `gsutil rm -r` the target prefix before launching.

The output-listing cell near the bottom of the notebook enumerates what landed in the results bucket. You should see one or more `.sqn` files under `submission_outputs/genbank/batch_1/<sample>/` and VADR annotation outputs under `vadr/<sample>/`.

![Output-listing cell showing .sqn files and VADR annotation outputs under the tostadas-test-results/ path](/images/tostadas/vertex-05-output-listing.png)

## Verification checklist

<details>
<summary>Click through these before considering the run complete</summary>

- [ ] Launch cell printed `Nextflow exited with code 0`.
- [ ] At least one `.sqn` file appears in the output bucket under the outdir you set (defaults to `tostadas-test-results/` if you did not override it).
- [ ] VADR annotation outputs (files under a `vadr/` prefix) show up in the listing cell with a count greater than zero.
- [ ] No red error tracebacks in the launch cell output.
- [ ] Any `WARN: Failed to publish file` messages are only about directory-typed outputs on a re-run (the known GCS-NIO bug), not about individual `.sqn` or metadata files.
- [ ] If you plan a real submission next, review at least one `.sqn` file (via the NCBI submission portal or a local tool like `asn2gb`) before uncommenting the real-submission cell.

</details>

## Where to go next

- Back to the [tostadas overview](../) for parameters, troubleshooting, and the customs office analogy.
- For the equivalent flow through the Seqera Launchpad instead of a notebook, see [Launch tostadas from Seqera](../tutorial-seqera/).
- For the upstream step that produces the consensus FASTA tostadas takes as input, see [notebook 03](../../../notebook-templates/#03-launch-a-pipeline).
- To move from dry-run to real NCBI submission, follow the "Real submission" section in notebook 06 itself. Read the un-comment checklist at the top of that section before enabling it; a real submission produces a permanent public record.
