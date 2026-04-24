+++
title = 'Bioinformatics Users'
date = 2026-04-07T07:07:07+01:00
weight = 4
+++

# Bioinformatics Users

## Create an Analytical Dataset

An Analytical Dataset is a named, curated collection of files that you want to analyze together. Files can come from any lab you have access to. The dataset is the unit of access — if you need files from another lab, you request access at the dataset level.

1. Navigate to **Labs** → your lab → your **Project**
1. Click **Datasets** → **New** **Dataset**
1. Enter a **Name** and **Description** for the dataset
1. Click **Save**

The dataset is created empty. Add files in the next step.

## Add files to an Analytical Dataset
1. Open your Analytical Dataset
1. Click **Add** **Files**
1. Search for files by lab, source type, organism, or filename
1. Select the files you want to include
1. Click **Add** **to** **Dataset**

If any files you selected belong to labs you don't already have access to, the dataset status will show **PENDING** until the relevant Lab Directors have approved your access requests. You'll be notified as each decision is made.
Files from labs you already have access to are added immediately.

## Request access to files from another lab

If your Analytical Dataset includes files from labs you don't have access to, APGAP will prompt you to submit access requests.

1. Open your Analytical Dataset
1. Click **Request** **Access** (visible if there are files awaiting approval)
1. Review the files listed and add a justification explaining your research need
1. Click **Submit**
Each Lab Director whose files are included will receive a notification. You can track the status of each request within the dataset view. When all requests are resolved, the dataset becomes available for analysis.

## Launch a pipeline

Pipeline launching uses the Seqera Platform integration. Your project must have a provisioned Seqera workspace before pipelines are available.

1. Navigate to **Labs** → your lab → your **Project**
1. Click **Pipelines** → **Launch**
1. Select a pipeline from the Seqera launchpad
1. Configure the pipeline parameters as needed
1. Select your Analytical Dataset as the input
1. Click **Launch**

Pipeline execution is tracked in Seqera Platform. When the pipeline completes, result files will appear in your project's file list.

**Pipelines section is empty?** The Seqera workspace for this project may not have been provisioned yet, or provisioning may have failed. Contact your Platform Admin.
