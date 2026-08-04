+++
title = 'Redact Metadata'
date = 2026-04-07t07:07:07+01:00
weight = 4
+++

# Redact Specific Metadata Elements

If some of the required metadata elements associated with your sequence file are considered sensitive, and should not be exposed in the data catalog, you have the option to redact those specific elements. 

Redacted metadata elements remain visible to members or the Lab, and will become visible any user you have approved to access the sequence file. 

> [!WARNING]
**The Permissions required for this operation are Lab Director, Lab Collaborator, Bioinformatics User, or Platform Admin**


1. Open the metadata editor, either by
  
    a. check boxes next to DRAFT or PRIMARY files and click the button "Upload or Edit Metadata" and either upload metadata (or upload corrected metadata) or "go straight to the editor"
  
    b. in the blade out view for a single file, click the link "Redact metadata (open editor)" 

2. Once you're in the editor, you will see the option to "redact all" under neath the column header. Clicking this will redact that metadata element for all of the files listed down the rows. 

3. Click save.

In the Data Catalog, users who are members of your lab will see "redacted" next to the metadata you have hidden. Users whose access requests you have approved will see this metadata element in their analytical datasets. Other users won't see that field name or the value at all, and it won't come up in a search of the Data Catalog for that specific tag that has been "redacted."
