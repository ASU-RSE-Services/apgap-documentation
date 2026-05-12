+++
title = 'Platform Admin'
date = 2026-04-07T07:07:07+01:00
weight = 3
+++

# Platform Admin access and constraints
Platform Admin status is controlled by the is_platform_admin boolean flag on a user's account. When this flag is set, the user is automatically added to the Platform Admin Django permission group, which governs all API-level access checks.

**Important constraints for Platform Admin accounts:**

- Platform Admins **cannot be added to individual Labs or Projects**. The system enforces this — if a user needs both admin access and lab membership, use the Data Analyst role instead (platform-wide analytics access, no lab membership restrictions).
- Platform Admin and Data Analyst flags are mutually exclusive in practice — a user should have one or the other, not both.
- Changes to the is_platform_admin flag take effect immediately. The user does not need to log out and back in.

To set or remove Platform Admin status for a user, navigate to **Admin** → **User** **Management**, find the user, click the edit icon, and toggle the **Platform Admin** switch.
