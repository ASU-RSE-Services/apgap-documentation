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

If you only need raw reads (FASTQ) submitted to SRA, or want to update fields on an existing BioSample, tostadas supports those workflows too via the `--workflow biosample_and_sra` and `--workflow biosample_update` flags. This documentation focuses on the `genbank` workflow, which is the most common path and the one the shipped notebook demonstrates.

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

Every run writes to the project's Seqera output bucket, under a `tostadas-*-results/` prefix.

| Output | What it is | Why it matters |
| --- | --- | --- |
| `*.sqn` | The validated submission package. One per sample. | This is the actual file NCBI would accept. Useful for human review before a real submission. |
| VADR reports (`*.vadr.*`) | Annotation output: which gene features were detected. | Lets you confirm the annotation matches what you expect for the organism. |
| Validation summaries | Per-sample reports on whether the metadata and genome pass NCBI's rules. | If a field is missing or malformed, this tells you exactly what to fix. |

## Two ways to launch it

**From Vertex AI Workbench (notebook 06).** Open `06-launch-tostadas.ipynb` in your Workbench. The notebook installs Nextflow, writes a Google Batch config for your project, clones the pipeline, and launches. Best fit when you want the launch flow visible in a notebook alongside your other analytical work.

**From Seqera Platform Launchpad.** Register the pipeline on your workspace's Launchpad, fill in the parameters, and launch through the Seqera UI. Best fit when you want a persistent launch definition and centralized run history across the workspace.

Step-by-step walkthroughs for both paths land in the tutorial pages under this section.

## Parameters

The parameters below are the ones you commonly override at launch time. The pipeline exposes many more; see the [tostadas repo](https://github.com/azpathogens/tostadas) for the full reference.

| Parameter | Default | What it controls |
| --- | --- | --- |
| `--workflow` | `genbank` | Which submission workflow to run. Options: `genbank`, `biosample_and_sra`, `biosample_update`. |
| `--updated_meta_path` | (required) | GCS URI or local path to the metadata `.xlsx`. |
| `--submission_config` | `tostadas/conf/submission_config.yaml` | Path to the YAML that holds NCBI credentials and organization defaults. |
| `--outdir` | `results/` | Where outputs go. Set this to your Seqera output bucket URI to keep outputs off the Workbench VM disk. |
| `--dry_run` | `true` (via `test` profile) | If `true`, the pipeline validates and packages the genome but never contacts NCBI. Flip to `false` for a real submission. |
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

Check the workflow completed cleanly (Nextflow prints a summary line at the end and writes a `pipeline_info/` directory next to your outputs). If the pipeline errored earlier, the `.sqn` is not produced. Check `.nextflow.log` for the failing task's work directory and read that task's `.command.log` for the actual error. If the workflow succeeded but no `.sqn`, verify you ran with `--workflow genbank`; the other workflows produce different artifact types.
</details>

<details>
<summary>Nextflow launch cell in the notebook keeps "running" after the pipeline finishes</summary>

Known interaction: Nextflow's JVM keeps Batch-polling threads alive after the main process exits, which keeps the notebook cell's subprocess pipe open. If the Nextflow log shows "Pipeline completed successfully" but the cell has not returned control after several minutes, interrupt the kernel and continue to the next cell. The outputs are already written; nothing is lost.
</details>

<details>
<summary>Real submission cell errors with "NCBI authentication failed"</summary>

Two most likely causes: (1) the credentials in the parameter cell are wrong or expired, or (2) `--prod_submission` was set to `true` but the account is not authorized to write to the production endpoint. Verify your credentials work by logging into https://account.ncbi.nlm.nih.gov. If they do, keep `--prod_submission = false` while testing so the pipeline uses the NCBI test endpoint.
</details>

## Source

- Pipeline fork used by APGAP: [`azpathogens/tostadas`](https://github.com/azpathogens/tostadas), branch `feature/measles-vadr`.
- Upstream: [`CDCgov/tostadas`](https://github.com/CDCgov/tostadas).
- Related notebook: [notebook 06 (`06-launch-tostadas`)](../../notebook-templates/#06-launch-tostadas).
- Upstream consensus source: [notebook 03 (`03-launch-a-pipeline`)](../../notebook-templates/#03-launch-a-pipeline) runs viralrecon and produces the consensus FASTA that tostadas takes as input.
