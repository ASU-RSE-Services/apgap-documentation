+++
title = 'Delete an Organization'
date = 2026-04-07t07:07:07+01:00
weight = 2
+++

> [!WARNING]
**The Permissions required for this operation are Admin**

# Delete an organization
⚠️ Deleting an organization is not reversible through the UI. All associated labs and data will remain in the database but the organization will be hidden from all views.
1. Navigate to **Admin → Organizations** 
1. Find the organization and click the **Delete** icon
1. In the confirmation dialog, click **Yes, delete** 

Organizations are soft-deleted (active = False) — the underlying data is preserved but the organization is invisible to users.

