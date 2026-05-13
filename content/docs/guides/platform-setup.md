+++
title = 'Platform Setup'
date = 2026-04-07T07:07:07+01:00
weight = 14
+++


**The Permissions required for this operation are Admin**

# Initial platform setup
For a new APGAP deployment, complete these steps in order. Skipping steps or doing them out of order will cause errors (for example, you cannot create a lab without first having an organization).

### Recommended setup order

1. Add approved email domains to the domain whitelist
1. Create at least one Organization
1. Create user accounts (domain must be whitelisted first)
1. Create Labs within their Organizations (triggers GCP provisioning)
1. Assign users to Labs
1. Configure metadata templates for each Source Type

Each step is covered in detail in the sections below.
