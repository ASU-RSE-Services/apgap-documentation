+++
title = 'Configure Reminder Emails'
date = 2026-07-09T07:07:07+01:00
weight = 11
+++

# Configure reminder emails

APGAP can send periodic email reminders about items still waiting on someone to act, e.g. data access requests that haven't been approved or denied. Reminders reuse each user's existing email preferences, so a user who has turned off a given type of email notification won't be reminded about it. Reminders are email-only; there is no in-app copy.

> [!WARNING]
**The Permissions required for this operation are Platform Admin**

1. Navigate to **Administration → Reminders → Reminder Settings**
1. Under **Send email reminders for**, toggle on each type of pending action you want reminders sent for. Each toggle saves immediately.
1. Under **Schedule**, set **Remind every [N] days** to control how often a reminder goes out for an item that is still unresolved
1. Set **Stop reminding after [N] days** to stop notifying about items past a certain age. Set this to **0** to keep reminding until the item is resolved.

Only action types that support reminders appear in the list. Turning a type off stops future reminders for that type without affecting the original notifications, which are always sent when the action first occurs.
