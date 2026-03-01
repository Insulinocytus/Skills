# Gmail Reference - Advanced Features

This document provides comprehensive documentation for advanced Gmail operations using gogcli.

## Table of Contents

- [Advanced Search Patterns](#advanced-search-patterns)
- [Sending Emails](#sending-emails)
- [Draft Management](#draft-management)
- [Label Management](#label-management)
- [Thread Operations](#thread-operations)
- [Attachment Handling](#attachment-handling)
- [Batch Operations](#batch-operations)
- [Message Tracking](#message-tracking)
- [Settings and Filters](#settings-and-filters)

## Advanced Search Patterns

### Gmail Query Syntax

Gmail search supports powerful query operators:

**Sender/Recipient:**
```bash
gog gmail search "from:alice@example.com"
gog gmail search "to:bob@example.com"
gog gmail search "cc:team@example.com"
```

**Date Filters:**
```bash
gog gmail search "after:2026/03/01"
gog gmail search "before:2026/03/31"
gog gmail search "newer_than:7d"
gog gmail search "older_than:1m"
```

**Status and Labels:**
```bash
gog gmail search "is:unread"
gog gmail search "is:starred"
gog gmail search "is:important"
gog gmail search "label:work"
```

**Attachments:**
```bash
gog gmail search "has:attachment"
gog gmail search "filename:pdf"
gog gmail search "larger:5M"
gog gmail search "smaller:1M"
```

**Combined Queries:**
```bash
gog gmail search "from:alice@example.com has:attachment newer_than:7d"
gog gmail search "subject:report is:unread after:2026/03/01"
gog gmail search "(from:alice OR from:bob) has:attachment"
```

### Search Flags

**Control result ordering:**
```bash
gog gmail search "query" --oldest=false  # Newest first (default)
gog gmail search "query" --oldest=true   # Oldest first
```

**Limit results:**
```bash
gog gmail search "query" --max 50        # Get up to 50 results
gog gmail search "query" --all           # Get all results (no pagination limit)
```

**Fail on empty:**
```bash
gog gmail search "query" --fail-empty    # Exit with error if no results
```

**Timezone:**
```bash
gog gmail search "query" --timezone "America/New_York"
```

## Sending Emails

### Basic Send

```bash
# Simple email
gog gmail send --to user@example.com --subject "Hello" --body "Message content"

# Multiple recipients
gog gmail send --to "alice@example.com,bob@example.com" --subject "Team Update"

# With CC and BCC
gog send --to alice@example.com --cc bob@example.com --bcc manager@example.com --subject "Report"
```

### HTML Emails

```bash
# HTML body
gog send --to user@example.com --subject "Newsletter" --body-html "<h1>Welcome</h1><p>Content here</p>"

# HTML from file
gog send --to user@example.com --subject "Report" --body-html "$(cat email.html)"
```

### Attachments

```bash
# Single attachment
gog send --to user@example.com --subject "Document" --attach report.pdf

# Multiple attachments
gog send --to user@example.com --subject "Files" --attach file1.pdf --attach file2.xlsx --attach image.png

# With body and attachments
gog send --to user@example.com --subject "Report" --body "Please review attached files" --attach report.pdf --attach data.csv
```

### Reply and Forward

**Reply to message:**
```bash
# Simple reply
gog gmail send --reply-to-message-id <messageId> --body "Thanks for your email"

# Reply all
gog gmail send --reply-to-message-id <messageId> --reply-all --body "Thanks everyone"

# Reply with quote
gog gmail send --reply-to-message-id <messageId> --quote --body "My response"

# Reply in thread
gog gmail send --thread-id <threadId> --reply-to-message-id <messageId> --body "Response"
```

### Sender Aliases

```bash
# Send from specific alias
gog gmail send --from "noreply@example.com" --to user@example.com --subject "Notification"
```

### Email Tracking

```bash
# Enable tracking
gog gmail send --to user@example.com --subject "Tracked" --track

# Track with split tracking pixel
gog gmail send --to user@example.com --subject "Tracked" --track-split
```

## Draft Management

### List Drafts

```bash
# List all drafts
gog gmail drafts list

# List with JSON output
gog gmail drafts list --json

# Limit results
gog gmail drafts list --max 20
```

### Create Draft

```bash
# Create basic draft
gog gmail drafts create --to user@example.com --subject "Draft" --body "Content"

# Create draft with attachments
gog gmail drafts create --to user@example.com --subject "Draft" --body "Content" --attach file.pdf

# Create HTML draft
gog gmail drafts create --to user@example.com --subject "Draft" --body-html "<h1>Title</h1>"
```

### Get Draft

```bash
# Get draft details
gog gmail drafts get <draftId>

# Get as JSON
gog gmail drafts get <draftId> --json
```

### Update Draft

```bash
# Update draft content
gog gmail drafts update <draftId> --subject "Updated Subject" --body "Updated content"

# Update recipients
gog gmail drafts update <draftId> --to "newemail@example.com"
```

### Send Draft

```bash
# Send existing draft
gog gmail drafts send <draftId>

# Send with dry-run
gog gmail drafts send <draftId> --dry-run
```

### Delete Draft

```bash
# Delete draft
gog gmail drafts delete <draftId>

# Delete without confirmation
gog gmail drafts delete <draftId> -y
```

## Label Management

### List Labels

```bash
# List all labels
gog gmail labels list

# Get label counts
gog gmail labels list --json | jq '.[] | {name: .name, total: .messagesTotal, unread: .messagesUnread}'
```

### Get Label Details

```bash
# Get specific label
gog gmail labels get <labelId>

# Get with message counts
gog gmail labels get INBOX --json | jq '{total: .messagesTotal, unread: .messagesUnread}'
```

### Create Label

```bash
# Create new label
gog gmail labels create --name "Project/SubProject"

# Create with visibility settings
gog gmail labels create --name "Archive" --label-list-visibility hide --message-list-visibility hide
```

### Modify Thread Labels

```bash
# Add single label
gog gmail thread modify <threadId> --add STARRED

# Add multiple labels
gog gmail thread modify <threadId> --add "IMPORTANT,STARRED"

# Remove labels
gog gmail thread modify <threadId> --remove UNREAD

# Add and remove simultaneously
gog gmail thread modify <threadId> --add STARRED --remove UNREAD
```

### Delete Label

```bash
# Delete label
gog gmail labels delete <labelId>

# Delete without confirmation
gog gmail labels delete <labelId> -y
```

### Common Label Operations

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

**Archive:**
```bash
gog gmail thread modify <threadId> --remove INBOX   # Archive
gog gmail thread modify <threadId> --add INBOX      # Move back to inbox
```

**Move to trash:**
```bash
gog gmail thread modify <threadId> --add TRASH      # Trash
gog gmail thread modify <threadId> --remove TRASH   # Restore from trash
```

## Thread Operations

### Get Thread

```bash
# Get thread details
gog gmail thread get <threadId>

# Get with full message bodies
gog gmail thread get <threadId> --full

# Download all attachments
gog gmail thread get <threadId> --download-attachments

# Get as JSON
gog gmail thread get <threadId> --json
```

### List Thread Attachments

```bash
# List all attachments in thread
gog gmail thread attachments <threadId>

# Get as JSON
gog gmail thread attachments <threadId> --json
```

## Attachment Handling

### Download Single Attachment

```bash
# Download attachment
gog gmail attachment <messageId> <attachmentId>

# Specify output path
gog gmail attachment <messageId> <attachmentId> --output /path/to/file.pdf

# Specify filename
gog gmail attachment <messageId> <attachmentId> --filename report.pdf
```

### Download All Thread Attachments

```bash
# Download all attachments from thread
gog gmail thread get <threadId> --download-attachments

# Download to specific directory
gog gmail thread get <threadId> --download-attachments --output-dir /path/to/dir
```

## Batch Operations

### Batch Delete

```bash
# Permanently delete multiple messages
gog gmail batch delete <messageId1> <messageId2> <messageId3>

# Delete with confirmation skip
gog gmail batch delete <messageId1> <messageId2> -y

# Delete from search results
gog gmail search "older_than:1y" --json --results-only | jq -r '.messages[].id' | xargs gog gmail batch delete
```

### Batch Modify

```bash
# Modify multiple messages
gog gmail batch modify <messageId1> <messageId2> --add STARRED

# Batch mark as read
gog gmail batch modify <messageId1> <messageId2> <messageId3> --remove UNREAD

# Batch archive
gog gmail batch modify <messageId1> <messageId2> --remove INBOX
```

### Batch Operations with Search

**Archive old emails:**
```bash
gog gmail search "older_than:1y is:read" --json --results-only | jq -r '.messages[].id' | xargs -n 50 gog gmail batch modify --remove INBOX
```

**Delete spam:**
```bash
gog gmail search "label:spam older_than:30d" --json --results-only | jq -r '.messages[].id' | xargs gog gmail batch delete -y
```

**Star important emails:**
```bash
gog gmail search "from:boss@example.com is:unread" --json --results-only | jq -r '.messages[].id' | xargs gog gmail batch modify --add STARRED
```

## Message Tracking

### Enable Tracking

```bash
# Track email opens
gog gmail send --to user@example.com --subject "Tracked Email" --track

# Track with split pixel
gog gmail send --to user@example.com --subject "Tracked Email" --track-split
```

### View Tracking Data

```bash
# Get tracking information
gog gmail track <trackingId>

# Get as JSON
gog gmail track <trackingId> --json
```

## Settings and Filters

### View Settings

```bash
# View Gmail settings
gog gmail settings

# Get specific setting
gog gmail settings get <settingName>
```

### Manage Filters

```bash
# List filters
gog gmail settings filters list

# Create filter
gog gmail settings filters create --from "notifications@example.com" --label "Notifications" --skip-inbox

# Delete filter
gog gmail settings filters delete <filterId>
```

## Messages Operations

### Search Messages

```bash
# Search messages (not threads)
gog gmail messages search "query"

# Get message details
gog gmail messages get <messageId>

# Get with specific format
gog gmail messages get <messageId> --format full
gog gmail messages get <messageId> --format metadata
gog gmail messages get <messageId> --format raw
```

### Message Headers

```bash
# Get specific headers
gog gmail messages get <messageId> --headers "From,To,Subject,Date"

# Get all headers
gog gmail messages get <messageId> --format metadata --json | jq '.payload.headers'
```

## History

### View Gmail History

```bash
# Get history since historyId
gog gmail history <historyId>

# Get as JSON
gog gmail history <historyId> --json
```

## URL Generation

### Open Gmail in Browser

```bash
# Get Gmail web URL
gog gmail url

# Get URL for specific message
gog gmail url --message <messageId>

# Get URL for specific thread
gog gmail url --thread <threadId>
```

## Tips and Best Practices

### Performance

- Use `--json --results-only` for scripting to get clean output
- Use `--max` to limit results when you don't need all messages
- Use `--oldest=false` to get newest messages first (default behavior)

### Safety

- Always use `--dry-run` before batch operations
- Use `--fail-empty` in scripts to catch unexpected empty results
- Test search queries before using in batch operations

### Automation

- Use `--no-input` to prevent prompts in automated scripts
- Use `-y` to skip confirmations in trusted scripts
- Check exit codes with `gog agent exit-codes` for error handling

### Multi-Account

- Use `-a user@example.com` to specify account
- Use `--client` to specify OAuth client for different accounts

