+++
title = 'APGAP How-To Guide'
date = 2026-04-07T07:07:07+01:00
weight = 2
+++

# APGAP How-To Guide

This guide covers common tasks in APGAP, organized by role. Use the table of contents to jump to what you need.

## All Users

### Log in and navigate the platform
1. Navigate to https://apgap.prod.rtd.asu.edu
1. Click **Sign in with Google** and select your institutional account
1. After login you'll land on the Dashboard

The left sidebar is your main navigation. What you see depends on your role — Platform Admins see an **Admin** section that other users don't.

To log out, click your name or avatar in the top right corner and select **Log out.**

### Update your notification preferences
APGAP sends in-app notifications (the bell icon in the top navigation bar) for events like access request decisions, file status changes, and lab updates. You can control whether these also generate emails.

1. Click your name or avatar in the top right → **Profile** → **Notifications**
1. Toggle email notifications on or off for each event type
1. Click **Save**

In-app notifications cannot be disabled — only email delivery can be turned off. The notification types available to you depend on your role.

## Lab Collaborators and Lab Readers

### Upload a sequence file (GUI)
GUI upload is the simplest way to get files into APGAP. Use this for single files or small batches where you want to upload through your browser.
1. Click **Labs** in the sidebar
1. Select your lab
1. Click the **Sequences** tab
1. Click **Upload** **Sequences** → **GUI** **Upload**
1. In the dialog that appears, drag and drop your file(s) or click the upload area to browse
1. Click **Upload** **Files** to confirm
Your file will show a status of **PROCESSING** for about 30–60 seconds while APGAP scans and registers it. When complete, the status will update to **UPLOADED** or **DRAFT**.
**Tip**: You can upload multiple files at once using GUI upload. All files will share the same upload session.

### Upload sequence files in bulk (Batch)
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

### Download a metadata CSV template
Metadata tells APGAP what each sequence file represents — the organism, collection date, location, and other scientific details. APGAP uses Source Types (Human, Wastewater, Wildlife, Animal/Livestock, etc.) to determine which fields are required.

1. Click **Labs** → select your lab → **Sequences** tab
1. Click **Download CSV Template**
1. Select your **Source** **Type** from the dropdown
1. Click **Download**

The CSV will download to your browser's default download folder. It's strongly recommended to rename the file before filling it in so you can track which batch it belongs to.

Understanding the CSV template:
- **Row 1** — Column headers (field names). Do not modify these.
- **Row 2** — Shows REQUIRED or OPTIONAL for each field. Do not modify this row.
- **Rows 3 onwards** — Add one row per file

The first column is always filename. This must contain the exact filename as it appears in APGAP, including the file extension and matching case.

**Note**: If your Platform Admin has added custom metadata fields via Metadata Management, those fields will appear in the template automatically.

### Fill in a metadata CSV
Once you've downloaded a template, fill it in following these rules:

#### Filename column (required for every row)
The filename must exactly match the file's name as it appears in the Sequences tab. Check for:
- Correct capitalization (APGAP is case-sensitive here)
- No extra spaces before or after the filename
- The correct file extension (e.g., .fastq, .fastq.gz, .fasta)

#### Required fields

Fields marked REQUIRED in Row 2 must have a value for the file to advance from DRAFT to PRIMARY status. Leaving a required field blank will cause that row to remain in DRAFT.

#### Controlled vocabulary fields

Some fields accept only values from a defined list — for example, organism names must match the values configured in your platform's Metadata Management settings. If you enter a value that doesn't match, the row will not be processed.

Common controlled vocabulary fields include:
- Organism / Pathogen Name
- Biospecimen Type
- Source Type
- Sequencing Platform

#### Date fields

Dates should be entered in YYYY-MM-DD format (e.g., 2026-03-15). Other formats may be accepted but can cause inconsistencies — stick to the ISO format to be safe.

#### Numeric fields

Fields like Age or Ct Value should contain numbers only (e.g., 35, not thirty-five or N/A). If a value is not available, leave the field blank rather than entering a placeholder.

#### After filling in the CSV:

Save the file in CSV format (not XLSX). If you opened it in Excel or Numbers, be sure to save as CSV before importing.

### Import a completed metadata CSV
1. Click **Labs** → select your lab → **Sequences** tab
1. Click **Upload** **CSV** (or navigate to **Metadata** → **Import** **CSV** depending on your version)
1. Select your **Source** **Type** from the dropdown — this must match the Source Type you used when downloading the template
1. Click Choose File and select your completed CSV
1. Click **Import**

The import results will show:
- How many rows were successfully processed
- Any rows that had errors, with a description of what went wrong

**Errors are row-specific** — a problem in one row does not prevent other rows from being processed. Fix the errors in your CSV and re-import. Existing metadata is not overwritten unless you provide a new value for the same field.
Common import errors:

| Error message | Cause | Fix |
| --- | --- | --- |
| "File not found" | Filename in CSV doesn't match APGAP | Check spelling, capitalization, and extension |
| "Invalid value for [field]" | Value not in controlled vocabulary | Check Metadata Management for valid values |
| "Required field missing" | A REQUIRED field has no value | Fill in the missing value |
| "Invalid date format" | Date not in YYYY-MM-DD format | Reformat dates |


### Add or edit metadata for a single file

For one-off updates to individual files, you can edit metadata directly in the UI without using a CSV.

1. Click **Labs** → your lab → **Sequences** tab
1. Click the filename of the file you want to update
1. Click **Add Metadata** (if no metadata exists) or **Edit Metadata**
1. Fill in or update the fields
1. Click **Save**

Fields marked with a red asterisk are required for PRIMARY status. You can save partial metadata — the file will remain in DRAFT until all required fields are complete.

### Check and understand file status

Every file has a status shown in the Sequences tab. Here's what each status means and what to do:

| Status | Meaning | What to do |
| --- | --- | --- |
| **PROCESSING** | File is being scanned and registered | Wait 1–2 minutes and refresh |
| **UPLOADED** | File stored successfully; no metadata required for this file type | Nothing — the file is ready |
| **DRAFT** | File stored; required metadata fields are incomplete | Add metadata via the UI or CSV upload |
| **PRIMARY** | File has complete metadata; available for analysis | Nothing — the file is ready |
| **PII_DETECTED** | File was flagged for potential personal information | Contact your Lab Director immediately |
| **FAILED** | A processing error occurred | Contact your Lab Director |
| **ARCHIVED** | File has been archived to external storage | Contact your Lab Director if you need access |

**A note on DRAFT files**: A file in DRAFT is safely stored — it isn't lost or corrupted. The DRAFT status simply means required metadata fields are incomplete. Once you add the missing metadata, the file will automatically advance to PRIMARY status.
To find all DRAFT files in your lab, use the **Filter** or **Search** options in the Sequences tab to filter by status.

### Request access to another lab's files

If you need data from another lab for your analysis, you can request access through the Data Catalog.

1. Click **Data** **Catalog** in the sidebar
1. Browse or search for the Analytical Dataset containing the files you need
1. Click **Request** **Access**
1. Select the specific files you need from the dataset
1. Add a justification explaining why you need access
1. Click Submit

The Lab Director who owns those files will receive a notification. You'll be notified by email and in-app when they make a decision. If approved, the files will appear in your project for download and analysis.


**Don't see the dataset you need?** The researcher who owns the data may need to create an Analytical Dataset first. Contact them directly or ask your Lab Director.

### Archive a file
Files in APGAP cannot be permanently deleted without going through an Archive Request. This ensures an auditable record of why files were removed.

1. Navigate to the file you want to archive in the Sequences tab
1. Click the file name to open the file detail view
1. Click **Request** **Archive**
1. Select the archive reason:
- **NCBI** **SRA** — the file has been deposited to NCBI SRA; enter the accession number
- **Other** — provide the external storage location and a justification
1. Click **Submit**

Your Lab Director will receive a notification and must approve the request before the file is archived. You'll be notified when a decision is made.

## Lab Directors

### Add a user to your lab

New users must already have an account in APGAP before you can add them to your lab. If a user doesn't appear in the search, ask your Platform Admin to create their account first.
You can add users from two places — from the Lab Roster or from the User Management section. From the Lab Roster:

1. Click Labs → select your lab → Lab Roster tab
1. Click Assign User
1. Search for the user by email address
1. Select their Role:
- **Lab** **Director** — full lab admin, can manage roster and approve access requests
- **Lab** **Collaborator** — read/write access, no admin capabilities
- **Lab** **Reader** — read-only access
1. Click **Assign** **User**

The user will receive a notification that they've been added to the lab.

**Note**: Platform Admins and Data Analysts cannot be added to labs — they have platform-wide access and are excluded from lab-level membership by design.

### Change a user's role in your lab
A user can only have one role per lab. To change their role, you need to remove them and re-add them with the new role.
1. Click **Labs** → your lab → **Lab** **Roster** tab
1. Find the user and click the trash can icon next to their name
1. Confirm the removal
1. Click **Assign** **User** and add them back with the new role

### Remove a user from your lab
1. Click **Labs** → your lab → **Lab** **Roster** tab
1. Find the user you want to remove
1. Click the trash can icon next to their name
1. A confirmation dialog will appear — click **Yes**, **Remove**
Removing a user from the lab removes their access to all files and projects in the lab. Their account is not deleted from the platform.

### Create a project
Projects live inside labs and provide an isolated workspace for a specific analysis effort. Each project can have its own set of Bioinformatics Users and its own Seqera pipeline workspace.
1. Click **Labs** → your lab → **Projects** tab
1. Click **Add** **Project**
1. Fill in the **Project** **Title** — note that project titles cannot contain spaces
1. Add a description
1. Click **Create** **Project**
**Note**: Creating a project triggers provisioning of a Seqera workspace in the background. This takes a few minutes. The Pipelines section of the project will be available once provisioning is complete.

### Assign a user to a project
Only users who are already members of your lab can be assigned to projects within that lab. Bioinformatics Users are typically assigned at the project level rather than the lab level.
1. Click **Labs** → your lab → **Projects** tab → select the project
1. Click the **Users** tab
1. Click **Assign** **User**
1. Select the user and their permissions
1. Click **Assign** **User**

### Approve or deny an access request
When a researcher from another lab requests access to files in your lab, you'll receive a notification (the bell icon in the top right).
1. Click the notification to go directly to the request, or navigate to **Labs** → your lab → **Access** **Requests**
1. Review the request: who is asking, which files they need, and their stated justification
1. For each file listed in the request, click **Approve** or **Deny**
1. If denying a file, enter a reason (required)
1. Click **Submit** **Decision**

The researcher will be notified of your decision immediately.

**Auto-approval**: If your organization has the auto-approval setting enabled, access requests from users within the same organization are approved automatically. You can review and revoke access later if needed.

### Review files in DRAFT status
Files stuck in DRAFT are one of the most common lab management tasks. To find them:
1. Click Labs → your lab → Sequences tab
1. Use the Filter or Search options to filter by Status = DRAFT

For each DRAFT file, you can:
- Click the filename to see which specific metadata fields are missing
- Download a pre-filled CSV of all DRAFT files, complete what's missing, and import it back

To export a CSV of all DRAFT files in your lab, click **Download** **CSV** **Template** and select the relevant Source Type. Any files that already have partial metadata will have those values pre-populated in the export.

If you have a large DRAFT backlog and the metadata cannot be collected, contact your Platform Admin — they have the ability to bulk-promote files to PRIMARY status when a data quality trade-off is justified.

## Bioinformatics Users

### Create an Analytical Dataset

An Analytical Dataset is a named, curated collection of files that you want to analyze together. Files can come from any lab you have access to. The dataset is the unit of access — if you need files from another lab, you request access at the dataset level.

1. Navigate to **Labs** → your lab → your **Project**
1. Click **Datasets** → **New** **Dataset**
1. Enter a **Name** and **Description** for the dataset
1. Click **Save**

The dataset is created empty. Add files in the next step.

### Add files to an Analytical Dataset
1. Open your Analytical Dataset
1. Click **Add** **Files**
1. Search for files by lab, source type, organism, or filename
1. Select the files you want to include
1. Click **Add** **to** **Dataset**

If any files you selected belong to labs you don't already have access to, the dataset status will show **PENDING** until the relevant Lab Directors have approved your access requests. You'll be notified as each decision is made.
Files from labs you already have access to are added immediately.

### Request access to files from another lab

If your Analytical Dataset includes files from labs you don't have access to, APGAP will prompt you to submit access requests.

1. Open your Analytical Dataset
1. Click **Request** **Access** (visible if there are files awaiting approval)
1. Review the files listed and add a justification explaining your research need
1. Click **Submit**
Each Lab Director whose files are included will receive a notification. You can track the status of each request within the dataset view. When all requests are resolved, the dataset becomes available for analysis.

### Launch a pipeline

Pipeline launching uses the Seqera Platform integration. Your project must have a provisioned Seqera workspace before pipelines are available.

1. Navigate to **Labs** → your lab → your **Project**
1. Click **Pipelines** → **Launch**
1. Select a pipeline from the Seqera launchpad
1. Configure the pipeline parameters as needed
1. Select your Analytical Dataset as the input
1. Click **Launch**

Pipeline execution is tracked in Seqera Platform. When the pipeline completes, result files will appear in your project's file list.

**Pipelines section is empty?** The Seqera workspace for this project may not have been provisioned yet, or provisioning may have failed. Contact your Platform Admin.
