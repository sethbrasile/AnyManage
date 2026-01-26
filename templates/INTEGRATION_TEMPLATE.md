# Integration Skill Template

Copy this template to create a new integration skill. Place in `.github/skills/[service]-sync/SKILL.md`.

---

## Frontmatter

```yaml
---
name: [service]-sync
description: Sync tasks with [Service Name]
triggers:
  - "sync [service]"
  - "sync with [service]"
  - "[service] sync"
  - "update [service]"
  - "pull from [service]"
  - "push to [service]"
integration:
  service: "[service-name]"
  mcp_server: "[npm-package-name]"
  requires_auth: true
---
```

---

## Required Hooks

Every integration MUST implement these three hooks. See [docs/protocols/integration-framework.md](../docs/protocols/integration-framework.md) for detailed behavior.

### Setup Hook

**Trigger:** "Add [service] integration"

**Behavior:**
1. Check prerequisites (Node.js installed)
2. Search for MCP server from trusted sources
3. Guide user through credential collection step-by-step
4. Install and configure MCP server (user never sees commands)
5. Test connection automatically
6. Report success or failure

**User Sees:**
- "Let's set up [Service]. First, you'll need..."
- Step-by-step credential guidance
- "Connected! Try 'sync [service]' to see your tasks."

**User Never Sees:**
- npm commands
- JSON configuration
- File paths or environment variables

**Error Handling:**
- Prerequisites missing: "You'll need Node.js. Want me to help install it?"
- Auth failure: "Those credentials didn't work. Let's try again."
- Network issues: "Couldn't connect. Check your internet and try again."

---

### Sync Hook

**Trigger:** "Sync [service]", automatic on session start (if configured)

**Behavior:**
1. Pull changes from external service
2. Update local files with external status
3. Push local changes to external service
4. Handle conflicts (external wins)
5. Update INTEGRATIONS.md with sync timestamp

**User Sees:**
- "Synced 5 tasks with [Service] (3 updated, 2 new)"
- "Conflict on [task]. Kept [Service] version: [status]"
- "[Service] is up to date."

**User Never Sees:**
- API calls or raw data
- Technical sync details

**Conflict Resolution:**
Default: External wins on true conflict (both changed since last sync). Notify user.

---

### Error Hook

**Trigger:** Any integration failure

**Behavior:**
1. Log error to ops/logs/agent-actions.log (sanitize credentials)
2. Display inline notice to user
3. One silent retry (5-second delay)
4. If still failing, continue with local data
5. Monitor for recovery
6. On recovery, prompt sync

**User Sees:**
- "[Service] unavailable - working with local data"
- "[Service] is back. Sync pending changes?"

**User Never Sees:**
- Error codes or stack traces
- API key references

---

## Optional Hooks

### Status Hook

**Trigger:** "Check integrations"

**Returns:** Connection health, last sync time, pending changes count

---

### Disconnect Hook

**Trigger:** "Remove [service] integration"

**Behavior:** Remove MCP configuration, update INTEGRATIONS.md

---

### Conflict Resolution Hook

**Override default if needed.** Default: external wins, notify user.

---

## Credentials Section

Document what credentials the user needs and where to find them.

```markdown
## Credentials

| Credential | Source | User Obtains From |
|------------|--------|-------------------|
| API_KEY | Manual entry | [URL to dashboard where user gets key] |
| TOKEN | Generated | [URL or process to generate token] |
| BOARD_ID | Selected | User chooses from list during setup |
```

**Important:** Never ask users to set environment variables. The agent handles credential storage.

---

## Rate Limits Section

Document service limits to handle gracefully.

```markdown
## Rate Limits

| Limit | Value | Handling |
|-------|-------|----------|
| Per API key | [N]/[time] | Queue excess, notify user |
| Per token | [N]/[time] | Batch operations |
```

---

## Data Mapping Section

Document what syncs where using dual source of truth pattern.

```markdown
## Data Mapping

| External Field | Local Field | Sync Direction |
|----------------|-------------|----------------|
| Task status | Checkbox [ ]/[x] | Bidirectional |
| Due date | Metadata | External → Local |
| Title | Task name | Bidirectional |
| Description | Task notes | External → Local |
| Comments | (not synced) | - |
```

**Dual Source of Truth Principle:**
- External tool = status tracking (team visibility)
- Local files = context and history (detailed notes)

---

## Example: Minimal Integration

```markdown
---
name: example-sync
description: Sync with Example Service
triggers:
  - "sync example"
integration:
  service: "example"
  mcp_server: "@example/mcp-server"
  requires_auth: true
---

## Setup

Guide user to example.com/api-keys, collect API key, install MCP server.

## Sync

Pull tasks, update local checkboxes, push completions.

## Errors

Show "Example unavailable", retry once, fall back to local.
```

---

## Checklist Before Publishing

- [ ] All three required hooks documented
- [ ] Credentials section with exact URLs
- [ ] Rate limits documented
- [ ] Data mapping table complete
- [ ] No references to JSON/config files in user-facing text
- [ ] Tested with actual service
