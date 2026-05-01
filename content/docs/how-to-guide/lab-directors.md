+++
title = 'Lab Directors'
date = 2026-04-07T07:07:07+01:00
weight = 3
+++

# Lab Directors

## Add a user to your lab

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

![Lab Roster](/images/lab-roster.png)

The user will receive a notification that they've been added to the lab.

**Note**: Platform Admins and Data Analysts cannot be added to labs — they have platform-wide access and are excluded from lab-level membership by design.

## Change a user's role in your lab
A user can only have one role per lab. To change their role, you need to remove them and re-add them with the new role.
1. Click **Labs** → your lab → **Lab** **Roster** tab
1. Find the user and click the trash can icon next to their name
1. Confirm the removal
1. Click **Assign** **User** and add them back with the new role

## Remove a user from your lab
1. Click **Labs** → your lab → **Lab** **Roster** tab
1. Find the user you want to remove
1. Click the trash can icon next to their name
1. A confirmation dialog will appear — click **Yes**, **Remove**
Removing a user from the lab removes their access to all files and projects in the lab. Their account is not deleted from the platform.

## Create a project
Projects live inside labs and provide an isolated workspace for a specific analysis effort. Each project can have its own set of Bioinformatics Users and its own Seqera pipeline workspace.
1. Click **Labs** → your lab → **Projects** tab
1. Click **Add** **Project**
1. Fill in the **Project** **Title** — note that project titles cannot contain spaces
1. Add a description
1. Click **Create** **Project**
**Note**: Creating a project triggers provisioning of a Seqera workspace in the background. This takes a few minutes. The Pipelines section of the project will be available once provisioning is complete.

## Assign a user to a project
Only users who are already members of your lab can be assigned to projects within that lab. Bioinformatics Users are typically assigned at the project level rather than the lab level.
1. Click **Labs** → your lab → **Projects** tab → select the project
1. Click the **Users** tab
1. Click **Assign** **User**
1. Select the user and their permissions
1. Click **Assign** **User**

## Approve or deny an access request
When a researcher from another lab requests access to files in your lab, you'll receive a notification (the bell icon in the top right).
1. Click the notification to go directly to the request, or navigate to **Labs** → your lab → **Access** **Requests**
1. Review the request: who is asking, which files they need, and their stated justification
1. For each file listed in the request, click **Approve** or **Deny**
1. If denying a file, enter a reason (required)
1. Click **Submit** **Decision**

The researcher will be notified of your decision immediately.

**Auto-approval**: If your organization has the auto-approval setting enabled, access requests from users within the same organization are approved automatically. You can review and revoke access later if needed.

## Review files in DRAFT status
Files stuck in DRAFT are one of the most common lab management tasks. To find them:
1. Click Labs → your lab → Sequences tab
1. Use the Filter or Search options to filter by Status = DRAFT

For each DRAFT file, you can:
- Click the filename to see which specific metadata fields are missing
- Download a pre-filled CSV of all DRAFT files, complete what's missing, and import it back

To export a CSV of all DRAFT files in your lab, click **Download** **CSV** **Template** and select the relevant Source Type. Any files that already have partial metadata will have those values pre-populated in the export.

If you have a large DRAFT backlog and the metadata cannot be collected, contact your Platform Admin — they have the ability to bulk-promote files to PRIMARY status when a data quality trade-off is justified.

