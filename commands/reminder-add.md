---
description: Add a new reminder to Apple Reminders
argument-hint: <title> [--list <list-name>] [--due <date>] [--note <text>]
allowed-tools: Bash
---

Use the apple-reminders skill to guide your AppleScript usage.

Add a new reminder to Apple Reminders on macOS.

Parse $ARGUMENTS to extract:
- **title** (required): The reminder text. Everything before any flag is the title. If the user provides the title in quotes, strip the quotes.
- **--list <name>** (optional): Which Reminders list to add to. Defaults to "Reminders" if not specified.
- **--due <date>** (optional): A due date in natural language (e.g. "tomorrow", "Friday", "March 30 at 9am"). Convert to a fully qualified AppleScript date string.
- **--note <text>** (optional): Additional notes for the body field.

Construct and run the AppleScript via `osascript` heredoc to create the reminder with the appropriate properties.

When constructing the AppleScript heredoc, use an unquoted heredoc delimiter (e.g. `<< APPLESCRIPT`) so shell variables are expanded. Pass the title and other values through shell variables to handle apostrophes and special characters safely.

After successfully creating the reminder, confirm to the user: what the reminder is called, which list it was added to, and the due date if one was set.

If the specified list doesn't exist, offer to add to the default "Reminders" list instead, or ask if the user wants to create the new list first.

If an error occurs that mentions authorization, tell the user to grant Reminders access in System Settings → Privacy & Security → Automation.
