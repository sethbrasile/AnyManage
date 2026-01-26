---
name: trello-sync
description: Sync tasks bidirectionally with Trello boards
triggers:
  - "sync trello"
  - "sync with trello"
  - "trello sync"
  - "update trello"
  - "pull from trello"
  - "push to trello"
  - "refresh trello"
integration:
  service: "trello"
  mcp_server: "@delorenj/mcp-server-trello"
  requires_auth: true
---

# Trello Sync

Bidirectional task synchronization with Trello boards.

---

## Overview

**What it does:**
- Pulls task status from Trello cards
- Pushes local task completions to Trello
- Keeps both sources in sync automatically

**Dual Source of Truth:**
- **Trello** = task status (done, blocked, active), due dates, assignments
- **Local files** = context, notes, history, related files

---

## Required Hooks

### Setup Hook

**Trigger:** "Add Trello integration"

**Prerequisites:**
- Node.js (for npx)

**MCP Server:** @delorenj/mcp-server-trello (Official MCP Registry)

**Credential Collection:**

Step 1: API Key
```
Go to: trello.com/power-ups/admin
Create a new Power-Up (any name, like "AnyManage")
Copy your API Key from the page
```

Step 2: Token
```
Go to this URL (with your API key):
https://trello.com/1/authorize?expiration=never&name=AgentPM&scope=read,write&response_type=token&key={API_KEY}

Click "Allow"
Copy the token shown
```

Step 3: Board Selection
```
Which Trello board should I sync with?
(Lists available boards, user selects one)
```

**Installation (agent executes silently):**

Claude Code:
```bash
claude mcp add --transport stdio \
  --env TRELLO_API_KEY=${API_KEY} \
  --env TRELLO_TOKEN=${TOKEN} \
  trello -- npx -y @delorenj/mcp-server-trello
```

opencode (update opencode.json):
```json
{
  "mcp": {
    "trello": {
      "type": "local",
      "command": ["npx", "-y", "@delorenj/mcp-server-trello"],
      "environment": {
        "TRELLO_API_KEY": "${API_KEY}",
        "TRELLO_TOKEN": "${TOKEN}"
      }
    }
  }
}
```

**Verification:**
- Call `trello_list_boards` to confirm connection
- Success: "Connected! Try 'sync trello' to see your tasks."
- Failure: Guide through credential re-entry

---

### Sync Hook

**Trigger:** "Sync trello", automatic on session start

**Pull Operations:**
1. Get cards from configured board
2. Map card status (list name) to local task checkbox
3. Map due dates to local metadata
4. Map labels to local tags

**Push Operations:**
1. Detect local task completions (checkbox changes)
2. Move cards to appropriate Trello list
3. Add comments for significant updates (optional)

**Bidirectional Mapping:**

| Trello | Local | Direction |
|--------|-------|-----------|
| Card name | Task title | Bidirectional |
| Card list (status) | Checkbox [ ]/[x] | Bidirectional |
| Due date | Metadata | Bidirectional |
| Labels | Tags | External → Local |
| Card description | Task notes | External → Local |
| Attachments | (not synced) | - |
| Comments | (not synced) | - |

**List-to-Checkbox Mapping:**
- "To Do", "Backlog" → `[ ]`
- "In Progress", "Doing" → `[-]`
- "Done", "Complete" → `[x]`
- Custom list names: configurable during setup

**Conflict Resolution:**
- Trello wins on true conflict (both changed since last sync)
- User notified: "Conflict on [task]. Kept Trello version: Done"
- Local context/notes always preserved

**User Communication:**
- Success: "Synced 5 tasks with Trello (3 updated, 2 new)"
- Partial: "Synced 3/5 tasks. 2 failed due to rate limit - try again shortly."
- No changes: "Trello is up to date."

---

### Error Hook

**Trigger:** Any Trello operation failure

**Connection Failure:**
1. Log error to ops/logs/agent-actions.log (credentials masked)
2. Display: "Trello unavailable - working with local data"
3. Silent retry (5-second delay)
4. If still failing: Continue local-only
5. Monitor for recovery (60-second checks)
6. On recovery: "Trello available. Sync pending changes?"

**Rate Limit (429):**
- Trello limits: 300 req/10s per API key, 100 req/10s per token
- Display: "Trello rate limited. Try again in a few minutes."
- Queue operations, don't drop them

**Auth Failure:**
- Display: "Trello authentication failed. Run 'add trello integration' to reconnect."

**Logging Format:**
```json
{
  "timestamp": "2026-01-25T10:30:00Z",
  "action": "trello_sync_error",
  "error_type": "connection_failed",
  "credentials": "TRELLO_TOKEN=***",
  "retry_count": 1,
  "fallback": "local_only"
}
```

---

## Optional Hooks

### Status Hook

**Trigger:** "Check integrations"

**Returns:**
- Connection state: connected / unavailable / not configured
- Last sync: timestamp
- Board: configured board name
- Pending changes: count

**Display:**
```
Trello: Connected
Last sync: 5 minutes ago
Board: Marketing Tasks
Pending: 0 changes
```

---

### Configure Hook

**Trigger:** "Configure trello"

**Options:**
- Change board: "Which board should I sync with?"
- Change list mapping: "Which list means 'done'?"
- Reconnect: Re-enter credentials

---

### Disconnect Hook

**Trigger:** "Remove trello integration"

**Behavior:**
1. Remove MCP configuration
2. Update INTEGRATIONS.md to "Not configured"
3. Confirm: "Trello integration removed. Your local files are unchanged."

---

## Credentials

| Credential | Source | User Obtains From |
|------------|--------|-------------------|
| TRELLO_API_KEY | Manual entry | trello.com/power-ups/admin (create Power-Up) |
| TRELLO_TOKEN | Authorization flow | URL with API key, click Allow |
| TRELLO_BOARD_ID | Selected during setup | User chooses from list |

---

## Rate Limits

| Limit | Value | Handling |
|-------|-------|----------|
| Per API key | 300/10s | Batch operations, queue excess |
| Per token | 100/10s | Respect limit, delay if needed |

**In practice:** Normal use rarely hits limits. Bulk operations (syncing 100+ tasks) may require pacing.

---

## Data Mapping

| Trello Field | Local Field | Sync Direction | Notes |
|--------------|-------------|----------------|-------|
| Card name | Task title | Bidirectional | Primary identifier |
| List position | Checkbox status | Bidirectional | Configurable list mapping |
| Due date | Due date metadata | Bidirectional | Format: YYYY-MM-DD |
| Labels | Tags | External → Local | Color labels become text tags |
| Description | Task notes section | External → Local | Rich context |
| Checklist items | Sub-tasks | External → Local | Nested checkboxes |
| Assigned members | (not synced) | - | Team info stays in Trello |
| Comments | (not synced) | - | Team communication stays in Trello |

---

## Session Startup

When session starts with Trello configured:

1. Quick health check (3-second timeout)
2. Report status: `Integrations: Trello (connected)`
3. If unavailable: `Integrations: Trello (unavailable - using local data)`

Don't block session startup for integration check.

---

## Related

- [docs/INTEGRATION_GUIDE.md](../../../docs/INTEGRATION_GUIDE.md) - Integration overview
- [docs/protocols/integration-framework.md](../../../docs/protocols/integration-framework.md) - Protocol details
- [ops/TASK_SYNC_PLAYBOOK.md](../../../ops/TASK_SYNC_PLAYBOOK.md) - Daily workflow guide
- [INTEGRATIONS.md](../../../INTEGRATIONS.md) - Current status
