+++
title = 'Download Public Sequence Data'
date = 2026-07-09T07:07:07+01:00
weight = 14
+++

# Download public sequence data from NCBI, ENA, and GenBank

APGAP includes a notebook for pulling sequencing data from the public repositories — NCBI SRA, ENA, and GenBank — directly into your project. Use this when you want to bring in reference genomes, comparison datasets, or externally sequenced samples alongside your own data.

> [!WARNING]
**The permissions required for this operation are Lab Director or Bioinformatics User**

### You will need

- A **project** with its Vertex AI Workbench notebook provisioned (see *APGAP and Vertex AI Workbench*).
- The accession numbers or run IDs of the data you want to download.

### Run the notebook

1. Open your project's Vertex AI Workbench notebook
1. Open **`04-download-from-public-repos.ipynb`**
1. When prompted to **Select Kernel**, choose **Python 3 (Local)**
1. Edit the **ACCESSION** and **OUTPUT_DIR** variables in the first cell of the notebook
1. Run **Kernel → Restart & Run All**

The notebook installs `sra-tools` and Biopython, then fetches the requested data. It includes a sanity check that compares the SRA and ENA copies of the same run, so you can confirm you pulled the data you expected before using it.

Downloaded files land in your project's environment, reachable from the other notebooks and from your pipeline runs. From there you can treat them like any other data in the project.
