+++
title = 'APGAP Platform Administrator Guide'
date = 2026-04-07T07:07:07+01:00
weight = 3
+++


This guide covers all administrative operations available to Platform Admins, including routine configuration tasks, user and lab management, metadata template management, and recovery procedures for infrastructure failures.
Platform Admins have unrestricted access across all organizations, labs, projects, and users. Handle this access with care — most destructive operations (deleting organizations, bulk-promoting files) cannot be reversed through the UI.

Table of Contents
Platform Admin access and constraints
Initial platform setup
Managing the domain whitelist
Managing organizations
Managing labs
Managing users
Managing lab membership
Managing metadata templates
Bulk operations via Django Admin
Recovery procedures
Monitoring and logs

1. Platform Admin access and constraints
Platform Admin status is controlled by the is_platform_admin boolean flag on a user's account. When this flag is set, the user is automatically added to the Platform Admin Django permission group, which governs all API-level access checks.
Important constraints for Platform Admin accounts:
Platform Admins cannot be added to individual Labs or Projects. The system enforces this — if a user needs both admin access and lab membership, use the Data Analyst role instead (platform-wide analytics access, no lab membership restrictions).
Platform Admin and Data Analyst flags are mutually exclusive in practice — a user should have one or the other, not both.
Changes to the is_platform_admin flag take effect immediately. The user does not need to log out and back in.
To set or remove Platform Admin status for a user, navigate to Admin → User Management, find the user, click the edit icon, and toggle the Platform Admin switch.

2. Initial platform setup
For a new APGAP deployment, complete these steps in order. Skipping steps or doing them out of order will cause errors (for example, you cannot create a lab without first having an organization).
Recommended setup order
Add approved email domains to the domain whitelist
Create at least one Organization
Create user accounts (domain must be whitelisted first)
Create Labs within their Organizations (triggers GCP provisioning)
Assign users to Labs
Configure metadata templates for each Source Type
Each step is covered in detail in the sections below.

3. Managing the domain whitelist
The domain whitelist controls which email domains are permitted to have accounts in APGAP. A user cannot be created unless their email domain is on the whitelist.
Important: Removing a domain from the whitelist does not deactivate existing users from that domain. Existing accounts remain active and can still log in. The whitelist is only checked when creating new accounts.
Add a domain
Navigate to Admin → Domain Whitelist
Click New Domain (or Add Domain depending on your version)
Enter the domain — include the extension (e.g., asu.edu, tgen.org, nau.edu)
Optionally add a description to note the associated organization
Click Add Domain
Edit a domain
Navigate to Admin → Domain Whitelist
Find the domain and click the Edit icon (pencil)
Make your changes
Click the checkmark to save, or the X to cancel
Delete a domain
Navigate to Admin → Domain Whitelist
Find the domain and click the Delete icon (trash can)
Confirm the deletion

4. Managing organizations
Organizations are the top-level container in APGAP. Each institution — ASU, ADHS, University of Arizona, NAU, TGen, or a partner agency — should be one Organization. Labs belong to Organizations.
Create an organization
Navigate to Admin → Organizations
Click Add New Organization
Enter the Organization Name (must be unique across the platform)
Set Default Approve Analytical Dataset Requests if applicable:
When enabled, access requests for files from this organization's labs are automatically approved when requested by users within the same organization
Use this for internal teams or highly trusted partners where per-request approval is not required
Click Add Organization
Delete an organization
⚠️ Deleting an organization is not reversible through the UI. All associated labs and data will remain in the database but the organization will be hidden from all views.
Navigate to Admin → Organizations
Find the organization and click the Delete icon
In the confirmation dialog, click Yes, delete
Organizations are soft-deleted (active = False) — the underlying data is preserved but the organization is invisible to users.

5. Managing labs
Labs belong to Organizations. Creating a lab triggers a Cloud Build pipeline that provisions a dedicated Google Cloud project for the lab — including a GCS bucket, IAM configuration, and VPC. This process takes 5 to 15 minutes.
Every lab must have at least one Lab Director before users can be assigned or files can be uploaded.
Create a lab
Before proceeding: Ensure the Organization exists and the designated Lab Director's user account has been created.
Navigate to Labs
Click Create Lab
In the dialog:
Select the Organization
Enter the Lab Name (display name)
Add at least one Lab Director
Click Create Lab
The lab will appear in the roster with Build Status: WORKING while infrastructure is being provisioned. When complete, the status changes to SUCCESS and the lab can accept file uploads.
Do not add users or attempt file uploads until the Build Status shows SUCCESS.
Monitor lab provisioning
To check provisioning status:
In the Labs list, look at the Build Status column for the lab
If you need more detail, check Cloud Build logs in the GCP Console:
GCP Console → Cloud Build → History
Filter by trigger: lab-tf-apply
Find the run associated with your lab's project_prefix
Edit a lab
Lab names and descriptions can be edited through the UI:
Click Labs → select the lab
Click Edit
Update the description or other editable fields
Click Save
The lab's project_prefix (used for GCP resource naming) cannot be changed after creation.

6. Managing users
APGAP does not support self-signup. All user accounts must be created by a Platform Admin.
Create a user
Before proceeding: Ensure the user's email domain is on the domain whitelist.
Navigate to Admin → User Management
Click Add User
Fill in:
Email address — must match the Google account they'll use to log in
Name — display name in the platform
Organization — the organization this user belongs to
Set role flags if applicable:
Platform Admin — grants full platform access; user cannot be added to labs
Data Analyst — grants platform-wide analytics access; user cannot be added to labs
Click Add User
The user will receive a welcome notification. They log in by navigating to the APGAP URL and clicking Sign in with Google using the email address you registered.
If you see "Domain not authorised" when creating a user, the email domain has not been added to the whitelist yet — add it first (see Section 3).
Edit a user
Navigate to Admin → User Management
Find the user and click the Edit icon
Select Edit User Details
Make your changes and click Save
Changing a user's email address is possible but requires that the new address matches an active Google account. The user will need to use the new email to log in after the change.
Deactivate or delete a user
User deactivation is handled through the Django admin panel (/admin/). In the main platform UI, you can remove a user from all their labs, which effectively revokes their access to lab data, but their account remains active.
For full deactivation, use the Django admin panel:
/admin/users/user/ → find the user → uncheck Active → save

7. Managing lab membership
Assign a user to a lab
Users can be assigned to labs from two places: through Admin → User Management or directly from the Lab Roster tab within a lab. The Lab Roster approach is generally more convenient.
From the Lab Roster:
Navigate to the lab → Lab Roster tab
Click Assign User
Search for the user by email
Select their Role:
Lab Director — full lab admin, can approve access requests and manage roster
Lab Collaborator — read/write, no admin capabilities
Lab Reader — read-only
Bioinformatics User — assign at the project level, not the lab level
Click Assign User
From User Management:
Navigate to Admin → User Management
Find the user → click Assign Lab
Toggle the Lab Director switch if applicable
Select the lab from the dropdown
Click Assign
Assign a user to a project
After assigning a user to a lab, they can be assigned to specific projects within that lab:
Navigate to the lab → Projects tab → select the project
Click the Users tab → Assign User
Select the user and their project permissions
Click Assign User
Remove a user from a lab
Navigate to the lab → Lab Roster tab
Find the user and click the trash can icon
Click Yes, Remove to confirm

8. Managing metadata templates
Metadata templates define which fields appear for each Source Type (Human, Wastewater, Wildlife, Animal/Livestock, etc.) and which are required for a file to reach PRIMARY status. Template changes affect all labs and all future CSV template downloads.
This is an infrequently needed operation — only make changes when adding support for a new Source Type or when a field definition needs to change. Incorrect changes can cause bulk metadata import failures across all labs.
View and edit a metadata template
Navigate to Admin → Metadata Management
Select the Source Type to configure
You'll see the current fields with their key name, required status, value type, and sort order
To edit an existing field:
Click the Edit icon next to the field
Update the settings:
Is Required — whether the field must be filled for PRIMARY status
Value Type — TEXT, NUMBER, DATE, or SELECT
Multiple values — whether a field can accept more than one value (SELECT type only)
Sort Order — display position in the CSV template and UI
For SELECT type fields, add or remove allowable values using the +Value button
Click Save
To remove a value from a SELECT field, click the X next to the value. Note that some core fields cannot be deleted — these are protected because they are required for NCBI/standards compatibility.
Add a custom metadata key
Navigate to Admin → Metadata Management
Click Add Custom Key/Value
In the dialog:
Enter the Key Name — will be normalized to uppercase automatically
Select the Key Format (TEXT, NUMBER, DATE, or SELECT)
Click Add Key
The new key will appear in the Metadata Management view and can be added to Source Type templates. It will also appear in CSV template downloads for any Source Type it is associated with.
Delete a custom key
Built-in keys (required for standards compliance) cannot be deleted. Custom keys can be deleted from the Metadata Management view:
Navigate to the key you want to delete
Click the Delete icon
A confirmation dialog will appear — click Yes to confirm
⚠️ Deleting a key also removes all associated metadata values from any files that have that key. This is irreversible.

9. Bulk operations via Django Admin
Some operations are not available in the main APGAP UI and require the Django Admin panel, accessible at https://apgap.prod.rtd.asu.edu/admin/.
Log in with a Django superuser account (separate from your APGAP Google OAuth account). Contact your infrastructure team if you need Django admin access configured.
Promote DRAFT files to PRIMARY in bulk
Use this when you have confirmed that a DRAFT backlog cannot be resolved through metadata entry — for example, old records where the metadata cannot be retroactively obtained.
Navigate to Admin panel → Files → Files
Use the filters on the right to narrow by:
Status = DRAFT
Lab = [target lab]
Source type = [if applicable]
Select the files you want to promote using the checkboxes
From the Action dropdown, select Mark as Primary
Click Go
⚠️ Only bulk-promote files when you have confirmed the metadata issue is resolved or the records are being archived. Promoting DRAFT files bypasses the metadata completeness check.
Reset stuck PROCESSING files
Files stuck in PROCESSING for more than an hour have almost certainly had their background task fail silently. Marking them FAILED allows labs to see the files and decide whether to re-upload.
Navigate to Admin panel → Files → Files
Filter by Status = PROCESSING
Sort by Created at to identify files older than one hour
Select the stuck files
From the Action dropdown, select Mark as Failed
Click Go
Notify the relevant Lab Director so they can inform their team.

10. Recovery procedures
Failed lab provisioning
When a lab's Build Status shows FAILURE, the lab has no GCS bucket and cannot accept file uploads. Users can see the lab but cannot upload to it.
Step 1 — Diagnose the failure:
GCP Console → Cloud Build → History
Filter by trigger: lab-tf-apply
Find the failed run for the lab (the run description will include the lab's project_prefix)
Read the failure reason in the build logs
Common causes: GCP quota exceeded, IAM permission error, transient API failure.
Step 2 — Fix the underlying issue:
Resolve whatever caused the Terraform failure (e.g., request quota increase, fix IAM permissions) before re-triggering.
Step 3 — Re-trigger provisioning:
GCP Console → Cloud Build → Triggers
Find the lab-tf-apply trigger
Click Run → set the _LAB_NAME substitution variable to the lab's project_prefix
Click Run Trigger
Monitor the build until it shows SUCCESS.
Step 4 — Update the lab's status in APGAP:
After successful provisioning, run the following from the Django shell:
from asu_apgap.labs.models import Lab

lab = Lab.objects.get(display_name="Lab Display Name")
lab.populate_terraform_cache()
lab.build_status = "SUCCESS"
lab.save()
print(f"Done. GCS bucket: {lab.gcs_bucket}")
The lab will now appear as fully provisioned and can accept uploads.

Failed Seqera workspace provisioning
When a Project's Seqera workspace was not created successfully, the Pipelines section of that project will be empty.
Step 1 — Check project status:
In the project view, look for seqera_launchpad_url. If empty, provisioning failed.
Step 2 — Re-trigger provisioning:
GCP Console → Cloud Build → Triggers
Find the deployment-tf-apply trigger
Manually invoke it with the project's substitution variables
Monitor until the build shows SUCCESS
Step 3 — Verify:
After successful provisioning, the backend will pick up the Seqera workspace details from Terraform state on the next request to the project. Ask the Lab Director to navigate to the project — the Pipelines section should now be available.
If Seqera itself is experiencing an outage, file uploads and metadata management still work normally — only pipeline launching is affected. Communicate this clearly to affected users.

DRAFT backlog — triage
If a lab has a significant number of files stuck in DRAFT, run the following query against the production database to understand the scope:
SELECT
    l.display_name AS lab,
    COUNT(*) AS draft_count,
    MIN(f.created_at) AS oldest_draft
FROM files_file f
JOIN labs_lab l ON f.lab_id = l.id
WHERE f.status = 'DRAFT'
GROUP BY l.display_name
ORDER BY draft_count DESC;
For labs with a large backlog where metadata can be collected, provide the Lab Director with a pre-filled CSV export of their DRAFT files. From the Django shell:
from asu_apgap.labs.models import Lab
from asu_apgap.metadatatags.models import SourceType
from asu_apgap.metadata_requirements.api.csv_handlers.export_handler import CSVExportHandler

lab = Lab.objects.get(display_name="Lab Display Name")
source_type = SourceType.objects.get(name="Human")  # adjust as needed

handler = CSVExportHandler(lab=lab, source_type=source_type)
csv_content = handler.export_csv()

# Write to file for distribution
with open("/tmp/draft_export.csv", "w") as f:
    f.write(csv_content)

print("Export complete. Send /tmp/draft_export.csv to the lab.")
The CSVImportHandler is idempotent — re-importing metadata for a file that already has a value is a no-op. Labs can safely re-import without risk of overwriting existing metadata.
For files where metadata cannot be obtained, use the bulk promote procedure in Section 9 only after confirming with the Lab Director.

Resetting a user's access
If a user reports they can see a lab but cannot take any actions, check:
Their lab role — Lab Readers have read-only access by design
Their is_active status in the Django admin panel
Whether the lab's Build Status is SUCCESS (users cannot upload to labs that aren't fully provisioned)
To reset a user's lab assignment:
Remove them from the lab (Lab Roster → trash can icon)
Re-add them with the correct role

11. Monitoring and logs
Application logs
Application logs are sent to Google Cloud Logging. To view them:
GCP Console → Logging → Log Explorer
Filter by:
Resource type: k8s_container
Severity: ERROR or WARNING (for problem investigation)
Filter: jsonPayload.logger="django" for Django application logs
Key log signatures
Log message
Indicates
Action
Celery task failed
Background processing error (DLP scan, metadata import)
Check the task name and traceback; may need to retry the operation
Failed to send governance notification
SendGrid or email configuration issue
Check SendGrid API key and sender domain
Failed to get output value for ... in lab
Terraform state read failure
Run lab.populate_terraform_cache() from the Django shell
Failed to delete GCS blob for file
GCS permission or connectivity issue
Check IAM bindings on the lab's GCS bucket
No approvers found for access request
A lab has no Lab Directors assigned
Add a Lab Director to the lab immediately
Bad Request: /authentication/google/login/
OAuth login failure
Check OAuth credential configuration

Recommended alerting configuration
Configure the following alerts in Google Cloud Monitoring to catch issues before users report them:
Celery task failure rate exceeding a threshold over a rolling window
File status = FAILED count increasing over time
Cloud Build trigger failures for lab-tf-apply and deployment-tf-apply
Django ERROR-level log messages
5xx error rate from the GKE ingress load balancer
The google-cloud-logging library is already included in the platform's dependencies — it just needs to be wired into alerting policies in the GCP Console under Monitoring → Alerting.
Checking Celery task health
Files stuck in PROCESSING for more than an hour indicate a Celery task failure. To check the current state of the task queue from the Django shell:
from celery import current_app

# Check active workers
inspect = current_app.control.inspect()
print("Active workers:", inspect.active())
print("Scheduled tasks:", inspect.scheduled())
print("Reserved tasks:", inspect.reserved())
If no workers are reported, the Celery worker pod may be down. Check the GKE pod status:
kubectl get pods -n apgap | grep celery
kubectl logs <celery-worker-pod-name> -n apgap --tail=50
