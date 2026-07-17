+++
title = 'Request a New Metadata Option'
date = 2026-07-09T07:07:07+01:00
weight = 6
+++

# Request a new metadata option

Some metadata fields (SELECT fields) only accept values from a controlled list. If the value you need isn't in the list, you can request that it be added instead of leaving the field blank. A Platform Admin reviews the request, and once it's approved the value becomes available to every lab.

> [!WARNING]
**The Permissions required for this operation are Lab Director or Bioinformatics User**

There are two ways to do this - through the metadata panel (for a single file) or the metadata editor (multiple files).

#### Requesting new values through the metadata panel 
1. Click **Labs** → your lab → **Sequences** tab
1. Open the file (filename or pencil icon) and go to the SELECT field you want to set
1. If your value isn't listed, click **Request a new option**
1. Enter the **Proposed value**
1. Optionally add a **Justification** — this is shown to the reviewer and helps them decide
1. Leave **Auto-promote my file to Primary once approved** checked if you want the file advanced to PRIMARY automatically on approval. If you uncheck it, the file stays in DRAFT after approval and you can promote it yourself.
1. Click **Submit**

When you type a proposed value, APGAP suggests any existing options that look similar (including known aliases). Pick a suggestion if one matches — it avoids creating a duplicate value.

### While the request is pending

The request is attached to your file. **A file with a pending option request cannot be promoted to PRIMARY** until the request is confirmed. You'll be notified by email and in-app when a reviewer approves or rejects it. On approval the new value is added to the field and, if you opted in, your file is promoted automatically.

You can cancel a request you submitted if you no longer need the value.
