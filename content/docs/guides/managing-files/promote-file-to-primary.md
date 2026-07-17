+++
title = 'Promote a File to PRIMARY'
date = 2026-07-09T07:07:07+01:00
weight = 4
+++

# Complete metadata and promote a file to PRIMARY

A file stays in **DRAFT** status until it has all of its required metadata. Promoting it to **PRIMARY** status makes it visible in the Data Catalog and available to be copied into project datasets for analysis, pending any necessary access approvals. This guide covers how to complete the metadata and promote the file to **PRIMARY**, and what to do when promotion is blocked.

> [!WARNING]
**The Permissions required for this operation are Lab Director or Bioinformatics User**

1. Click **Labs** → your lab → **Sequences** tab
1. Click the filename or the pencil icon of a **DRAFT** file
1. Fill in any field marked with a red asterisk. Missing required fields are also listed in the panel as *"Required: [field] is required."*
1. Click **Save**
1. Click **Promote to Primary**

If everything checks out, the file advances to **PRIMARY** and you'll see a *"Sequence promoted to primary successfully"* confirmation.

### If the file won't promote

Setting a file to PRIMARY runs the full metadata validation engine. Promotion is blocked for one of these reasons:

| Reason | What to do |
| --- | --- |
| **Missing required metadata** | Fill in the fields listed in the panel, click **Save**, then try again |
| **Validation errors** | A field's value breaks a rule (for example, a bad date or an out-of-range value). Correct the value and save |
| **Validation warnings** | Review each warning and acknowledge it to continue. Acknowledgements are tied to the exact value — if you change the value later, you'll be asked to review again |
| **Duplicate filename** | Another file in your lab already uses this filename. Rename one of them; the filename check runs before the metadata check |
| **A pending option request** | The value you need is still awaiting review. The file cannot be promoted until the request is approved (see *Request a New Metadata Option*) |

Only files in **DRAFT** can be promoted. You can preview validation at any time without changing the file's status — the panel shows the same errors and warnings you'd get from **Promote to Primary**.
