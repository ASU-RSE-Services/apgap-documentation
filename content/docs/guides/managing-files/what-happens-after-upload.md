+++
title = 'What Happens After You Upload a File'
date = 2026-07-09T07:07:07+01:00
weight = 2
+++

# What happens after you upload a file

When you upload a sequence file, it doesn't appear in your lab immediately. APGAP runs it through a short processing pipeline first, which is why a new file shows **PROCESSING** for several minutes before it becomes usable. This page explains what that pipeline does and what to do when a file doesn't come out the other side cleanly.

### The processing pipeline

1. **Registration.** The file first goes into temporary storage for processing, is recorded in your lab and marked **PROCESSING**.
1. **PII scan.** Google Cloud DLP scans the file for personal information. If it finds any, the file is set to **PII_DETECTED** and held.
1. **Human read scrubbing.** For FASTQ files, APGAP runs the SRA human read scrubber, which removes human sequence reads before the file is stored. This step loads a large reference database, so it takes longer for FASTQ files than for other file types. Non-FASTQ files skip this step.
1. **Metadata check.** APGAP checks the file's metadata against the requirements for its Source Type. The file lands in **DRAFT** if required fields are missing, or **PRIMARY** if everything is present.

Processing usually takes 5–15 minutes depending on file size. Larger FASTQ files take longer because of the scrubbing step. The status updates on its own; refresh the **Sequences** tab to see the current state.

### When a file doesn't process cleanly

| Status | What it means | What to do |
| --- | --- | --- |
| **PII_DETECTED** | The file was flagged as possibly containing personal information | Do not attempt to re-upload it as-is. Contact your Lab Director, who can review the flag and decide how to proceed |
| **FAILED** | A step in the pipeline did not complete in time, or errored | Delete the file and upload it again. If it fails a second time, contact your Lab Director with the filename |
| **Stuck in PROCESSING** for much longer than expected | A very large file may still be scrubbing legitimately | Give it more time. APGAP automatically moves a file to **FAILED** once it passes its expected processing window, and notifies you — so a genuinely stuck file will not sit in PROCESSING forever |

A file only reaches **DRAFT** or **PRIMARY** after it has cleared the PII scan and (for FASTQ) the scrubber. A file you can see and edit has already been through those checks.
