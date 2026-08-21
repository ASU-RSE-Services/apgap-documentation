+++
title = 'Before you begin'
weight = 6
date = 2026-08-18
+++

# Before you begin

Skim this checklist before your first session on APGAP. Most items are one-time setup handled by someone else on your behalf; a few require accounts on external services. You can come back and fill gaps as you need them.

## APGAP Portal account

You need a login on your organization's APGAP Portal. Portal accounts are created by a **Platform Admin**. Contact them to have one provisioned for you; you cannot self-register.

## Lab membership

Everything on APGAP is scoped to a **lab**. To browse projects, upload sequences, or open a Workbench, you need to be assigned to at least one lab. A **Platform Admin** or a **Lab Director** can add you.

## Project + Vertex AI Workbench access

Individual analyses live inside **projects**, which sit under a lab. Each project is provisioned with its own Google Cloud project and its own Vertex AI Workbench instance.

If your lab has an existing project, you should be able to open its Workbench directly from the Portal. No separate Google Cloud login is required. If the Workbench link fails with a 403 or a blank page, contact your Platform Admin; the underlying IAM binding may still be propagating.

For an overview of what the Workbench is and how the notebooks are laid out, see the [Vertex AI Workbench primer](../vertex/).

## Seqera Platform workspace (only if you'll launch pipelines from the Seqera side)

At project creation time, the Portal asks whether to deploy a Seqera Platform workspace alongside the project. If you check that box, the Portal provisions a workspace and a bound compute environment, both scoped to your project. You own them from that point forward.

You need this if you plan to launch pipelines through the Seqera Launchpad. You can skip it for projects that only use notebook-launched Nextflow, or that don't run pipelines at all.

For background on how APGAP and Seqera work together, see the [Seqera Platform primer](../seqera/).

## External accounts (only if your work needs them)

- **NCBI account**: only if you plan a real submission through the tostadas pipeline (dry-runs don't need one). Sign up at https://account.ncbi.nlm.nih.gov.
- **Illumina BaseSpace account with project access**: only if you plan to pull reads from BaseSpace through the basespace-copy pipeline. The account with the source data has to explicitly share the BaseSpace project with your account.
