+++
title = 'APGAP and Vertex AI Workbench'
date = 2026-04-07T07:07:07+01:00
weight = 5
+++

### Open a Vertex AI Workbench notebook

Each APGAP project has a managed Vertex AI Workbench notebook attached to it, provisioned automatically alongside the project's Seqera Workspace. The notebook is a JupyterLab environment running on Google Cloud, pre-configured to access the files in your project — so you can move from "I have an Analytical Dataset" to "I'm exploring it interactively" without having to set up your own compute.

**To open the notebook:**

1. Navigate to **Labs** → your lab → your **Project**
2. Click the **NOTEBOOK LINK** button inside the Vertex AI Workbench box
3. You'll be redirected to JupyterLab in a new tab, authenticated through your APGAP Google account

The notebook environment is the standard GCP Vertex AI Workbench instance (Python 3, JupyterLab, the usual data science stack — pandas, numpy, scikit-learn, matplotlib, etc.) running on a managed VM in the project's isolated GCP environment. It has network access to the project's GCS bucket, so files in your Analytical Datasets are reachable directly from notebook code without separate authentication.

**R is available in addition to Python.** The default Vertex AI Workbench image only ships with Python; APGAP's notebook environment has been customized to include R as a first-class kernel. You'll see both **Python 3** and **R** options when creating a new notebook from the JupyterLab launcher. This makes it straightforward to run R-based bioinformatics packages (Bioconductor, phylogenetics tooling, statistical analysis) alongside Python workflows in the same project.

> **Notebook section is empty or the button is missing?** The Vertex AI Workbench instance is provisioned by the same Cloud Build pipeline that creates the Seqera Workspace when a project is created. If provisioning didn't complete successfully, the notebook won't be available. Contact your Platform Admin — they have a recovery procedure for re-triggering project provisioning.

> **A note on cost and idle instances:** Vertex AI Workbench notebooks are billed for as long as the underlying VM is running, regardless of whether you're actively using JupyterLab. 
