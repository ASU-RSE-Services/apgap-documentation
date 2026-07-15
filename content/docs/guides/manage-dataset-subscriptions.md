+++
title = 'Manage Dataset Subscriptions'
date = 2026-07-09T07:07:07+01:00
weight = 9
+++

# Manage dataset subscriptions

A subscription is a rule that keeps an Analytical Dataset up to date automatically. Instead of adding files by hand, you define a rule based on metadata, and APGAP pulls in matching files as they become available. This is useful for standing collections — for example, "every PRIMARY influenza sequence from wastewater."

> [!WARNING]
**You can manage subscriptions if you are a Platform Admin, the dataset's creator, or a Lab Director of the dataset's lab.**

A subscription rule only pulls in a file when that file is **promoted to PRIMARY** and matches the rule. Files that are still in DRAFT are never added. Matches from your own lab are copied into the dataset directly; matches from other labs go through the normal access-request workflow, so the owning Lab Director still approves them.

### Add a subscription rule

1. Open the Analytical Dataset you want to keep updated
1. Click **Manage Dataset** → **Manage subscriptions**
1. Click **Add subscription**
1. Give the subscription rule a **Name**
1. Choose to allow whether the rule may match files from **other labs**. Cross-lab matches are requested, not copied directly.
1. Choose a **Source type** 
1. When you use more than one condition, choose whether a file must match all conditions (**AND**) or any of them (**OR**)
1. Build the rule from metadata conditions.
1. Click **Preview matches** to see which current PRIMARY files it would match
1. Click **Create subscription**

New rules are **Active** by default. From the **Manage subscriptions** panel you can see each rule's name, its conditions, whether it's active, and whether it allows cross-lab matches.

### Edit or remove a rule

1. Open the dataset → **Manage subscriptions**
1. Click the **Edit** icon to change a rule's name, conditions, or cross-lab setting, or toggle it inactive
1. Click the **Delete** icon to remove a rule

Deleting or deactivating a rule stops future matches from being pulled in. It does not remove files the rule has already added to the dataset.

Subscriptions can't be changed while the dataset is locked for another operation (for example, while a build or deletion is in progress).
