# Integration Framework Protocol

Defines consistent behavior for all external tool integrations.

---

## Core Principle

**The agent handles technical complexity. Users provide only credentials.**

Users never:
- Edit configuration files
- Run terminal commands
- See JSON, environment variables, or MCP references
- Troubleshoot technical issues themselves

---

## Integration Lifecycle

```
DISCOVERY → SETUP → ACTIVE → SYNC ↔ ERROR → RECOVERY
                      ↓
                  DISCONNECT
```

| State | Description |
|-------|-------------|
| Discovery | User requests integration, agent finds appropriate server |
| Setup | Credential collection, server installation, verification |
| Active | Integration configured and working |
| Sync | Bidirectional data synchronization |
| Error | Connection failed, using local data |
| Recovery | Connection restored, prompting sync |
| Disconnect | User removes integration |

---

## Required Hooks

Every integration MUST implement these three hooks.

### 1. Setup Hook

**Trigger:** "Add [service] integration"

**Responsibilities:**
1. Verify prerequisites (Node.js, etc.)
2. Discover MCP server from trusted sources
3. Collect credentials with step-by-step guidance
4. Configure MCP (claude mcp add or JSON update)
5. Verify connection works
6. Update INTEGRATIONS.md

**User Experience:**
- Guided prompts: "First, go to..."
- Progress messages: "Setting up connection..."
- Clear outcome: "Connected!" or "Couldn't connect. Let's try..."

**Never expose:**
- npm/npx commands
- JSON configuration syntax
- Environment variable names
- File paths

---

### 2. Sync Hook

**Trigger:** "Sync [service]", automatic on session start

**Responsibilities:**
1. Pull changes from external service
2. Map external status to local format
3. Push local changes to external service
4. Handle conflicts (see Conflict Resolution)
5. Update INTEGRATIONS.md sync timestamp

**User Experience:**
- Brief summary: "Synced 5 tasks (3 updated, 2 new)"
- Conflict notification: "Conflict on [task]. Kept Trello version."
- No changes: "[Service] is up to date."

**Never expose:**
- API responses
- Raw data structures
- Sync algorithms

---

### 3. Error Hook

**Trigger:** Any integration failure

**Responsibilities:**
1. Log error (sanitized - no credentials)
2. Display inline notice to user
3. One silent retry (5-second delay)
4. If still failing, enter local-only mode
5. Monitor for recovery
6. On recovery, prompt for sync

**User Experience:**
- Notice: "[Service] unavailable - working with local data"
- Recovery: "[Service] is back. Sync pending changes?"

**Never expose:**
- Error codes or technical messages
- API key references
- Stack traces

---

## Optional Hooks

### Status Hook

**Trigger:** "Check integrations"

**Returns:**
- Connection state (connected/unavailable/not configured)
- Last successful sync time
- Pending changes count (if any)

---

### Disconnect Hook

**Trigger:** "Remove [service] integration"

**Behavior:**
1. Remove MCP configuration
2. Update INTEGRATIONS.md to "Not configured"
3. Confirm: "Trello integration removed. Your local files are unchanged."

---

### Conflict Resolution Hook

**Default behavior:** External wins on true conflict.

Override if the service needs custom handling.

---

## Graceful Degradation Protocol

When an integration fails, the system continues working with local data.

**Step-by-step:**

1. **Detect failure** - Connection timeout, auth error, service unavailable
2. **Log error** - To ops/logs/agent-actions.log (credentials masked as `***`)
3. **Notify user** - Inline: "[Service] unavailable - working with local data"
4. **Silent retry** - One attempt after 5 seconds
5. **Local-only mode** - If retry fails, continue with local files only
6. **Monitor recovery** - Periodic health checks (every 60 seconds)
7. **Prompt on recovery** - "[Service] is back. Sync pending changes?"

**Circuit Breaker States:**

| State | Behavior |
|-------|----------|
| CLOSED | Normal operation, requests flow through |
| OPEN | Service failing, skip requests, use local data |
| HALF-OPEN | Periodic test requests to check recovery |

---

## Session Startup

On every session start:

1. Read INTEGRATIONS.md for configured integrations
2. For each configured integration:
   - Quick health check (3-second timeout)
   - Note status
3. Display summary: `Integrations: Trello (connected) | Calendar (not configured)`

**If integration unavailable at startup:**
- Note in status: `Trello (unavailable - using local data)`
- Don't block session startup
- Monitor for recovery

**If no integrations configured:**
- Skip silently (integrations are optional)

---

## Conflict Resolution

Conflicts occur when both local and external change the same item since last sync.

**Default Resolution: External Wins**

Rationale: External tool represents team's shared state. Local files preserve context separately.

**Process:**
1. Detect conflict (both timestamps newer than last sync)
2. Keep external version for synced fields (status, due date)
3. Preserve local-only content (notes, context)
4. Notify user: "Conflict on [task]. Kept Trello version: Done"

**What's preserved locally regardless:**
- Detailed notes and context
- Related file links
- Full history

---

## Credential Security

**Rules:**
- Never log full credentials
- Use `***` masking in logs: `TRELLO_TOKEN=***`
- Credentials stored in agent's secure configuration (environment variables)
- User provides credentials once, agent handles storage

**Logging example:**
```json
{
  "timestamp": "2026-01-25T10:30:00Z",
  "action": "integration_error",
  "integration": "trello",
  "error": "auth_failed",
  "credentials": "TRELLO_TOKEN=***"
}
```

---

## Rate Limit Handling

External services limit request frequency.

**When rate limited:**
1. Detect 429 response
2. Notify user: "Rate limited. Try again in a few minutes."
3. Queue pending operations (don't lose them)
4. Fall back to local-only mode
5. Retry after delay

**Never:**
- Silently drop operations
- Retry immediately (makes it worse)
- Show technical rate limit details

---

## Rollback on Failure

Multi-step operations should be atomic.

**Before sync:**
1. Create checkpoint of local state
2. Begin sync operations

**On failure mid-operation:**
1. Rollback to checkpoint
2. Notify user: "Sync failed. No changes were made."

**User sees:** Clean failure, no partial state.

---

## Trusted Sources

Agent searches these sources for MCP servers:

1. **Official MCP Registry** - registry.modelcontextprotocol.io
2. **npm verified publishers** - Packages from verified npm accounts
3. **Known good list** - Maintained list of tested packages

**Never install from:**
- Unverified npm packages
- Unknown GitHub repositories
- User-provided URLs without verification

---

## Adding New Integrations

1. Copy [templates/INTEGRATION_TEMPLATE.md](../../templates/INTEGRATION_TEMPLATE.md)
2. Implement three required hooks (setup, sync, error)
3. Document credentials and rate limits
4. Test with actual service
5. Add to trusted sources list

---

## Related

- [INTEGRATIONS.md](../../INTEGRATIONS.md) - Current integration status
- [docs/INTEGRATION_GUIDE.md](../INTEGRATION_GUIDE.md) - User-facing guide
- [templates/INTEGRATION_TEMPLATE.md](../../templates/INTEGRATION_TEMPLATE.md) - Skill template
