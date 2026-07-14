+++
title = 'Add Post-Analysis Metadata'
date = 2026-07-09T07:07:07+01:00
weight = 7
+++

# Add post-analysis metadata to a file

Some metadata only exists after you've analyzed a sequence — lineage calls, quality metrics, or other analysis findings. Post-analysis metadata lets you attach these results to a file that is already **PRIMARY**, without disturbing the original submission metadata. Published results appear in the Data Catalog and flow into any Analytical Dataset that includes the file.

> [!WARNING]
**The Permissions required for this operation are Lab Director or the file's uploader**

Post-analysis metadata is organized into **sets**. You build a set as a draft, then publish it. Each published set is dated and cannot be changed afterward; if you need to correct or add results later, you publish a new set. A file can carry many published sets but only one open draft at a time.

### Publish a set of post-analysis metadata

1. Click **Labs** → your lab → **Sequences** tab
1. Open a **PRIMARY** file (filename or pencil icon)
1. Click on the **Add Post-Analysis Metadata** button
1. Start a new set and add the fields and values from your analysis
1. Click to **Publish** the set

Once published, the set's values become visible in the Data Catalog and in every dataset copy of the file. A published file shows a marker with the date it was last published.

### Use custom tags for lab-specific fields

If your analysis produces a field that isn't in the standard metadata list, your lab can define its own **custom tag** and use it in post-analysis sets.

1. In the **Post-Analysis Metadata** panel, under **Custom tags**, click **Create a new custom tag**
1. Name the tag and choose its format (TEXT, NUMBER, DATE, SELECT, GPS COORDINATES or LIST). SELECT tags get their own per-lab list of allowed values.
1. Click the **Create tag** button.

Custom tags behave like standard fields: their values appear in the catalog, in CSV exports, and in dataset copies. If you mark a custom tag **private**, its values are visible only to members of your lab and are hidden from anyone outside it.
