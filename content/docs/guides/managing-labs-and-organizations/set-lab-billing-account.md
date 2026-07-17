+++
title = "Set a Lab's Billing Account"
date = 2026-07-09T07:07:07+01:00
weight = 16
+++

# Set or change a lab's GCP billing account

Each lab's Google Cloud resources are billed to a GCP billing account. You set this when you create a lab, and you can change it later from the lab's settings. The billing account is optional at creation but must be set before the lab's cloud resources will run.

> [!WARNING]
**The Permissions required for this operation are Platform Admin**

1. Navigate to **Administration** → **Labs & Projects** → select the lab 
1. Enter or update the **GCP billing account** field
1. Click **Save**

Changing the billing account triggers a targeted infrastructure update that re-applies the lab's billing configuration in Google Cloud. Give it a few minutes to complete. You can confirm the lab's current value in the labs administration table, which lists the **GCP Billing ID** for each lab.

Use a billing account your organization actually owns and has enabled. An invalid or disabled billing account will cause the lab's provisioning to fail.
