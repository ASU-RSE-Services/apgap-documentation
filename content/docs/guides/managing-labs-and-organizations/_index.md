+++
title = 'Managing Labs and Organizations'
date = 2026-04-07t07:07:07+01:00
weight = 2
bookCollapseSection = true
+++

Organizations are the top-level container in APGAP. Each institution — ASU, ADHS, University of Arizona, NAU, TGen, or a partner agency — should be an Organization. Labs belong to Organizations.

Labs belong to Organizations. Creating a lab triggers a Cloud Build pipeline that provisions a dedicated Google Cloud project for the lab — including a GCS bucket, IAM configuration, and VPC. **This process takes 5 to 15 minutes**.

Every lab must have at least one Lab Director before users can be assigned or files can be uploaded.

