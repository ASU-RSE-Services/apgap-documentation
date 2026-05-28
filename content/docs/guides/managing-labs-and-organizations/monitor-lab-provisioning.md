+++
title = 'Monitor Lab Provisioning'
date = 2026-04-07t07:07:07+01:00
weight = 5
+++


# Monitor lab provisioning
> [!WARNING]
> **The Permissions required for this operation are Platform Admin**
>
> To check provisioning status:
1. Using the Labs list view, look at the **Status** column field for the lab
1. If you need more detail, check Cloud Build logs in the GCP Console:
- GCP Console → Cloud Build → History
- Filter by trigger: lab-tf-apply
- Find the run associated with the lab's project_prefix
