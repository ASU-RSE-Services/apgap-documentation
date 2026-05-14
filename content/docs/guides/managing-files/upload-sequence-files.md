+++
title = 'Upload a sequence file'
date = 2026-04-07t07:07:07+01:00
weight = 1
+++

> [!WARNING]
**The Permissions required for this operation are Lab Collaborator or Admin**

# Upload Sequence Files

There are several ways of uploading sequence files into APGAP. 

## Upload a sequence file (GUI)
GUI upload is the simplest way to get files into APGAP. Use this for single files or small batches where you want to upload through your browser.
1. Click **Labs** in the sidebar
1. Select your lab
1. Click the **Sequences** tab
1. Click **Upload** **Sequences** → **GUI** **Upload**
1. In the dialog that appears, drag and drop your file(s) or click the upload area to browse
1. Click **Upload** **Files** to confirm
Your file will show a status of **PROCESSING** for about 30–60 seconds while APGAP scans and registers it. When complete, the status will update to **UPLOADED** or **DRAFT**.
**Tip**: You can upload multiple files at once using GUI upload. All files will share the same upload session.

![Sequences Page](/apgap-documentation/images/sequences.png)


## Upload sequence files in bulk (Batch)
Batch upload is designed for large volumes of files. Instead of uploading through your browser, APGAP creates a secure Google Cloud Storage endpoint that you transfer files to directly — useful for automated pipelines or very large files.

**Step 1** — **Create a batch endpoint**:
1. Click **Labs** → select your lab → **Sequences** tab
1. Click **Upload** **Sequences** → **Batch**
1. Select the **Lifetime** **Duration** for the endpoint (how long the upload window stays open)
1. Enter a description to identify this batch
1. Click **Confirm**

APGAP will create a GCS bucket and generate a JSON key for authentication. You'll see the endpoint details in the **Batch** **Endpoints** tab.

**Step 2** — **Use the endpoint**:
1. Click **Labs** → your lab → **Batch** **Endpoints** tab
1. Find your endpoint and download the JSON key
1. Use the Google Cloud CLI or your preferred GCS client to transfer files to the bucket
Files transferred to the batch bucket are automatically ingested into APGAP. They will appear in the Sequences tab as they are processed.
