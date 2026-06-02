+++
title = 'APGAP Quickstart Guide'
date = 2026-04-07T07:07:07+01:00
weight = 1
+++

# APGAP Quickstart Guide

Welcome to APGAP! This guide will get you up and running in minutes, regardless of your role. If you run into anything that isn't covered here, reach out to your Lab Director or Platform Admin.

## Before You Begin

To acquire an APGAP account you will need an email address from an approved domain (ASU, ADHS, University of Arizona, Northern Arizona University, TGen, or a partner organization). Contact your Lab Director or your organization's Platform Admin and provide them with your institutional email address. An account will then be created for you by a Platform Admin.

## Step 1 — Log In

Open a browser and navigate to [https://apgap.prod.rtd.asu.edu](https://apgap.prod.rtd.asu.edu)
Click **Sign in with Google**
Select your institutional Google account — this must match the email address registered in APGAP
After a moment you'll be redirected to your dashboard
- Open a browser and navigate to APGAP.
- Click **Sign in with Google**.
- Select your institutional Google account — this must match the email address registered in APGAP.
- After a moment you'll be redirected to your dashboard.

**Trouble logging in?**
| What you see | What to do |
| --- | --- |
| Domain not authorised | Your email domain hasn't been added to the allowlist. Contact a Platform Admin.|
| Account not enabled | Your account exists but hasn't been activated. Contact your Lab Director or Platform Admin. |
| Blank page or error | Try clearing your browser cache and logging in again. If it persists, contact your Platform Admin. |

## Step 2 — Getting Oriented

Your role determines what you can see and do. Here's a quick summary of the role-based access and permissions:

| Role                    | What you can do                                              |
| ----------------------- | ------------------------------------------------------------ |
| **Platform Admin**      | Full access to all organizations, labs, users, and settings. |
| **Bioinformatics User** | Access specific projects, launch pipelines, work with analytical datasets. |
| **Lab Director**        | Everything a Bioinformatics User can do, plus manage lab membership, approve access requests, and manage projects. |
| **Lab Collaborator**    | Upload sequences, add metadata, view files in your lab.      |
| **Data Analyst**        | Platform-wide read access to datasets. Cannot be assigned to individual labs. |
| **Lab Reader**          | View files in your lab. No data upload or edit permissions.  |

After logging in all users will land on a Dashboard. The left sidebar acts as the main navigation panel.
Here you will find links to your:   
1) **Dashboard** —  A landing page containing a high level view of dataset requests, sequence file status, and sequence archive requests;  
2) **Labs** — A list of labs of which you are a member;  
3) **Projects** —  A list of projects of which you are a member; project contain datasets;   
4) **Data Catalog** — A browsable view of sequence files available for analysis;  
5) **Account info** — The lower left corner contains a rollover menu labeled with your email. Clicking on this will reveal links to **Notifications**, the **User Guide**, a form to **Report Issues**, and **Logout**.  
6) The upper right hand corner shows your email address and a rollover menu labeled with "?". Clicking on "?" will reveal links to a **guided tour** of the site. 

Visible to Platform Admins only is an **Administration** menu, which contains links to several user and website management areas.

## Step 3 — Your First Task (by Role)

### If you're a Lab Director

Start by making sure your lab is configured properly. Click **Labs** → your lab and check:
- **Lab Roster** — are all your team members added with their proper roles?

- **Sequences** — are there any files in DRAFT status that need attention?

- **Projects** — are the right Bioinformatics Users assigned to each project?

To add a team member to your lab:
1. Click **Labs** → your lab → Lab Roster
1. Click **Assign User**
1. Select a **Project** for the user from the dropdown menu
1. Search for and select the user from the dropdown menu. If the user isn't found, they haven't been entered into the system yet. Ask your **Platform Admin** to create the user account.
1. If the user is going to be a **Lab Director** or **Lab Collaborator**, select the corresponding button. If the user will not have either of those roles, select the **Bioinformatics User** in the Select Permissions dropdown menu.
1. Click the **Assign User** box.
  

### If you're a Bioinformatics User

Click **Labs** in the sidebar and navigate to your lab

From the lab workspace you can:

- View the list of projects
- Upload sequence files and metadata files
- Edit metadata files
- View the lab roster
- View batch endpoints

Click the **Projects** tab and select your project.

From your project you can:
- View analytical datasets
- View a list of other project members
- Launch pipelines through the Seqera Workspace link (if your project workspace has been provisioned)
- Launch Jupyter Notebooks by clicking on the Notebook link

### If you're a Lab Collaborator

Your primary home is your Lab. Click **Labs** in the sidebar, then click your lab name. You'll see tabs for **Sequences**, **Projects**, **Lab Roster**, and **Batch Endpoints.**


### If you're a Platform Admin

Your first actions after a fresh deployment should be:
1. **Create your organization** → Administration → Organizations → Add New Organization
1. **Add your organization's email domain** to the whitelist→ Administration → Domain Whitelist → Add Allowed Domain
1. **Create labs** → Labs → Add Lab (this triggers a 5–15 minute provisioning process)
1. **Create user accounts** → Administration → User Management → Add User
1. **Assign users to labs** → Administration → User Management or via a Lab Roster's tab

The full Admin Guide covers each of these in detail, including recovery procedures for failed lab provisioning.


## Sequera
APGAP leverages **Seqera Platform** (formerly Nextflow Tower) to manage the execution of bioinformatics
pipelines. Seqera Platform is built and supported by Seqera, the company, which was founded by the
creators of Nextflow. Nextflow is an open-source workflow language widely used in
genomics. The Nextflow CLI provides the orchestration, monitoring, and compute
management that running pipelines at scale requires and Seqera Platform proveds the same tools in a browser-based web platform. Seqera Platform allows researchers to launch analytic pipelines via a comprehensive GUI and the results are tracked back into your APGAP project. Each APGAP project is automatically provisioned with its own dedicated Seqera workspace. Inside that workspace,
the **Launchpad** is your catalog of pre-configured pipelines ready to run against your data.

**Note:** When creating a new project, a lab director can choose to also provision a Seqera Workspace. This process will take a few minutes. If a Seqera Workspace link doesn't appear in a completely provisioned project, even though the Sequera Workspace option was checked when creating the project, contact a Platform Admin.


## Getting Help
- **Lab-level questions** (access, file issues, metadata): contact your **Lab Director**
- **Account or domain issues**: contact your **Platform Admin**
- **Technical platform issues**: contact the APGAP support team through your organization's help desk 
