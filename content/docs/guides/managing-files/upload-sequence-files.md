+++
title = 'Upload a sequence file'
date = 2026-04-07t07:07:07+01:00
weight = 1
+++


# Upload Sequence Files

There are several ways to upload sequence files into APGAP. 

> [!WARNING]
**The permissions required for this operation are Lab Director, Lab Collaborator, or Bioinformatics User**

## Upload a sequence file (GUI)
GUI upload is the simplest way to get files into APGAP. Use this for single files or small batches where you want to upload through your browser.
1. Click **Labs** in the sidebar
1. Select your lab
1. Click the **Sequences** tab
1. Click **Upload** **Sequences** → **GUI** **Upload**
1. In the dialog that appears, drag and drop your file(s) into the designated box or click the **Choose a file** link to browse for files
1. Click **Upload** **Files** to confirm. Your file(s) will show a status of **PROCESSING** for about 5-15 minutes (depending on file size) while APGAP scans and registers them. When complete, the status will update to **DRAFT**.

![Sequences Page](/images/sequences.png)


## Upload sequence files in bulk (Batch)
Batch upload is designed for large volumes of files. Instead of uploading through your browser, APGAP creates a secure Google Cloud Storage endpoint that you transfer files to directly.

**Step 1** — **Create a batch endpoint**:
1. Click **Labs** → select your lab → **Sequences** tab
1. Click **Upload** **Sequences** → **Command Line (Batch)**
1. Select the **Lifetime** **Duration** for the endpoint (how long the upload window stays open)
1. Enter a description to identify this batch
1. Click **Confirm**

APGAP will generate a GCS bucket URI, a JSON key for authentication, and an upload script template. You'll see the endpoint details in the **Batch** **Endpoints** tab.

**Step 2** — Use the script:

1. Copy and paste the **Service Key**, **URI**, and script template to your desktop machine
1. Edit the script to assign the **Service Key** and the **URI** to the KEY_FILE and GCS_BUCKET variables, respectively
1. Edit the LOCAL_FILE variable to assign the pathname of the file(s) you want to upload. Wildcards (\*) and globs (\*.fastq) are permitted to match and upload multiple files.
1. Run the bash script. It uses the Google Cloud CLI gsutil command to upload the files, so that must be installed prior to running the script.
1. Your file(s) will show a status of **PROCESSING** for about 5-15 minutes (depending on file size)  while APGAP scans and registers them. When complete, the status will update to **DRAFT**.

Service keys and URIs for all batch endpoints are listed under the **Batch Endpoint** tab. They may be used until they expire.
