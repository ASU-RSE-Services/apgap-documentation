+++
title = 'Managing the Domain Whitelist'
date = 2026-04-07T07:07:07+01:00
weight = 11
+++


# Managing the domain whitelist
The domain whitelist controls what email domains are permitted to have accounts in APGAP. A user account cannot be created unless their email domain is on the whitelist. Currently only Google-based domains can be used.

**Important**: Removing a domain from the whitelist does not deactivate existing users from that domain. Existing accounts remain active and can still log in. The whitelist is only checked when creating new accounts.
> [!WARNING]
**The permissions required for this operation are Platform Admin**

### Add a domain

Navigate to **Administration→ Domain Whitelist** 
1. Enter the domain name including the extension (e.g., asu.edu, tgen.org, nau.edu) in the **Add Allowed Domain** field
1. Optionally add a domain description
1. Click **Add Domain** 

### Edit a domain
1. Navigate to **Administraion → Domain Whitelist** 
1. Find the domain and click the edit icon (pencil)
1. Make your changes
1. Click the checkmark to save, or the X to cancel

### Delete a domain
1. Navigate to Administration → Domain Whitelist
1. Find the domain and click the delete icon (trash can)
1. Confirm the deletion

