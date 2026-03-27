---
name: apple-reminders
description: >
  Use this skill when the user asks to interact with Apple Reminders — including
  listing reminders, adding a reminder, completing a reminder, deleting a reminder,
  or working with reminder lists on macOS. Triggers on phrases like "show my reminders",
  "add a reminder", "mark reminder as done", "what's on my reminder list", "create a
  reminder for", "check off a reminder", or "remind me to".
version: 0.1.0
---

# Apple Reminders via AppleScript

Control Apple Reminders on macOS by running AppleScript through the `osascript` command in Bash.

## Core Invocation Pattern

Always run AppleScript using a heredoc for multi-line scripts — this avoids shell escaping problems with quotes and special characters:

```bash
osascript << 'APPLESCRIPT'
tell application "Reminders"
  -- your code here
end tell
APPLESCRIPT
```

For short one-liners, `-e` works fine:
```bash
osascript -e 'tell application "Reminders" to get name of every list'
```

## Reminders Object Model

```
application "Reminders"
  └── list (named, e.g. "Reminders", "Work", "Shopping")
        └── reminder
              ├── name (string)
              ├── body (string — notes field)
              ├── completed (boolean)
              ├── due date (date or missing value)
              ├── remind me date (date or missing value)
              └── priority (integer: 0=none, 1=high, 5=medium, 9=low)
```

## Common Operations

### List all reminder lists
```applescript
tell application "Reminders"
  set listNames to name of every list
  return listNames
end tell
```

### List incomplete reminders (all lists)
```applescript
tell application "Reminders"
  set output to ""
  repeat with l in every list
    set listName to name of l
    set incompleteItems to (reminders of l whose completed is false)
    repeat with r in incompleteItems
      set output to output & listName & ": " & name of r & linefeed
    end repeat
  end repeat
  return output
end tell
```

### List incomplete reminders (specific list)
```applescript
tell application "Reminders"
  set targetList to list "Work"
  set incompleteItems to (reminders of targetList whose completed is false)
  set output to ""
  repeat with r in incompleteItems
    set output to output & name of r & linefeed
  end repeat
  return output
end tell
```

### Add a reminder (basic)
```applescript
tell application "Reminders"
  make new reminder at end of list "Reminders" with properties {name:"Buy milk"}
end tell
```

### Add a reminder with due date
```applescript
tell application "Reminders"
  set dueDate to date "Friday, March 28, 2026 at 9:00:00 AM"
  make new reminder at end of list "Reminders" with properties {name:"Call the dentist", due date:dueDate}
end tell
```

### Complete a reminder by name (first match, any list)
```applescript
tell application "Reminders"
  set found to false
  repeat with l in every list
    repeat with r in (reminders of l whose name is "Buy milk" and completed is false)
      set completed of r to true
      set found to true
    end repeat
    if found then exit repeat
  end repeat
  return found
end tell
```

### Complete a reminder by name in a specific list
```applescript
tell application "Reminders"
  tell list "Shopping"
    set completed of reminder "Buy milk" to true
  end tell
end tell
```

### Delete a reminder by name
```applescript
tell application "Reminders"
  repeat with l in every list
    set matches to (reminders of l whose name is "Old task" and completed is false)
    repeat with r in matches
      delete r
    end repeat
  end repeat
end tell
```

## Handling Dates

Use AppleScript's `date` keyword with a fully qualified string:
```applescript
set dueDate to date "Friday, March 28, 2026 at 9:00:00 AM"
```

To compute a date relative to now, use `current date`:
```applescript
set dueDate to (current date) + (2 * days)
```

## Permission Requirements

Apple Reminders requires explicit macOS privacy permission. If `osascript` returns an error like "Not authorized to send Apple events to Reminders", instruct the user to:

1. Open **System Settings → Privacy & Security → Automation**
2. Enable **Reminders** access for the terminal or application running Claude

Or they may see a permission dialog automatically the first time — they should click **Allow**.

## Error Handling

Wrap calls in try/on error when needed:
```applescript
tell application "Reminders"
  try
    tell list "NonExistent"
      -- ...
    end tell
  on error errMsg
    return "Error: " & errMsg
  end try
end tell
```

## Tips for Shell Integration

- Use `osascript -e` for simple one-liners; use heredoc (`<< 'APPLESCRIPT'`) for multi-line scripts
- AppleScript `linefeed` is equivalent to `\n` — use it to build multi-line output strings
- `return` in a `tell application` block returns output to stdout when invoked via `osascript`
- List names are case-sensitive in AppleScript
- If a reminder name contains a quote, escape it as `\"` inside the AppleScript string

## Detailed Reference

See `references/applescript-reference.md` for advanced operations: batch updates, priority handling, reminders with body/notes, searching by due date, and more.
