# Apple Reminders AppleScript — Advanced Reference

## Reminder Properties (Full List)

| Property | Type | Notes |
|----------|------|-------|
| `name` | string | The reminder title |
| `body` | string | Notes field (optional) |
| `completed` | boolean | true = done |
| `completion date` | date / missing value | Set automatically when completed |
| `due date` | date / missing value | When the reminder is due |
| `remind me date` | date / missing value | When the alert fires |
| `priority` | integer | 0=none, 1=high, 5=medium, 9=low |
| `flagged` | boolean | Flagged status |
| `id` | string | Unique identifier (read-only) |
| `modification date` | date | Last modified (read-only) |
| `creation date` | date | When created (read-only) |

## Batch Operations

### List all reminders with due dates across all lists
```applescript
tell application "Reminders"
  set output to ""
  repeat with l in every list
    repeat with r in (reminders of l whose completed is false and due date is not missing value)
      set output to output & name of l & " | " & name of r & " | due: " & (due date of r as string) & linefeed
    end repeat
  end repeat
  return output
end tell
```

### Complete all reminders in a list
```applescript
tell application "Reminders"
  tell list "Daily Tasks"
    repeat with r in every reminder
      set completed of r to true
    end repeat
  end tell
end tell
```

### Get count of incomplete reminders per list
```applescript
tell application "Reminders"
  set output to ""
  repeat with l in every list
    set cnt to count of (reminders of l whose completed is false)
    if cnt > 0 then
      set output to output & name of l & ": " & cnt & " incomplete" & linefeed
    end if
  end repeat
  return output
end tell
```

## Adding Reminders with All Fields

```applescript
tell application "Reminders"
  set dueDate to date "Monday, March 30, 2026 at 9:00:00 AM"
  set newReminder to make new reminder at end of list "Work" with properties {name:"Prepare quarterly report", body:"Include Q1 metrics and compare to last year", due date:dueDate, priority:1}
end tell
```

## Searching by Due Date Range

### Reminders due today
```applescript
tell application "Reminders"
  set todayStart to current date
  set hours of todayStart to 0
  set minutes of todayStart to 0
  set seconds of todayStart to 0
  set todayEnd to todayStart + (23 * hours + 59 * minutes + 59)
  set output to ""
  repeat with l in every list
    repeat with r in (reminders of l whose completed is false and due date >= todayStart and due date <= todayEnd)
      set output to output & name of l & ": " & name of r & linefeed
    end repeat
  end repeat
  return output
end tell
```

### Overdue reminders
```applescript
tell application "Reminders"
  set rightNow to current date
  set output to ""
  repeat with l in every list
    repeat with r in (reminders of l whose completed is false and due date < rightNow and due date is not missing value)
      set output to output & name of l & ": " & name of r & " (due: " & (due date of r as string) & ")" & linefeed
    end repeat
  end repeat
  return output
end tell
```

## Creating a New Reminder List

```applescript
tell application "Reminders"
  make new list with properties {name:"Project Alpha"}
end tell
```

## Finding a Reminder by Partial Name

```applescript
tell application "Reminders"
  set searchTerm to "dentist"
  set output to ""
  repeat with l in every list
    repeat with r in (reminders of l whose completed is false)
      set rName to name of r
      if rName contains searchTerm then
        set output to output & name of l & ": " & rName & linefeed
      end if
    end repeat
  end repeat
  return output
end tell
```

## Priority Mapping

| AppleScript value | Meaning |
|-------------------|---------|
| 0 | None |
| 1 | High |
| 5 | Medium |
| 9 | Low |

## Common Error Messages

| Error | Cause | Fix |
|-------|-------|-----|
| "Not authorized to send Apple events to Reminders" | No privacy permission | Grant in System Settings → Privacy & Security → Automation |
| "Can't get list \"Name\"" | List doesn't exist | Check spelling (case-sensitive) or create the list |
| "Can't get reminder \"Name\"" | Reminder not found in the specified list | Verify name matches exactly |
| "-1728" | Object not found | Usually a list or reminder name mismatch |

## Shell Escaping Tips

```bash
REMINDER_NAME="Sam's dentist appointment"
osascript << APPLESCRIPT
tell application "Reminders"
  make new reminder at end of list "Reminders" with properties {name:"${REMINDER_NAME}"}
end tell
APPLESCRIPT
```
