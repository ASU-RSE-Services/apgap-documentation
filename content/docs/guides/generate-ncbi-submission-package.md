+++
title = 'Generate an NCBI Submission Package'
date = 2026-07-09T07:07:07+01:00
weight = 10
+++

# Generate an NCBI submission package with TOSTADAS

APGAP includes a notebook that runs CDC's **TOSTADAS** pipeline to prepare pathogen sequence data for submission to NCBI. It validates your metadata, annotates a consensus genome (using VADR), and produces a submission package for the NCBI repositories (BioSample, SRA, and GenBank). This is how you go from an approved consensus genome to a standards-compliant submission without assembling the submission by hand.

> [!WARNING]
**The permissions required for this operation are Lab Director or Bioinformatics User**

### You will need

- A **project** with its Vertex AI Workbench notebook provisioned (see *APGAP and Vertex AI Workbench*).
- A consensus genome and its metadata available to the project.
- **NCBI submission credentials** (an NCBI username and password) if you intend to actually submit. You can validate and annotate without submitting.

### Run the pipeline

1. Open your project's Vertex AI Workbench notebook
1. Open **`06-launch-tostadas.ipynb`**
1. When prompted to **Select Kernel**, choose **Python 3 (Local)**
1. Run **Kernel → Restart & Run All**

The notebook installs Nextflow, writes a GCP Batch configuration, and runs TOSTADAS end to end on the measles VADR branch. It validates your metadata, annotates the consensus genome, and assembles the NCBI submission outputs.

> **Validate first, submit second.** TOSTADAS supports a dry run so you can confirm the metadata and annotation are correct before anything is sent to NCBI. Only provide your NCBI credentials and turn off the dry run once the validation output looks right.

### What you get

The pipeline produces a submission package in the results directory, including the VADR-annotated genome and the metadata files formatted for NCBI. GenBank submission uses an updated metadata file that the BioSample/SRA step generates for you, so run those steps first. You can also fetch and parse the report files from a previous submission to retrieve assigned accession numbers.

TOSTADAS is pathogen-agnostic. The notebook ships configured for measles, but the same mechanics apply to other pathogens by changing the species and submission settings in the pipeline configuration.
