+++
title = 'APGAP How-To Guide'
date = 2026-04-07T07:07:07+01:00
weight = 3
+++

## 5. Managing labs
Labs belong to Organizations. Creating a lab triggers a Cloud Build pipeline that provisions a dedicated Google Cloud project for the lab — including a GCS bucket, IAM configuration, and VPC. **This process takes 5 to 15 minutes**.

Every lab must have at least one Lab Director before users can be assigned or files can be uploaded.

### Create a lab

**Before proceeding**: Ensure the Organization exists and the designated Lab Director's user account has been created.
1. Navigate to **Labs**
1. Click **Create** **Lab**
1. In the dialog:
    - Select the **Organization**
    - Enter the **Lab** **Name** (display name)
    - Add at least one **Lab** **Director**
1. Click **Create** **Lab**

The lab will appear in the roster with **Build Status: WORKING** while infrastructure is being provisioned. When complete, the status changes to **SUCCESS** and the lab can accept file uploads.

**Do not add users or attempt file uploads until the Build Status shows SUCCESS.**

### Monitor lab provisioning
To check provisioning status:
1. In the Labs list, look at the **Build** **Status** column for the lab
1. If you need more detail, check Cloud Build logs in the GCP Console:
- GCP Console → Cloud Build → History
- Filter by trigger: lab-tf-apply
- Find the run associated with your lab's project_prefix

### Edit a lab
Lab names and descriptions can be edited through the UI:
1. Click **Labs** → select the lab
1. Click **Edit**
1. Update the description or other editable fields
1. Click **Save**

The lab's project_prefix (used for GCP resource naming) cannot be changed after creation.

