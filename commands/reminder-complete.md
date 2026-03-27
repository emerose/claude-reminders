---
description: Mark a reminder as complete in Apple Reminders
argument-hint: <title> [--list <list-name>]
allowed-tools: Bash
---

Use the apple-reminders skill to guide your AppleScript usage.

Mark a reminder as complete in Apple Reminders on macOS.

Parse $ARGUMENTS to extract:
- **title** (required): The name of the reminder to complete. Everything before any flag is the title.
- **--list <name>** (optional): Limit the search to a specific list. If not provided, search all lists.

Search strategy:
1. If --list is specified, search only that list for an incomplete reminder matching the title exactly.
2. If no --list, search all lists for the first incomplete reminder whose name matches exactly.
3. If no exact match is found, search for incomplete reminders whose name *contains* the title text (case-insensitive where possible).

Run the AppleScript via `osascript` heredoc. Use shell variables for the reminder title to handle apostrophes and special characters.

After completing the reminder, confirm to the user: the name of the reminder and which list it was in.

If multiple reminders match, list them and ask the user to clarify which one to complete (include the list name for each).

If no match is found, tell the user clearly and suggest running `/reminders` to see the current list.

If an error occurs that mentions authorization, tell the user to grant Reminders access in System Settings → Privacy & Security → Automation.
