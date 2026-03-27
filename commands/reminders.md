---
description: List incomplete reminders from Apple Reminders
argument-hint: [list-name]
allowed-tools: Bash
---

Use the apple-reminders skill to guide your AppleScript usage.

List incomplete reminders from Apple Reminders on macOS.

If $ARGUMENTS is provided, treat it as the name of a specific Reminders list and show only incomplete reminders from that list.

If no argument is provided, show all incomplete reminders across every list, grouped by list name.

Run the appropriate AppleScript via `osascript` using a heredoc. Capture the output and present it clearly:
- When showing all lists: display each list as a header with its reminders underneath, skipping any empty lists.
- When showing a specific list: display each reminder as a simple list item.
- Include due dates next to any reminder that has one, formatted as a friendly date (e.g. "due today", "due Mar 30", "overdue").
- If no incomplete reminders are found, say so clearly.

If an error occurs that mentions authorization or permissions, tell the user they need to grant Reminders access in System Settings → Privacy & Security → Automation.
