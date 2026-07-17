+++
title = 'Manage Validation Rules'
date = 2026-07-09T07:07:07+01:00
weight = 13
+++

# Manage metadata validation rules

Validation rules decide whether a file's metadata is acceptable before it can be promoted to PRIMARY. They go beyond "is this field filled in" — they can check a value's format or range, and they can check one field against another. This is where you configure those checks.

> [!WARNING]
**The Permissions required for this operation are Platform Admin**

**This is an advanced, infrequently needed operation.** Rules apply across all labs, and a bad rule can block files from being promoted platform-wide. Change rules only when a metadata standard changes or a new check is genuinely needed.

1. Navigate to **Administration → Validation Rules**

The rules are split into two kinds, shown as tabs:

- **Field rules** validate a single field on its own — for example, requiring a value, enforcing a format, or restricting a number to a range.
- **Cross-field rules** validate one field against another. A rule has a **reference field** that governs a **dependent field**. For example, the county must be valid for the selected state, or a field becomes required only when the pathogen is reportable.

### Add or edit a rule

1. Select the **Field rules** or **Cross-field rules** tab
1. To add a cross-field rule, click **+ Add Cross-Field Rule**
1. Pick the **Rule Type** — this determines what the rule checks
1. Choose the field(s) the rule applies to. For a cross-field rule, choose the dependent field and the reference field, then the values that tie them together.
1. Save the rule

To edit or remove an existing rule, use the **Edit** and **Delete** icons in the rule's row.

### How rules take effect

Rules run whenever a file is validated or promoted to PRIMARY. A rule produces either an **error**, which blocks promotion until the value is corrected, or a **warning**, which a user can acknowledge and continue. Users see the specific messages your rules generate in the file's metadata panel, so write rule messages that tell them what to fix. 

Note that if a rule is changed for an existing metadata field, it will not apply retroactively to files that are already in PRIMARY status. However, if a correction is made to a PRIMARY file for that field, the new rule will apply at that time.
