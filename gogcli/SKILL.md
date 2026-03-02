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

## Discovery Workflow

When encountering unfamiliar gogcli tasks:

1. **Check service exists:** `gog --help` (lists all services)
2. **Explore service:** `gog <service> --help` (shows subcommands)
3. **Check command details:** `gog <service> <action> --help` (shows flags and arguments)

## Drive Operations

### Listing and Searching (Read)

**List root folder (default):**
```bash
gog drive ls
```

**List a folder by ID:**
```bash
gog drive ls --parent <folderId>
```

**Search by name or content:**
```bash
gog drive search "budget"
gog drive search "name:'Q1 report'"
```

**Get file metadata:**
```bash
gog drive get <fileId>
```

**Get a web URL for a file:**
```bash
gog drive url <fileId>
```

### Downloading (Read)

**Download a file:**
```bash
gog drive download <fileId>
```

**Download with output path:**
```bash
gog drive download <fileId> --out "./local-name.pdf"
```

**Export Google Docs format:**
```bash
gog drive download <fileId> --format pdf
```

### Creating Files and Folders (Create)

**Upload a local file:**
```bash
gog drive upload "./report.pdf"
```

**Upload into a folder:**
```bash
gog drive upload "./report.pdf" --parent <folderId>
```

**Create a folder:**
```bash
gog drive mkdir "Project Docs"
```

**Create a folder inside another folder:**
```bash
gog drive mkdir "Project Docs" --parent <folderId>
```

### Updating Files and Folders (Update)

**Rename a file or folder:**
```bash
gog drive rename <fileId> "New Name"
```

**Move a file to another folder:**
```bash
gog drive move <fileId> --parent <folderId>
```

**Copy a file:**
```bash
gog drive copy <fileId> "Copy of File"
```

### Sharing (Basic)

**Share with a user:**
```bash
gog drive share <fileId> --to user --role writer --email user@example.com
```

**List permissions:**
```bash
gog drive permissions <fileId>
```

**Remove a permission:**
```bash
gog drive unshare <fileId> <permissionId>
```

### Deleting Files and Folders (Delete)

**Move to trash:**
```bash
gog drive delete <fileId>
```

**Delete permanently:**
```bash
gog drive delete <fileId> --permanent
```

## Gmail Operations

### Searching Emails (Read) - Most Common

**Basic keyword search (the default):**
```bash
# Search anywhere in the message (Gmail query string)
gog gmail search "budget"
gog gmail search "project alpha"
```

**Emails from last 24 hours:**
```bash
gog gmail search "newer_than:1d"
```

**Unread emails from last week:**
```bash
gog gmail search "is:unread newer_than:7d"
```

**Emails with attachments this month:**
```bash
gog gmail search "has:attachment after:2026/03/01"
```

**Recent emails from specific sender:**
```bash
gog gmail search "from:alice@example.com newer_than:3d"
```

**Common query syntax (Gmail search operators):**
```bash
# Plain text / keywords
gog gmail search "invoice"

# Sender/recipient
gog gmail search "from:alice@example.com"
gog gmail search "to:bob@example.com"

# Date filters
gog gmail search "after:2026/03/01"
gog gmail search "before:2026/03/31"

# Status and labels
gog gmail search "is:unread"
gog gmail search "is:starred"

# Combined queries
gog gmail search "invoice from:alice@example.com has:attachment newer_than:7d"
```

**Control result ordering:**
```bash
gog gmail search "query" --oldest=false  # Newest first (default)
gog gmail search "query" --oldest=true   # Oldest first
gog gmail search "query" --max 50        # Limit results
```

### Get Email Details

**View email details:**
```bash
gog gmail get <messageId>
```

### Sending Emails (Create)

**Basic send:**
```bash
gog send --to user@example.com --subject "Hello" --body "Message"
```

**With attachment:**
```bash
gog send --to user@example.com --subject "Report" --attach file.pdf
```

**Multiple recipients and CC:**
```bash
gog send --to "alice@example.com,bob@example.com" --cc manager@example.com --subject "Update"
```

**HTML email (brief):**
```bash
gog send --to user@example.com --subject "Newsletter" --body-html "<h1>Hello</h1>"
```

**Reply to email (brief):**
```bash
gog gmail reply <messageId> --body "Thanks for your email"
```

### Deleting Messages (Delete)

**Delete message:**
```bash
gog gmail delete <messageId>
```

**Batch delete (brief):**
```bash
# Search first, then delete
gog gmail search "older_than:1y" --max 100
# Then delete found messages one by one
```

### Draft Operations (Basic CRUD)

**Create draft:**
```bash
gog gmail draft create --to user@example.com --subject "Draft" --body "Content"
```

**List drafts:**
```bash
gog gmail drafts
```

**Send draft:**
```bash
gog gmail draft send <draftId>
```

**Delete draft:**
```bash
gog gmail draft delete <draftId>
```

### Managing Labels (Update)

**Mark as read/unread:**
```bash
gog gmail thread modify <threadId> --remove UNREAD  # Mark as read
gog gmail thread modify <threadId> --add UNREAD     # Mark as unread
```

**Star/unstar:**
```bash
gog gmail thread modify <threadId> --add STARRED    # Star
gog gmail thread modify <threadId> --remove STARRED # Unstar
```

**Archive and trash:**
```bash
gog gmail thread modify <threadId> --remove INBOX   # Archive
gog gmail thread modify <threadId> --add TRASH      # Move to trash
```

---

**For more advanced features (tracking, filters, settings, etc.), use:**
```bash
gog gmail <command> --help
```

## Calendar Operations

### Listing Events (Read) - Most Common

**Today's events:**
```bash
gog calendar events --today
```

**This week's events:**
```bash
gog calendar events --week
```

**Next 7 days:**
```bash
gog calendar events --days 7
```

**This month's events:**
```bash
gog calendar events --from 2026-03-01 --to 2026-03-31
```

**Next week (using relative dates):**
```bash
gog calendar events --from "monday" --to "friday"
```

**Custom time range:**
```bash
# Using dates
gog calendar events --from 2026-03-01 --to 2026-03-31

# Using ISO 8601 timestamps
gog calendar events --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T17:00:00+09:00"
```

**Search events:**
```bash
gog calendar search "meeting"
gog calendar search "standup" --from 2026-03-01
```

**Limit results:**
```bash
gog calendar events --max 50
```

### Creating Events (Create)

**Basic event:**
```bash
gog calendar create primary --summary "Meeting" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"
```

**All-day event:**
```bash
gog calendar create primary --summary "Holiday" --date 2026-03-05
```

**Event with attendees:**
```bash
gog calendar create primary --summary "Team Sync" --attendees "alice@example.com,bob@example.com" --from "2026-03-05T14:00:00+09:00" --to "2026-03-05T15:00:00+09:00"
```

**Event with Google Meet:**
```bash
gog calendar create primary --summary "Video Call" --meet --from "2026-03-05T15:00:00+09:00" --to "2026-03-05T16:00:00+09:00"
```

**Event with reminder:**
```bash
gog calendar create primary --summary "Appointment" --reminder 30 --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"
```

### Updating Events (Update)

**Update event basic info:**
```bash
gog calendar update primary <eventId> --summary "New Title"
gog calendar update primary <eventId> --from "2026-03-05T11:00:00+09:00" --to "2026-03-05T12:00:00+09:00"
```

**Update attendees:**
```bash
gog calendar update primary <eventId> --attendees "alice@example.com,bob@example.com,charlie@example.com"
```

### Deleting Events (Delete)

**Delete event:**
```bash
gog calendar delete primary <eventId>
```

**Delete recurring event:**
```bash
gog calendar delete primary <eventId> --single    # Delete this instance only
gog calendar delete primary <eventId> --future    # Delete this and future
gog calendar delete primary <eventId> --all       # Delete all instances
```

### Recurring Events (Basic)

**Daily recurrence:**
```bash
gog calendar create primary --summary "Daily Standup" --rrule "FREQ=DAILY" --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T09:30:00+09:00"
```

**Weekly recurrence:**
```bash
gog calendar create primary --summary "Weekly Meeting" --rrule "FREQ=WEEKLY;BYDAY=MO" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"
```

**Monthly recurrence:**
```bash
gog calendar create primary --summary "Monthly Review" --rrule "FREQ=MONTHLY;BYMONTHDAY=1" --from "2026-03-05T14:00:00+09:00" --to "2026-03-05T15:00:00+09:00"
```

### Responding to Invitations

**Accept invitation:**
```bash
gog calendar respond primary <eventId> --status accepted
```

**Decline invitation:**
```bash
gog calendar respond primary <eventId> --status declined
gog calendar respond primary <eventId> --status declined --comment "Conflict"
```

**Tentatively accept:**
```bash
gog calendar respond primary <eventId> --status tentative
```

### Get Event Details

**View event details:**
```bash
gog calendar event primary <eventId>
```

---

**For more advanced features (special event types, detailed options, freebusy, ACL, etc.), use:**
```bash
gog calendar <command> --help
```

## Common Mistakes

- **Forgetting account flag:** Multi-account setups need `-a user@example.com`
- **Not using --dry-run:** Test destructive operations first with `--dry-run`
- **Ignoring --help:** Commands have many useful flags - always check
- **Hardcoding IDs:** Use search/list to find IDs dynamically
- **Wrong time format:** Use ISO 8601 for precise times: `2026-03-05T10:00:00+09:00`
- **Windows timezone error:** If you see `unknown time zone` errors on Windows, set `ZONEINFO` environment variable to point to Go's timezone database
