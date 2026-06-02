+++
title = 'Admin User Management'
date = 2026-04-07t07:07:07+01:00
weight = 1
+++


# Admin User Management
APGAP does not support self-signup. All user accounts must be created by a Platform Admin.

> [!WARNING]
**The Permissions required for this operation are Admin**

### Create a user
Before proceeding: Ensure the user's email domain is on the domain whitelist.
1. Navigate to **Administration → User Management** 
1. Click **Add** **User**
1. Fill in:
    - **Full Name** — first and last name required
    - **Email** **address** — must match the Google account they'll use to log in
    - **Organization** — the organization this user belongs to
1. Set role flags if applicable:
    - **Platform** **Admin** — grants full platform access; user cannot be added to labs
    - **Select a Lab** — grants platform-wide analytics access; user cannot be added to labs
    - **Lab Director** — grants highest level of permissions in a lab
1. Click **Add** **User**

The user will receive a welcome notification. They log in by navigating to the APGAP URL and clicking **Sign** **in** **with** **Google** using the email address you registered.

If you see "Domain not authorised" when creating a user, that means the email domain has not been added to the whitelist yet. Add the email domain (see "Managing the domain whitelist") .

### Edit a user
1. Navigate to **Administration → User Management** 
1. Find the user and click the **Edit** (pencil) icon in their Profile Details
1. Make your changes and click **Save**

Changing a user's email address is possible but requires that the new address matches an active Google account. The user will need to use the new email to log in after the change.

### Deactivate or delete a user
User deactivation is handled through the Django admin panel (/admin/). In the main platform UI you can remove a user from all their labs, which effectively revokes their access to lab data, but their account remains active.
For full deactivation, use the Django admin panel:
- /admin/users/user/ → find the user → uncheck **Active** → save
