+++
title = 'Understanding File Statuses'
date = 2026-04-07t07:07:07+01:00
weight = 1
+++

# Check and understand file status

Every file has a status shown in the Sequences tab. Here's what each status means and what to do:

| Status | Meaning | What to do |
| --- | --- | --- |
| **PROCESSING** | File is being scanned and registered | Wait 1–2 minutes and refresh |
| **DRAFT** | File stored; required metadata fields are incomplete | Add metadata via the UI or CSV upload |
| **PRIMARY** | File has complete metadata; available for analysis | Nothing — the file is ready |
| **PII_DETECTED** | File was flagged for potential personal information | Contact your Lab Director immediately |
| **FAILED** | A processing error occurred | Contact your Lab Director |
| **ARCHIVED** | File has been archived to external storage | Contact your Lab Director if you need access |

**A note on DRAFT files**: A file in DRAFT is safely stored — it isn't lost or corrupted. The DRAFT status simply means required metadata fields are incomplete. Once you add the missing metadata, the file will automatically advance to PRIMARY status.
To find all DRAFT files in your lab, use the **Filter** or **Search** options in the Sequences tab to filter by status.

