+++
title = 'APGAP and Seqera Platform — A Primer'
date = 2026-04-07T07:07:07+01:00
weight = 4
aliases = ['/docs/apgap_ecosystem/sequera/']
+++

# APGAP and Seqera Platform — A Primer

**Arizona Pathogen Genomics Analytics Platform**

---

If you've used APGAP for any length of time, you've probably noticed that the
**Pipelines** section of a project hands off to something called **Seqera
Platform**. This primer explains what Seqera is, why APGAP uses it, and what
you can expect to see when working across the two systems.

This document is intended for technical staff and informed non-technical
stakeholders. It assumes some familiarity with the idea of running
computational pipelines — but no specific Nextflow or genomics tooling
experience is required.

---

## What Seqera Platform is

**Seqera Platform** is a workflow orchestration and management product for
running bioinformatics pipelines at scale. It was previously known as
**Nextflow Tower** and was renamed to Seqera Platform in late 2023.

Seqera is built by the creators of **Nextflow** — the open-source workflow
language that has become a de facto standard for reproducible bioinformatics
analyses. Nextflow describes a pipeline as a graph of containerized steps and
runs that graph across whatever execution environment you point it at: a
single laptop, an HPC cluster, or thousands of cloud VMs. Seqera Platform
sits on top of Nextflow and provides the missing pieces that turn it from a
command-line tool into something an organization can actually operate at
scale:

- A web UI for launching pipelines without writing Nextflow command lines
- Centralized monitoring, logging, and resource usage tracking for every run
- Compute environment management — connecting Nextflow to AWS Batch, Google
  Batch, Kubernetes, Slurm, and similar targets
- A workspace and organization model with role-based access, shared
  credentials, and pipeline catalogs
- Reproducibility features (every run captures its exact configuration,
  container versions, and inputs)

Seqera supports the most widely used bioinformatics pipeline ecosystem
available — the **nf-core** community library — alongside any custom Nextflow
workflows your organization writes.

---

## Why APGAP integrates with Seqera

APGAP is fundamentally a **data management** platform: organize sequencing
files, track metadata, control access, request datasets. Running the actual
analyses — variant calling, assembly, lineage assignment, surveillance
pipelines — is a separate problem with very different operational
characteristics: long-running jobs, large compute clusters, container
management, GPU scheduling, restart-on-failure logic, terabytes of
intermediate files.

Building all of that into APGAP would have meant reinventing what Nextflow
and Seqera already do well. Instead, APGAP delegates pipeline execution to
Seqera and focuses on what it's uniquely positioned to do: structured genomic
data management for the public health context.

The integration follows a clean separation of concerns:

| What APGAP handles | What Seqera handles |
|---|---|
| Sequence file storage and lifecycle | Pipeline execution |
| Metadata and source-type schemas | Compute environment management |
| Organizational hierarchy and RBAC | Workflow run history and logs |
| Analytical Dataset assembly and access requests | Container and dependency management |
| Audit trails for data access | Resource usage tracking |
| Notification system | Pipeline catalog |

When you connect from APGAP to Seqera, APGAP provides your Analytical Dataset
as input to Seqera so that you can run a specific pipeline against it. Seqera does
the actual work and writes results back to a GCS location.

---

## Per-project Seqera workspaces

Seqera Platform uses a hierarchical model of **organizations** containing
**workspaces**. A workspace is the unit of access control and resource
scoping — pipelines, compute environments, credentials, and run history all
live inside a workspace.

APGAP maps **one Seqera workspace per APGAP project**. When you create a
project in APGAP, the platform automatically:

1. Triggers a Cloud Build pipeline that provisions a dedicated GCP
   environment (VPC, NAT, Vertex AI notebook instance, service accounts,
   IAM bindings)
2. Creates a Seqera workspace tied to that GCP environment
3. Configures the workspace with a compute environment pointing at your
   project's GCP resources
4. Stores the workspace's launchpad URL on the APGAP project record

This per-project model means every analysis project has isolated compute,
isolated credentials, and isolated run history. Users assigned to a
project's **Bioinformatics User** role get access to that project's Seqera
workspace; users from other projects do not.

Provisioning typically takes a few minutes — somewhere in the 5 to 15 minute
range, similar to how Lab provisioning works.

---

## The Launchpad and pipeline catalog

Inside each Seqera workspace is a **Launchpad** — Seqera's name for the
catalog of pre-configured pipelines that workspace members can run.

Each Launchpad entry combines three things into a one-click launch experience:

- **A workflow repository** — a Git repository containing the Nextflow
  pipeline code (typically an nf-core pipeline like `nf-core/viralrecon` or
  `nf-core/nascent`, or a custom internal pipeline)
- **A set of default parameters** — pre-filled values for the pipeline's
  required inputs
- **A compute environment** — the cloud or HPC target where the pipeline
  will actually run

Without the Launchpad, a researcher launching a Nextflow pipeline would need
to specify the Git repo, version, container registry, compute target, and
parameter overrides every time. The Launchpad bundles all of that into a
named pipeline that can be launched in two clicks.

For APGAP projects, the Launchpad is typically pre-populated with the
pipelines relevant to your source type — viral surveillance pipelines for
clinical isolates, wastewater pipelines for environmental samples, and so
on. The pipelines available to you depend on what your Platform Admin has
configured for your project.

---

## What you see in APGAP vs. inside Seqera

The integration is designed so that most day-to-day work stays in APGAP,
with Seqera taking over for the parts where it has a better UI or richer
information.

**You'll do this in APGAP:**

- Upload sequence files and add metadata
- Create Analytical Datasets that group files for analysis
  
**You'll typically jump to Seqera for:**

- Lauch pipelines
- Detailed run monitoring while a pipeline is executing
- Process-level logs (each step of the pipeline has its own log)
- Resource usage graphs (CPU, memory, disk, runtime per task)
- Re-running a previous workflow with modified parameters
- Sharing run details with collaborators outside your APGAP project

---

## Provisioning behavior

When you create a new APGAP project, **provisioning the Seqera workspace
takes a few minutes** — typically 5 to 15. 

If the creation of your project fails do the following:

1. Note the project name and approximate time of creation.
2. Contact your Platform Admin and provide them with this information.
3. The Platform Admin can check the Cloud Build logs in the GCP console and try to
   fix the underlying issue.

If Seqera Platform itself is experiencing an outage (rare, but possible),
file uploads and metadata management still work normally — only pipeline
launching is affected. APGAP and Seqera are loosely coupled, so problems on
one side don't take down the other.

---

## Further reading

- **Seqera Platform documentation:** https://docs.seqera.io/
- **Nextflow:** https://www.nextflow.io/
- **nf-core community pipelines:** https://nf-co.re/
