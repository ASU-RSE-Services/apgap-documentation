+++
title = 'APGAP Quickstart Guide'
date = 2026-04-07T07:07:07+01:00
weight = 1
+++

# APGAP Quickstart Guide

Welcome to APGAP! This guide will get you up and running in minutes, regardless of your role. If you run into anything that isn't covered here, reach out to your Lab Director or Platform Admin.

## Before You Begin

You'll need:
An institutional email address from an approved domain (ASU, ADHS, University of Arizona, Northern Arizona University, TGen, or a partner organization)
An account created by a Platform Admin — APGAP does not support self-signup
Access to at least one Lab, assigned by your Lab Director or Platform Admin
If you don't have an account yet, contact your Lab Director or your organization's Platform Admin and provide your institutional email address.

## Step 1 — Log In

Open a browser and navigate to [https://apgap.prod.rtd.asu.edu](https://apgap.prod.rtd.asu.edu)
Click **Sign in with Google**
Select your institutional Google account — this must match the email address registered in APGAP
After a moment you'll be redirected to your dashboard
**Trouble logging in?**
| What you see | What to do |
| --- | --- |
| Domain not authorised | Your email domain hasn't been added to the allowlist. Contact a Platform Admin.|
| Account not enabled | Your account exists but hasn't been activated. Contact your Lab Director or Platform Admin. |
| Blank page or error | Try clearing your browser cache and logging in again. If it persists, contact your Platform Admin. |


## Step 2 — Find Your Way Around
After logging in you'll land on the Dashboard. The left sidebar is your main navigation:
**Labs** — your labs and their sequences, projects, and rosters
**Data Catalog** — browsable view of datasets available for analysis
**Notifications** — the bell icon shows requests, approvals, and system messages
**Admin** — visible to Platform Admins only
Your role determines what you see and can do. Here's a quick summary:
Role
What you can do
**Lab Collaborator
Upload sequences, add metadata, view and download files in your lab
**Lab Reader
View and download files in your lab; no upload or edit access
**Lab Director**
Everything a Collaborator can do, plus manage lab membership, approve access requests, and manage projects
**Bioinformatics User**
Access specific projects, launch pipelines, work with analytical datasets
**Data Analyst**
Platform-wide read access to datasets; cannot be assigned to individual labs
**Platform Admin**
Full access to all organizations, labs, users, and settings

## Step 3 — Your First Task (by Role)

### If you're a Lab Director

Start by making sure your lab is fully set up. Click **Labs** → your lab and check:
- **Lab Roster** — are all your team members added with the right roles?
- **Sequences** — are there any files in DRAFT status that need attention?
- **Projects** — are the right Bioinformatics Users assigned?
To add a team member to your lab:
1. Click **Labs** → your lab → Lab Roster
1. Click **Assign User**
1. Search for the user by email address
1. Select their role and click **Assign User**
If the user isn't found, they haven't been created in the system yet — ask your Platform Admin to create the account first.

### If you're a Bioinformatics User

Your access is project-based. Click **Labs** in the sidebar and navigate to the lab that contains your project. Within the lab, click the **Projects** tab, then select your project.

From your project you can:
- Browse and download files in your project
- Create and manage Analytical Datasets
- Launch pipelines through the Seqera integration (if your project workspace has been provisioned)
**To get started with an Analytical Dataset:**
1. Navigate to your **Project**
1. Click **Datasets** → **New Dataset**
1. Give it a name, add files, and save
If the Pipelines section of your project is empty, the Seqera workspace may not have been set up yet — contact your Platform Admin.

### If you're a Lab Collaborator or Lab Reader

Your primary home is your Lab. Click **Labs** in the sidebar, then click your lab name. You'll see tabs for **Sequences**, **Projects**, **Lab Roster**, and **Batch Endpoints.**

**To upload your first sequence file:**
1. Click **Labs** → select your lab
2. Click the **Sequences** tab
3. Click **Upload Sequences** → **GUI Upload**
4. Drag and drop your file, or click to browse
5. Click **Upload Files**
Your file will move through **PROCESSING** → **UPLOADED** or **DRAFT** within about a minute. If it lands in DRAFT, it needs metadata before it can be used in analysis — see the How-To Guide for instructions.

### If you're a Platform Admin

Your first actions after a fresh deployment should be:
1. **Add your organization's email domain** → Admin → Domain Whitelist → New Domain
1. **Create your organization** → Admin → Organizations → Add New Organization
1. **Create user accounts** → Admin → User Management → Add User
1. **Create labs** → Labs → Create Lab (this triggers a 5–15 minute provisioning process)
1. **Assign users to labs** via User Management or from the Lab Roster tab

The full Admin Guide covers each of these in detail, including recovery procedures for failed lab provisioning.

## Understanding File Statuses
Every file in APGAP has a status. Here's what they mean:
| Status | Meaning | Action needed? |
| --- | --- | --- |
| **PROCESSING** | File is being scanned and registered | Wait 1–2 minutes |
| **UPLOADED** | File stored; no metadata required for this file type | None |
| **DRAFT** | File stored; required metadata fields are incomplete | Add metadata via the UI or CSV upload |
| **PRIMARY** | File has complete metadata; available for analysis | None |
| **PII_DETECTED** | File was flagged for potential personal information | Contact your Lab Director immediately |
| **FAILED** | A processing error occurred | Contact your Lab Director |
| **ARCHIVED** | File has been archived to external storage | None |

The most common status to pay attention to is **DRAFT**. A file in DRAFT has been successfully stored in the platform — it isn't lost — but it needs complete metadata before it can be used in analysis or shared with other researchers.

## Getting Help
- **Lab-level questions** (access, file issues, metadata): contact your **Lab Director**
- **Account or domain issues**: contact your **Platform Admin**
- **Technical platform issues**: contact the APGAP support team through your organization's helpdesk channel
