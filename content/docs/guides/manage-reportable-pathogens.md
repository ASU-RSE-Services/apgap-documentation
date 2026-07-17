+++
title = 'Manage Reportable Pathogens'
date = 2026-07-09T07:07:07+01:00
weight = 15
+++

# Register pathogens and mark them reportable

The Pathogens list is the platform's registry of known pathogens and which of them are **reportable**. Marking a pathogen reportable matters beyond record-keeping: it drives metadata validation (fields that are only required for reportable pathogens) and access-request auto-approval, when your organization has that setting enabled. *Note:* Novel or emerging pathogens are also maintained in this list. Reportable status is a legal designation and follows applicable state rules.

> [!WARNING]
**The Permissions required for this operation are Platform Admin**

### Register a pathogen

1. Navigate to **Administration → Pathogens**
1. Under **Name**, choose from the list of unregistered pathogen names. These come from existing pathogen metadata values that haven't been registered yet.
1. Optionally set a **Category** and **Description**
1. Set **Reportable** to **Yes** if the pathogen is reportable
1. Click **Add**

If the pathogen you need isn't in the Name list, add it as a metadata option first, then come back and register it. For many pathogens at once, use the **Upload CSV** option.

### Change or remove a pathogen

A pathogen's name is tied to its linked metadata values, so you can't rename it directly. Create a new metadata option with the desired name and register that instead. 

Toggling a pathogen's **Reportable** setting takes effect immediately and changes how validation and auto-approval treat files tagged with that pathogen.
