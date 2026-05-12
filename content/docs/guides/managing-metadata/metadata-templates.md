+++
title = 'Managing Metadata templates'
date = 2026-04-07t07:07:07+01:00
weight = 3
+++

# Managing metadata templates

Metadata templates define which fields appear for each Source Type (Human, Wastewater, Wildlife, Animal/Livestock, etc.) and which are required for a file to reach PRIMARY status. Template changes affect all labs and all future CSV template downloads.

**This is an infrequently needed operation** — only make changes when adding support for a new Source Type or when a field definition needs to change. Incorrect changes can cause bulk metadata import failures across all labs.

### View and edit a metadata template

1. Navigate to **Admin → Metadata Management** 
1. Select the **Source Type** to configure
1. You'll see the current fields with their key name, required status, value type, and sort order

To edit an existing field:
1. Click the **Edit** icon next to the field
1. Update the settings:
    - **Is** **Required** — whether the field must be filled for PRIMARY status
    - **Value** **Type** — TEXT, NUMBER, DATE, or SELECT
    - **Multiple** **values** — whether a field can accept more than one value (SELECT type only)
    - **Sort** **Order** — display position in the CSV template and UI
1. For SELECT type fields, add or remove allowable values using the **+Value** button
1. Click **Save**
To remove a value from a SELECT field, click the **X** next to the value. Note that some core fields cannot be deleted — these are protected because they are required for NCBI/standards compatibility.

### Add a custom metadata key

1. Navigate to **Admin → Metadata Management** 
1. Click **Add Custom Key/Value** 
1. In the dialog:
    - Enter the **Key Name** — will be normalized to uppercase automatically
    - Select the **Key Format** (TEXT, NUMBER, DATE, or SELECT)
1. Click **Add** **Key**
The new key will appear in the Metadata Management view and can be added to Source Type templates. It will also appear in CSV template downloads for any Source Type it is associated with.

### Delete a custom key
Built-in keys (required for standards compliance) cannot be deleted. Custom keys can be deleted from the Metadata Management view:
1. Navigate to the key you want to delete
1. Click the **Delete** icon
1. A confirmation dialog will appear — click **Yes** to confirm
⚠️ Deleting a key also removes all associated metadata values from any files that have that key. This is irreversible.

