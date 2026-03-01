# Calendar Reference - Advanced Features

This document provides comprehensive documentation for advanced Calendar operations using gogcli.

## Table of Contents

- [Listing Events](#listing-events)
- [Creating Events](#creating-events)
- [Special Event Types](#special-event-types)
- [Updating Events](#updating-events)
- [Recurring Events](#recurring-events)
- [Event Responses](#event-responses)
- [Freebusy and Conflicts](#freebusy-and-conflicts)
- [Calendar Management](#calendar-management)
- [Team Calendars](#team-calendars)
- [Advanced Features](#advanced-features)

## Listing Events

### Basic Listing

```bash
# List upcoming events from primary calendar
gog calendar events

# List from specific calendar
gog calendar events user@example.com

# List with alias
gog calendar ls primary
```

### Time Range Filters

**Predefined ranges:**
```bash
gog calendar events --today           # Today's events
gog calendar events --tomorrow        # Tomorrow's events
gog calendar events --week            # This week's events
gog calendar events --days 7          # Next 7 days
```

**Custom ranges:**
```bash
# Specific dates
gog calendar events --from 2026-03-01 --to 2026-03-31

# ISO 8601 timestamps
gog calendar events --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T17:00:00+09:00"

# Relative keywords (supported: today, tomorrow, monday, tuesday, etc.)
gog calendar events --from "monday" --to "friday"
gog calendar events --from "yesterday" --to "tomorrow"
```

### Search and Filter

**Text search:**
```bash
gog calendar search "meeting"
gog calendar search "standup" --from 2026-03-01
```

**Query filter:**
```bash
gog calendar events --query "team meeting"
```

**Extended properties:**
```bash
# Filter by private property
gog calendar events --private-prop-filter "key=value"

# Filter by shared property
gog calendar events --shared-prop-filter "project=alpha"
```

### List from Multiple Calendars

```bash
# List events from all calendars
gog calendar events --all

# List from specific calendars
gog calendar events calendar1@example.com,calendar2@example.com
```

### Pagination

```bash
# Limit results
gog calendar events --max 50

# Get specific page
gog calendar events --page 2 --max 25

# Get all events (no pagination)
gog calendar events --all
```

## Creating Events

### Basic Event Creation

```bash
# Simple event
gog calendar create primary --summary "Team Meeting" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"

# With description
gog calendar create primary --summary "Project Review" --description "Q1 project review meeting" --from "2026-03-05T14:00:00+09:00" --to "2026-03-05T15:00:00+09:00"

# With location
gog calendar create primary --summary "Client Meeting" --location "Conference Room A" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"
```

### All-Day Events

```bash
# Single day
gog calendar create primary --summary "Holiday" --from "2026-03-10" --to "2026-03-11" --all-day

# Multiple days
gog calendar create primary --summary "Conference" --from "2026-03-15" --to "2026-03-18" --all-day
```

### Attendees

```bash
# Single attendee
gog calendar create primary --summary "1:1 Meeting" --attendees "alice@example.com" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T10:30:00+09:00"

# Multiple attendees
gog calendar create primary --summary "Team Sync" --attendees "alice@example.com,bob@example.com,charlie@example.com" --from "2026-03-05T14:00:00+09:00" --to "2026-03-05T15:00:00+09:00"
```

### Google Meet Integration

```bash
# Create event with Google Meet
gog calendar create primary --summary "Remote Meeting" --with-meet --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"

# With attendees and Meet
gog calendar create primary --summary "Team Call" --attendees "team@example.com" --with-meet --from "2026-03-05T14:00:00+09:00" --to "2026-03-05T15:00:00+09:00"
```

### Reminders

```bash
# Popup reminder 30 minutes before
gog calendar create primary --summary "Meeting" --reminder "popup:30m" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"

# Email reminder 1 day before
gog calendar create primary --summary "Important Meeting" --reminder "email:1d" --from "2026-03-10T10:00:00+09:00" --to "2026-03-10T11:00:00+09:00"

# Multiple reminders
gog calendar create primary --summary "Critical Meeting" --reminder "popup:30m" --reminder "email:1d" --from "2026-03-10T10:00:00+09:00" --to "2026-03-10T11:00:00+09:00"
```

### Event Colors

```bash
# Set event color (1-11)
gog calendar create primary --summary "Important" --event-color 11 --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"

# View available colors
gog calendar colors
```

### Visibility and Transparency

**Visibility:**
```bash
# Default visibility
gog calendar create primary --summary "Meeting" --visibility default

# Public event
gog calendar create primary --summary "Public Event" --visibility public

# Private event
gog calendar create primary --summary "Private Meeting" --visibility private

# Confidential event
gog calendar create primary --summary "Confidential" --visibility confidential
```

**Transparency (busy/free):**
```bash
# Mark as busy (default)
gog calendar create primary --summary "Meeting" --transparency busy

# Mark as free
gog calendar create primary --summary "Optional Event" --transparency free
```

### Guest Permissions

```bash
# Allow guests to invite others
gog calendar create primary --summary "Meeting" --guests-can-invite --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"

# Allow guests to modify event
gog calendar create primary --summary "Collaborative Meeting" --guests-can-modify --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"

# Allow guests to see other guests
gog calendar create primary --summary "Team Meeting" --guests-can-see-others --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"
```

### Notification Settings

```bash
# Send updates to all attendees
gog calendar create primary --summary "Meeting" --send-updates all --attendees "team@example.com" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"

# Send updates only to external attendees
gog calendar create primary --summary "Meeting" --send-updates externalOnly --attendees "external@example.com,internal@mycompany.com" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"

# Don't send updates
gog calendar create primary --summary "Meeting" --send-updates none --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"
```

### Extended Properties

```bash
# Add private property
gog calendar create primary --summary "Meeting" --private-prop "project=alpha" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"

# Add shared property
gog calendar create primary --summary "Meeting" --shared-prop "category=engineering" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"

# Multiple properties
gog calendar create primary --summary "Meeting" --private-prop "project=alpha" --shared-prop "category=engineering" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T11:00:00+09:00"
```

## Special Event Types

### Focus Time

```bash
# Basic focus time
gog calendar focus-time primary --summary "Deep Work" --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T12:00:00+09:00"

# With auto-decline
gog calendar focus-time primary --summary "Focus Time" --auto-decline all --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T12:00:00+09:00"

# Auto-decline only new invitations
gog calendar focus-time primary --summary "Focus Time" --auto-decline new --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T12:00:00+09:00"

# Set chat status
gog calendar focus-time primary --summary "Focus Time" --chat-status doNotDisturb --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T12:00:00+09:00"

# With decline message
gog calendar focus-time primary --summary "Focus Time" --auto-decline all --decline-message "In focus time, will respond later" --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T12:00:00+09:00"
```

### Out of Office

```bash
# Basic out of office
gog calendar out-of-office primary --summary "Vacation" --from "2026-03-10" --to "2026-03-15" --all-day

# With auto-decline
gog calendar out-of-office primary --summary "Out of Office" --auto-decline --from "2026-03-10" --to "2026-03-15" --all-day

# With decline message
gog calendar out-of-office primary --summary "Vacation" --auto-decline --decline-message "Out of office, will respond when I return" --from "2026-03-10" --to "2026-03-15" --all-day

# Partial day out of office
gog calendar out-of-office primary --summary "Doctor Appointment" --from "2026-03-05T14:00:00+09:00" --to "2026-03-05T16:00:00+09:00"
```

### Working Location

```bash
# Work from home
gog calendar working-location primary --type home --from "2026-03-05" --to "2026-03-06" --all-day

# Work from office
gog calendar working-location primary --type office --from "2026-03-06" --to "2026-03-07" --all-day

# Office with details
gog calendar working-location primary --type office --office-label "Main Office" --building-id "Building-A" --floor-id "3" --desk-id "3-42" --from "2026-03-06" --to "2026-03-07" --all-day

# Custom location
gog calendar working-location primary --type custom --custom-label "Client Site" --from "2026-03-07" --to "2026-03-08" --all-day
```

## Updating Events

### Basic Updates

```bash
# Update summary
gog calendar update primary <eventId> --summary "Updated Title"

# Update time
gog calendar update primary <eventId> --from "2026-03-05T15:00:00+09:00" --to "2026-03-05T16:00:00+09:00"

# Update description
gog calendar update primary <eventId> --description "Updated description"

# Update location
gog calendar update primary <eventId> --location "New Conference Room"
```

### Update Attendees

```bash
# Replace all attendees
gog calendar update primary <eventId> --attendees "alice@example.com,bob@example.com"

# Add attendees (keeping existing)
gog calendar update primary <eventId> --add-attendee "charlie@example.com"
gog calendar update primary <eventId> --add-attendee "dave@example.com" --add-attendee "eve@example.com"
```

### Update Notifications

```bash
# Send updates to all
gog calendar update primary <eventId> --send-updates all --summary "Updated Meeting"

# Send to external only
gog calendar update primary <eventId> --send-updates externalOnly --summary "Updated Meeting"

# Don't send updates
gog calendar update primary <eventId> --send-updates none --summary "Updated Meeting"
```

## Recurring Events

### Create Recurring Events

**Weekly recurring:**
```bash
# Every week on Tuesday
gog calendar create primary --summary "Weekly Sync" --rrule "FREQ=WEEKLY;BYDAY=TU" --from "2026-03-05T10:00:00+09:00" --to "2026-03-05T10:30:00+09:00"

# Every week on Monday and Wednesday
gog calendar create primary --summary "Standup" --rrule "FREQ=WEEKLY;BYDAY=MO,WE" --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T09:15:00+09:00"
```

**Daily recurring:**
```bash
# Every day
gog calendar create primary --summary "Daily Standup" --rrule "FREQ=DAILY" --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T09:15:00+09:00"

# Every weekday
gog calendar create primary --summary "Morning Review" --rrule "FREQ=DAILY;BYDAY=MO,TU,WE,TH,FR" --from "2026-03-05T08:00:00+09:00" --to "2026-03-05T08:30:00+09:00"
```

**Monthly recurring:**
```bash
# Every month on the 15th
gog calendar create primary --summary "Monthly Review" --rrule "FREQ=MONTHLY;BYMONTHDAY=15" --from "2026-03-15T14:00:00+09:00" --to "2026-03-15T15:00:00+09:00"

# First Monday of every month
gog calendar create primary --summary "Monthly Planning" --rrule "FREQ=MONTHLY;BYDAY=1MO" --from "2026-03-03T10:00:00+09:00" --to "2026-03-03T11:00:00+09:00"
```

**With end date:**
```bash
# Until specific date
gog calendar create primary --summary "Project Standup" --rrule "FREQ=DAILY;UNTIL=20260331T000000Z" --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T09:15:00+09:00"

# For specific count
gog calendar create primary --summary "Training Sessions" --rrule "FREQ=WEEKLY;COUNT=10" --from "2026-03-05T14:00:00+09:00" --to "2026-03-05T16:00:00+09:00"
```

### Update Recurring Events

**Update scope:**
```bash
# Update single instance
gog calendar update primary <eventId> --scope single --summary "Updated Title"

# Update this and future instances
gog calendar update primary <eventId> --scope future --summary "Updated Title"

# Update all instances
gog calendar update primary <eventId> --scope all --summary "Updated Title"
```

**Update specific instance:**
```bash
# Update instance with original start time
gog calendar update primary <eventId> --original-start "2026-03-12T10:00:00+09:00" --summary "Special Session"
```

### Delete Recurring Events

```bash
# Delete single instance
gog calendar delete primary <eventId> --scope single

# Delete this and future instances
gog calendar delete primary <eventId> --scope future

# Delete all instances
gog calendar delete primary <eventId> --scope all
```

## Event Responses

### Respond to Invitations

```bash
# Accept invitation
gog calendar respond primary <eventId> --status accepted

# Decline invitation
gog calendar respond primary <eventId> --status declined

# Tentatively accept
gog calendar respond primary <eventId> --status tentative

# Mark as needs action
gog calendar respond primary <eventId> --status needsAction
```

### Respond with Comment

```bash
# Accept with comment
gog calendar respond primary <eventId> --status accepted --comment "Looking forward to it"

# Decline with reason
gog calendar respond primary <eventId> --status declined --comment "Have a conflict"

# Tentative with note
gog calendar respond primary <eventId> --status tentative --comment "Will confirm by tomorrow"
```

### Alias Command

```bash
# Using rsvp alias
gog calendar rsvp primary <eventId> --status accepted
```

## Freebusy and Conflicts

### Check Freebusy

```bash
# Check single calendar
gog calendar freebusy primary --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T17:00:00+09:00"

# Check multiple calendars
gog calendar freebusy "calendar1@example.com,calendar2@example.com" --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T17:00:00+09:00"

# Get as JSON
gog calendar freebusy primary --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T17:00:00+09:00" --json
```

### Find Conflicts

```bash
# Find conflicts in calendar
gog calendar conflicts primary --from "2026-03-05T09:00:00+09:00" --to "2026-03-05T17:00:00+09:00"

# Find conflicts for specific event
gog calendar conflicts primary --event-id <eventId>
```

### Propose New Time

```bash
# Generate URL to propose new meeting time
gog calendar propose-time primary <eventId> --from "2026-03-06T10:00:00+09:00" --to "2026-03-06T11:00:00+09:00"
```

## Calendar Management

### List Calendars

```bash
# List all calendars
gog calendar calendars

# List with pagination
gog calendar calendars --max 50 --page 1

# Get all calendars
gog calendar calendars --all

# Get as JSON
gog calendar calendars --json
```

### Access Control (ACL)

```bash
# List calendar ACL
gog calendar acl list primary

# Add user with specific role
gog calendar acl create primary --user "user@example.com" --role writer

# Remove user access
gog calendar acl delete primary <aclId>
```

## Team Calendars

### View Team Events

```bash
# Show events for all members of a Google Group
gog calendar team group@example.com --from "2026-03-05" --to "2026-03-12"

# Get as JSON
gog calendar team group@example.com --from "2026-03-05" --to "2026-03-12" --json
```

### List Workspace Users

```bash
# List all users in workspace
gog calendar users

# Get as JSON
gog calendar users --json
```

## Advanced Features

### Server Time

```bash
# Get server time
gog calendar time

# Get as JSON
gog calendar time --json
```

### Event Colors

```bash
# View available colors
gog calendar colors

# Get color IDs
gog calendar colors --json
```

### Get Event Details

```bash
# Get specific event
gog calendar event primary <eventId>

# Get as JSON
gog calendar event primary <eventId> --json

# Using get alias
gog calendar get primary <eventId>
```

### Delete Events

```bash
# Delete event
gog calendar delete primary <eventId>

# Delete without confirmation
gog calendar delete primary <eventId> -y

# Delete with notification
gog calendar delete primary <eventId> --send-updates all
```

## Tips and Best Practices

### Time Formats

- Use ISO 8601 format for precise times: `2026-03-05T10:00:00+09:00`
- Use date-only format for all-day events: `2026-03-05`
- Use relative keywords for convenience: `today`, `tomorrow`, `monday`, `tuesday`, etc.
- Note: Expressions like "3 days ago" or "2 weeks from now" are NOT supported

### Performance

- Use `--json --results-only` for scripting
- Use `--max` to limit results when appropriate
- Use specific time ranges to reduce data transfer

### Safety

- Use `--dry-run` before destructive operations
- Use `--scope single` when updating recurring events to avoid unintended changes
- Always specify `--send-updates` explicitly when modifying events with attendees

### Automation

- Use `--no-input` to prevent prompts in scripts
- Use `-y` to skip confirmations in trusted scripts
- Use `--json` output with `jq` for parsing

### Multi-Calendar

- Use calendar email address instead of "primary" for specific calendars
- Use `--all` flag to query across all calendars
- Check `gog calendar calendars` to see available calendars
