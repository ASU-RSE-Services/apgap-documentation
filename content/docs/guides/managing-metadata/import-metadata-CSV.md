+++
title = 'Import a Completed Metadata CSV'
date = 2026-04-07t07:07:07+01:00
weight = 3
+++

# Import a completed metadata CSV
1. Click **Labs** → select your lab → **Sequences** tab
1. Click **Upload a preformatted CSV** (or navigate to **Metadata** → **Import** **CSV** depending on your version)
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

