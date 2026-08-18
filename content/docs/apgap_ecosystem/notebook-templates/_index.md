+++
title = 'Notebook templates'
weight = 7
date = 2026-08-18
bookCollapseSection = true
+++

# Notebook templates

APGAP ships seven Jupyter notebooks pre-loaded into every project's Vertex AI Workbench. They cover the common paths a new user takes: getting oriented, reading data, running pipelines, pulling from external repositories, and looking up file formats and tool concepts.

The notebooks live at `/home/jupyter/apgap-notebooks/notebooks/` on the Workbench. Open a notebook in JupyterLab and pick the **Python 3 (Local)** kernel from the launcher. Avoid the PyTorch, TensorFlow, and ipykernel variants; they are missing libraries these notebooks need. If you're new to the Workbench itself, start with the [Vertex AI Workbench primer](../vertex/).

## At a glance

| # | Notebook | What it does | Difficulty | Est. time | Launches |
|---|---|---|---|---|---|
| 01 | `01-getting-started` | Confirms identity, project, bucket access | Beginner | 5 min |  |
| 02 | `02-read-your-data` | Streams FASTQs from GCS, joins metadata | Beginner | 10 min |  |
| 03 | `03-launch-a-pipeline` | Runs nf-core/viralrecon (or any nf-core pipeline) on GCP Batch | Intermediate | 30-45 min | nf-core pipelines |
| 04 | `04-download-from-public-repos` | Pulls sequencing data from NCBI SRA, ENA, GenBank | Beginner | 5-30 min |  |
| 05 | `05-reference` | File-format and tool cheatsheet (markdown-only) | Beginner | 30 min reading |  |
| 06 | `06-launch-tostadas` | Packages a consensus genome for NCBI GenBank submission | Intermediate | 15-30 min dry-run | tostadas |
| 07 | `07-launch-basespace-copy` | Copies FASTQ files from Illumina BaseSpace | Intermediate | Varies with file size | basespace-copy |

## Per-notebook detail

### `01-getting-started`

Your first notebook. Confirms your kernel, GCP identity, and project name, then lists the buckets visible to you. Also previews a sample of your data if a dataset is loaded. If this notebook doesn't run cleanly end-to-end, contact your Platform Admin. Everything downstream depends on the basics working.

**Prereqs:** Portal account, lab membership, project with an active Workbench.

### `02-read-your-data`

Reads FASTQ files directly from your project's analytical-dataset bucket without downloading them locally. Uses `gcsfs` under the hood. Reports per-file stats (read count, total bases), does a paired-end sanity check, and joins to `_apgap_metadata.csv` for a per-sample view.

**Prereqs:** nb 01 completed; at least one dataset loaded in the project.

### `03-launch-a-pipeline`

Installs Nextflow + JDK 17 (both version-pinned), writes a Google Batch configuration for your project, and runs `nf-core/viralrecon` on a small synthetic test dataset. Also includes a samplesheet builder for your own data and a commented-out real-data launch cell.

The launch cells work as a template for any nf-core pipeline. Change `PIPELINE_REPO` and `PIPELINE_REVISION` in the parameter cell and the rest transfers. See the notebook's "How to use this template" section for the switching guide.

**Prereqs:** nb 01 completed; project has a compute environment configured.

### `04-download-from-public-repos`

Pulls sequencing data from NCBI's Sequence Read Archive (SRA), its European mirror (ENA), and reference sequences from GenBank via NCBI's Entrez API. Handles the `sra-tools` install with a version pin (older builds fail TLS handshake against modern NCBI). Cross-checks that SRA and ENA copies of the same accession agree on the underlying reads. They do, though the FASTQ header conventions differ.

**Prereqs:** nb 01 completed; external network access from the Workbench.

### `05-reference`

A markdown-only reference notebook. Covers file formats you'll see across the platform (FASTQ, FASTA, VCF, BAM, GFF, BED), tool concepts (Nextflow, nf-core, GCP Batch), samplesheet conventions, and links out to canonical documentation. No code cells to run; open it, read what you need, keep it in a tab as you work.

**Prereqs:** none.

### `06-launch-tostadas`

Runs CDC's `tostadas` pipeline (the `feature/measles-vadr` branch, forked and patched for GCP Batch compatibility) to package an assembled consensus genome into a submission bundle for NCBI GenBank. Uses VADR for viral annotation and `table2asn` for the final `.sqn` submission file.

Ships with three run modes: (1) dry-run on Glen's pre-loaded measles demo data, (2) dry-run on your own data, and (3) real submission to NCBI. Modes 1 and 2 require no NCBI credentials and produce no upstream artifacts. Mode 3 posts to NCBI's production endpoints. Read the un-comment checklist in the notebook before enabling it.

**Prereqs:** nb 01 completed; consensus FASTA + metadata XLSX in a GCS bucket; NCBI account only for Mode 3.

### `07-launch-basespace-copy`

Copies FASTQ files from Illumina BaseSpace into an APGAP ingest bucket. From the ingest bucket, an APGAP-managed compliance cascade (DLP scan → SRA human-read scrubber) routes the files to your lab's analytical-dataset bucket. This notebook uses local Python + the `bs` CLI + `gsutil`; there is also a parallel Seqera Platform launch path that runs the same copy through a Nextflow pipeline.

**Prereqs:** nb 01 completed; Illumina BaseSpace account with project access; a batch-upload endpoint created via the APGAP Portal.

## Where to find the source

All seven notebooks live in the public repository at [`azpathogens/apgap-notebooks`](https://github.com/azpathogens/apgap-notebooks). The copy on your Workbench is a clone that stays in sync with `main`. If you find a bug or have a suggestion, file an issue there.
