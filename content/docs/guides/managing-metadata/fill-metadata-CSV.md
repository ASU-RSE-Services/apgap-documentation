+++
title = 'Fill a Metadata CSV'
date = 2026-04-07t07:07:07+01:00
weight = 3
+++


# Fill in a metadata CSV

> [!WARNING]
**The Permissions required for this operation are Lab Collaborator or Admin**

Once you've downloaded a template, fill it in following these rules:

### Filename column (required for every row)
The filename must exactly match the file's name as it appears in the Sequences tab. Check for:
- Correct capitalization (APGAP is case-sensitive here)
- No extra spaces before or after the filename
- The correct file extension (e.g., .fastq, .fastq.gz, .fasta)

### Required fields

Fields marked REQUIRED in Row 2 must have a value for the file to advance from DRAFT to PRIMARY status. Leaving a required field blank will cause that row to remain in DRAFT.

### Controlled vocabulary fields

Some fields accept only values from a defined list — for example, organism names must match the values configured in your platform's Metadata Management settings. If you enter a value that doesn't match, the row will not be processed.

Common controlled vocabulary fields include:
- Organism / Pathogen Name
- Biospecimen Type
- Source Type
- Sequencing Platform

### Date fields

Dates should be entered in YYYY-MM-DD format (e.g., 2026-03-15). Other formats may be accepted but can cause inconsistencies — stick to the ISO format to be safe.

### Numeric fields

Fields like Age or Ct Value should contain numbers only (e.g., 35, not thirty-five or N/A). If a value is not available, leave the field blank rather than entering a placeholder.

### After filling in the CSV:

Save the file in CSV format (not XLSX). If you opened it in Excel or Numbers, be sure to save as CSV before importing.

