+++
title = 'Search the Data Catalog'
date = 2026-07-09T07:07:07+01:00
weight = 12
+++

# Search the Data Catalog

The Data Catalog is where you find sequence files across the platform — PRIMARY and ARCHIVED files from every active lab. This is the starting point for building an Analytical Dataset or requesting access to another lab's data. You can browse the full catalog, narrow it by lab, or filter on metadata.

1. Click **Data Catalog** in the sidebar

From here you have three ways to narrow the list:

- **Filter by Lab** — use the lab selector to limit results to one or more labs. Labs aren't metadata, so this control sits next to the search rather than inside it.
- **Advanced Search** — click **Advanced Search** to filter on metadata. The **Active Filters** box lists how many filters and of what type are currently active.

### Build a metadata filter

1. Click **Advanced Search**
1. Choose a **Source Type**
1. Select AND/OR corresponding to desired filter match logic
1. Optionally choose a date range for the uploaded files.
1. Add a filter by selecting a **Key** (metadata field). The available keys depend on the Source Type and match logic you've selected.
1. Select a **Value**
1. Click the **Apply** button

Add as many conditions as you need. When you have more than one, the match logic controls whether a file must match **all** of them or **any** of them. Each active condition appears as a removable tag in the Active Filters panel, so you can drop one without rebuilding the whole search.

Once you've found the files you want, select them and click **Create analytical dataset** to group them for analysis. Files from labs you don't belong to are requested through the access-request workflow. Files from labs you're already in are added directly.
