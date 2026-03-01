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

## Discovery Workflow

When encountering unfamiliar gogcli tasks:

1. **Check service exists:** `gog --help` (lists all services)
2. **Explore service:** `gog <service> --help` (shows subcommands)
3. **Check command details:** `gog <service> <action> --help` (shows flags and arguments)

## Quick Reference - Gmail

### Most Common Operations

**List emails (newest first):**
```bash
gog gmail search "in:inbox" --oldest=false
```

**Show emails from last 24 hours:**
```bash
gog gmail search "newer_than:1d"
```

**List emails newest first from last 24 hours:**
```bash
gog gmail search "newer_than:1d" --oldest=false
```

**Search with filters:**
```bash
gog gmail search "from:alice@example.com subject:report"
gog gmail search "is:unread has:attachment"
```

**Send email:**
```bash
gog send --to user@example.com --subject "Hello" --body "Message"
gog send --to user@example.com --subject "Report" --attach file.pdf
```

**Get email details:**
```bash
gog gmail get <messageId>
```

**Mark as read/starred:**
```bash
gog gmail thread modify <threadId> --remove UNREAD  # Mark as read
gog gmail thread modify <threadId> --add STARRED    # Star
```

### Advanced Gmail Features

For detailed documentation on advanced Gmail features, see [gmail-reference.md](references/gmail-reference.md):
- Advanced search syntax and query operators
- HTML emails, attachments, and tracking
- Draft management workflow
- Label management and batch operations
- Thread operations and attachment handling

## Quick Reference - Calendar

### Most Common Operations

**Get calendar events (custom time range):**
```bash
# Using specific dates
gog calendar events primary --from "2026-02-27" --to "2026-03-16"

# Using relative keywords
gog calendar events primary --from "monday" --to "friday"
```

**List upcoming events:**
```bash
gog calendar events                    # Default upcoming events
gog calendar events --today            # Today's events
gog calendar events --week             # This week
gog calendar events --days 7           # Next 7 days
```

**List events for specific time range:**
```bash
gog calendar events primary --from 2026-03-01 --to 2026-03-31
gog calendar events primary --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T17:00:00+09:00"
```

**Create simple event:**
```bash
gog calendar create primary --summary "Meeting" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"
gog calendar create primary --summary "Team Sync" --attendees "alice@example.com,bob@example.com" --from "2026-03-05T14:00:00+09:00" --to "2026-03-05T15:00:00+09:00"
```

**Search events:**
```bash
gog calendar search "meeting"
gog calendar search "standup" --from 2026-03-01
```

**Respond to invitation:**
```bash
gog calendar respond primary <eventId> --status accepted
gog calendar respond primary <eventId> --status declined --comment "Conflict"
```

**Get event details:**
```bash
gog calendar event primary <eventId>
```

### Advanced Calendar Features

For detailed documentation on advanced Calendar features, see [calendar-reference.md](references/calendar-reference.md):
- Recurring events and RRULE syntax
- Special event types (focus-time, out-of-office, working-location)
- Advanced event options (reminders, colors, visibility, guest permissions)
- Freebusy queries and conflict detection
- Team calendars and ACL management

## Common Flags

- `-j, --json`: Output JSON (for scripting)
- `-p, --plain`: Output parseable text (TSV)
- `--results-only`: Output only main results (no pagination tokens)
- `-n, --dry-run`: Preview without making changes
- `-y, --force`: Skip confirmations
- `-a, --account`: Specify account email
- `--select`: Select specific JSON fields
- `--no-input`: Never prompt (fail instead, useful for automation)

## Common Patterns

**JSON output for scripting:**
```bash
gog people me --json | jq '.emailAddresses'
gog drive search "budget" --json --results-only
gog gmail search "query" --json --results-only | jq -r '.messages[].id'
```

**Batch operations:**
```bash
# Find and process multiple items
gog gmail search "older_than:1y" --json --results-only | jq -r '.messages[].id' | while read id; do
  gog gmail batch modify "$id" --remove INBOX
done
```

**Dry-run before execution:**
```bash
gog calendar delete primary <eventId> --dry-run  # Preview first
gog calendar delete primary <eventId> -y         # Then execute
```

## Agent-Friendly Features

- `gog agent exit-codes`: Stable exit codes for error handling
- `gog schema <command>`: Machine-readable command schema
- `--json --results-only`: Clean JSON output without envelope
- `--no-input`: Never prompt (fail instead, useful for automation)

## Common Mistakes

- **Forgetting account flag:** Multi-account setups need `-a user@example.com`
- **Not using --dry-run:** Test destructive operations first
- **Ignoring --help:** Commands have many useful flags - always check
- **Hardcoding IDs:** Use search/list to find IDs dynamically
- **Wrong time format:** Use ISO 8601 for precise times: `2026-03-05T10:00:00+09:00`
- **Windows timezone error:** If you see `unknown time zone` errors on Windows, set `ZONEINFO` environment variable to point to Go's timezone database: `export ZONEINFO="/path/to/go/lib/time/zoneinfo.zip"` (find it in your Go installation directory, or download from golang.org; add to `.bashrc` for persistence)
