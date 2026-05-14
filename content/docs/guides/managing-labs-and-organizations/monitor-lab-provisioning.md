+++
title = 'Monitor Lab Provisioning'
date = 2026-04-07t07:07:07+01:00
weight = 5
+++


# Monitor lab provisioning
> [!WARNING]
**The Permissions required for this operation are Admin**
To check provisioning status:
1. In the Labs list, look at the **Build** **Status** column for the lab
1. If you need more detail, check Cloud Build logs in the GCP Console:
- GCP Console → Cloud Build → History
- Filter by trigger: lab-tf-apply
- Find the run associated with your lab's project_prefix
