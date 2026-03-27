# claude-reminders

A Cowork plugin that lets Claude interact with **Apple Reminders** on macOS using AppleScript.

## Overview

This plugin gives Claude the ability to read from and write to your Apple Reminders app directly — no third-party service or API key required. It uses the built-in `osascript` command to run AppleScript on your Mac.

## Components

### Commands

| Command | Description |
|---------|-------------|
| `/reminders [list-name]` | List all incomplete reminders. Optionally filter to a specific list. |
| `/reminder-add <title> [--list <name>] [--due <date>] [--note <text>]` | Add a new reminder. |
| `/reminder-complete <title> [--list <name>]` | Mark a reminder as complete. |

### Skills

**apple-reminders** — Loads automatically when you ask about your reminders. Teaches Claude the AppleScript object model for Reminders and how to safely construct and run scripts from the shell.

## Setup

No API keys or configuration needed. The only requirement is a macOS privacy permission.

### Grant Reminders Access

The first time Claude tries to access Reminders, macOS may show a permission dialog — click **Allow**.

If you don't see the dialog, or if you get an authorization error, grant access manually:

1. Open **System Settings**
2. Go to **Privacy & Security → Automation**
3. Find the terminal or application running Claude
4. Enable **Reminders**

## Usage Examples

```
/reminders
→ Shows all incomplete reminders grouped by list

/reminders Work
→ Shows incomplete reminders in your "Work" list

/reminder-add Buy oat milk --list Shopping
→ Adds "Buy oat milk" to your Shopping list

/reminder-add Call dentist --due Friday at 9am
→ Adds reminder with a due date

/reminder-add Quarterly report --list Work --due March 30 --note Include Q1 metrics
→ Adds reminder with list, due date, and notes

/reminder-complete Buy oat milk
→ Marks "Buy oat milk" as complete (searches all lists)

/reminder-complete Call dentist --list Reminders
→ Marks "Call dentist" complete in the Reminders list
```

You can also speak naturally without slash commands — phrases like "show me my reminders", "add a reminder to call the dentist on Friday", or "mark the dentist reminder as done" will trigger the apple-reminders skill automatically.

## Notes

- List names are **case-sensitive** in AppleScript (e.g. "Shopping" ≠ "shopping")
- The default list is called "Reminders" on most macOS installs
- Due dates support natural language (e.g. "tomorrow", "next Monday", "March 30 at 9am")
- Completed reminders are hidden by default; only incomplete reminders are shown
