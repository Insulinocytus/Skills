---
name: gogcli
description: Use when managing Google services (Gmail, Calendar, Drive, Docs, Sheets, Contacts, Tasks, People, Chat, Classroom, Forms, Keep) via command line, or when user mentions gog commands
---

# gogcli - Google Services CLI

## Overview

gogcli is a unified CLI for Google Workspace services. Core principle: **Explore dynamically rather than memorize all commands**.

## Command Structure

```
gog <service> <action> [arguments] [flags]
```

**Common services:** gmail, calendar, drive, docs, sheets, contacts, tasks, people, chat, classroom, forms, keep

**Common actions:** list/ls, get, create, update, delete, search

**Top-level aliases:** `send` (gmail), `ls`/`search`/`download`/`upload` (drive), `me`/`whoami` (people)

## Quick Reference

| Task | Command Pattern | Example |
|------|----------------|---------|
| List items | `gog <service> list` | `gog calendar events` |
| Search | `gog <service> search <query>` | `gog drive search "report"` |
| Get details | `gog <service> get <id>` | `gog drive get <fileId>` |
| Create | `gog <service> create [flags]` | `gog calendar create --summary "Meeting"` |
| Delete | `gog <service> delete <id>` | `gog drive delete <fileId>` |
| Upload file | `gog drive upload <path>` | `gog upload report.pdf` |
| Send email | `gog send --to user@example.com` | `gog send --to alice@example.com --subject "Hi"` |

## Discovery Workflow

When encountering unfamiliar gogcli tasks:

1. **Check service exists:** `gog --help` (lists all services)
2. **Explore service:** `gog <service> --help` (shows subcommands)
3. **Check command details:** `gog <service> <action> --help` (shows flags and arguments)

## Common Flags

- `-j, --json`: Output JSON (for scripting)
- `-p, --plain`: Output parseable text (TSV)
- `-n, --dry-run`: Preview without making changes
- `-y, --force`: Skip confirmations
- `-a, --account`: Specify account email
- `--select`: Select specific JSON fields

## Common Patterns

**List with filters:**
```bash
gog calendar events --from 2026-03-01 --to 2026-03-31
gog drive ls --query "mimeType='application/pdf'"
```

**JSON output for scripting:**
```bash
gog people me --json | jq '.emailAddresses'
gog drive search "budget" --json --results-only
```

**Batch operations:**
```bash
# Find and process multiple files
gog drive search "old report" --json | jq -r '.files[].id' | while read id; do
  gog drive delete "$id"
done
```

## Gmail CRUD Operations

### Create (Send Email)
```bash
# Basic email
gog gmail send --to user@example.com --subject "Hello" --body "Message content"

# With attachments and CC
gog send --to alice@example.com --cc bob@example.com --subject "Report" --body "See attached" --attach report.pdf

# HTML email
gog send --to team@example.com --subject "Update" --body-html "<h1>Title</h1><p>Content</p>"

# Reply to thread
gog gmail send --reply-to-message-id <messageId> --reply-all --body "Thanks!"
```

### Read (Search & Get)
```bash
# Search emails (Gmail query syntax)
gog gmail search "from:alice@example.com subject:report"
gog gmail search "is:unread after:2026/03/01"
gog gmail search "has:attachment larger:5M"

# Get specific message
gog gmail get <messageId>
gog gmail get <messageId> --json | jq '.payload.headers'

# Download attachment
gog gmail attachment <messageId> <attachmentId> --output file.pdf
```

### Update (Modify Thread/Labels)
```bash
# Add/remove labels
gog gmail thread modify <threadId> --add-labels IMPORTANT,STARRED
gog gmail thread modify <threadId> --remove-labels UNREAD

# Mark as read/unread
gog gmail thread modify <threadId> --remove-labels UNREAD  # mark read
gog gmail thread modify <threadId> --add-labels UNREAD     # mark unread
```

### Delete (Trash/Delete)
```bash
# Move to trash
gog gmail thread modify <threadId> --add-labels TRASH

# Permanent delete (use with caution)
gog gmail messages delete <messageId> --permanent
```

## Calendar CRUD Operations

### Create Event
```bash
# Basic event (calendarId is usually "primary" or email address)
gog calendar create primary --summary "Team Meeting" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"

# With attendees and location
gog calendar create primary --summary "Project Review" --from "2026-03-05T14:00:00+09:00" --to "2026-03-05T15:00:00+09:00" --attendees "alice@example.com,bob@example.com" --location "Conference Room A"

# All-day event
gog calendar create primary --summary "Holiday" --from "2026-03-10" --to "2026-03-11" --all-day

# With Google Meet
gog calendar create primary --summary "Remote Standup" --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T09:30:00+09:00" --with-meet

# Recurring event
gog calendar create primary --summary "Weekly Sync" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T10:30:00+09:00" --rrule "FREQ=WEEKLY;BYDAY=TU"
```

### Read (List & Get)
```bash
# List upcoming events
gog calendar events
gog calendar events primary --from 2026-03-01 --to 2026-03-31

# Get specific event
gog calendar event primary <eventId>

# Search events
gog calendar search "standup" --from 2026-03-01

# Check free/busy
gog calendar freebusy primary --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T17:00:00+09:00"
```

### Update Event
```bash
# Update event details
gog calendar update primary <eventId> --summary "Updated Title"
gog calendar update primary <eventId> --from "2026-03-05T15:00:00+09:00" --to "2026-03-05T16:00:00+09:00"

# Add attendees
gog calendar update primary <eventId> --attendees "alice@example.com,bob@example.com,charlie@example.com"

# Respond to invitation
gog calendar respond primary <eventId> --response accepted
gog calendar respond primary <eventId> --response declined --comment "Conflict"
```

### Delete Event
```bash
# Delete event
gog calendar delete primary <eventId>

# Delete without confirmation
gog calendar delete primary <eventId> -y
```

## Agent-Friendly Features

- `gog agent exit-codes`: Stable exit codes for error handling
- `gog schema <command>`: Machine-readable command schema
- `--json --results-only`: Clean JSON output without envelope
- `--no-input`: Never prompt (fail instead, useful for automation)

## When to Expand This Skill

If you repeatedly need detailed reference for a specific service, create `gogcli-<service>-reference.md` with comprehensive command documentation.

## Common Mistakes

- **Forgetting account flag:** Multi-account setups need `-a user@example.com`
- **Not using --dry-run:** Test destructive operations first
- **Ignoring --help:** Commands have many useful flags - always check
- **Hardcoding IDs:** Use search/list to find IDs dynamically
