# Phase 8: Integration Patterns - Research

**Researched:** 2026-01-25
**Domain:** MCP (Model Context Protocol), External Tool Integration, Bidirectional Sync
**Confidence:** HIGH

## Summary

This phase implements optional external tool integrations using Model Context Protocol (MCP), the open standard for connecting AI applications to external systems. MCP was introduced by Anthropic in November 2024 and donated to the Agentic AI Foundation under the Linux Foundation in December 2025, making it the industry-standard for AI-tool integrations with adoption by OpenAI, Google DeepMind, and others.

The research validates that existing community MCP servers for Trello are production-ready and well-maintained (multiple npm packages with 77,000+ stars in the official servers repo). The dual source of truth pattern (external tool = status, local repo = context) is a well-established integration architecture. Agent-driven setup is feasible since Claude Code, opencode, and other agents support MCP configuration through both CLI commands and JSON configuration files.

**Primary recommendation:** Document MCP from zero-knowledge, use existing community Trello MCP servers (do not build custom), implement graceful degradation with circuit breaker pattern, and create an integration skill framework that handles setup, sync, and error recovery.

## Standard Stack

The established tools/services for this domain:

### Core

| Component | Version/Source | Purpose | Why Standard |
|-----------|----------------|---------|--------------|
| MCP | Protocol Revision 2025-03-26 | Standard protocol for AI-tool integration | Industry standard (Anthropic, OpenAI, Google, Microsoft) |
| @delorenj/mcp-server-trello | npm latest | Trello MCP server | Official MCP Registry, full CRUD, rate limiting built-in |
| Official MCP Registry | registry.modelcontextprotocol.io | Server discovery | Community-owned, verified every 5 seconds |

### Supporting

| Component | Purpose | When to Use |
|-----------|---------|-------------|
| JSON-RPC 2.0 | MCP transport layer | Underlying protocol (abstracted from users) |
| Claude Code CLI | `claude mcp add/list/remove` | Adding servers to Claude Code |
| opencode.json | MCP configuration | Adding servers to opencode |
| .mcp.json | Project-scoped config | Team-shared MCP configuration |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| @delorenj/mcp-server-trello | @iflow-mcp/trello-mcp-server | Both viable; delorenj is in official registry |
| stdio transport | HTTP/SSE transport | HTTP for remote servers, stdio for local npm packages |
| Manual JSON editing | CLI commands | CLI is user-friendly, JSON for automation |

### No Installation Required by Users

Per CONTEXT.md decision: "User never manually edits JSON config files or runs npm commands directly." The agent handles all installation using:

**Claude Code:**
```bash
claude mcp add --transport stdio trello -- npx -y @delorenj/mcp-server-trello
```

**opencode:**
```json
{
  "mcp": {
    "trello": {
      "type": "local",
      "command": ["npx", "-y", "@delorenj/mcp-server-trello"],
      "environment": {
        "TRELLO_API_KEY": "...",
        "TRELLO_TOKEN": "..."
      }
    }
  }
}
```

## Architecture Patterns

### Recommended Configuration Structure

```
project-root/
  INTEGRATIONS.md           # User-facing integration status and config reference
  ops/
    TASK_SYNC_PLAYBOOK.md   # Operational guide for sync workflows
    voice/                  # Existing voice profiles
    logs/                   # Existing logging
  docs/
    INTEGRATION_GUIDE.md    # Comprehensive integration documentation
  templates/
    INTEGRATION_TEMPLATE.md # Template for adding new integrations
  .github/skills/
    trello-sync/SKILL.md    # Trello integration skill
    add-integration/SKILL.md # Guided setup skill
```

### Pattern 1: Dual Source of Truth

**What:** External tool manages status tracking (completed/blocked/active), local repo stores context and history.

**When to use:** Any task management integration (Trello, Jira, Asana, etc.)

**Implementation:**
```
EXTERNAL TOOL (Trello)           LOCAL REPO (Markdown)
-------------------              -------------------
Card status                      Task context/notes
Due dates                        Background information
Assignments                      Related files/links
Labels                           Full history
Comments (brief)                 Extended discussion
Checklists                       Implementation details
```

**Sync Direction:**
- External -> Local: Status changes, assignment updates, due date changes
- Local -> External: Task completion, new comments, checklist items

**Conflict Resolution:** External wins on actual conflict (both changed same item). Non-conflicting changes sync bidirectionally.

### Pattern 2: Graceful Degradation with Circuit Breaker

**What:** When integration unavailable, work continues with local data; reconnection prompts sync.

**When to use:** All external integrations.

**Implementation:**
```
1. Integration unavailable detected
2. Log error (ops/logs/agent-actions.log)
3. Display inline notice: "Trello sync unavailable, working with local data"
4. One silent retry (5-second delay)
5. If still unavailable, continue with local-only mode
6. On reconnection, prompt: "Trello available again. Sync pending changes?"
```

**Circuit Breaker States:**
- CLOSED: Normal operation, requests flow through
- OPEN: Service failing, requests bypass (use local data)
- HALF-OPEN: Periodic test requests to check recovery

### Pattern 3: Agent-Driven Setup

**What:** Agent handles all technical complexity of integration setup; user only confirms choices and provides credentials.

**When to use:** Adding any new integration.

**Implementation Flow:**
```
1. User: "Add Trello integration"
2. Agent: Searches for available MCP servers (web search trusted sources)
3. Agent: Presents options: "Found Trello MCP server. This will let you sync tasks..."
4. User: Confirms
5. Agent: Checks prerequisites (node.js/bun installed)
6. If missing: "You'll need Node.js. Want me to help install it?"
7. Agent: Adds MCP server configuration (claude mcp add or updates JSON)
8. Agent: Guides credential collection (Trello API key, token)
9. Agent: Tests connection automatically
10. Agent: Reports success/failure
```

### Anti-Patterns to Avoid

- **Exposing MCP/JSON to users:** Never mention MCP, JSON-RPC, or config files to non-technical users. Say "integration" or "connection."
- **Manual config editing:** Never ask users to edit configuration files directly.
- **Silent failures:** Always notify user when integration is unavailable.
- **Full database sync:** Don't try to replicate entire external tool state locally. Sync only what's needed.
- **Polling without rate limits:** Always respect API rate limits. Use webhooks when available.

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Trello API integration | Custom Trello client | MCP server npm package | Rate limiting, auth, error handling built-in |
| MCP server protocol | Custom JSON-RPC handler | Existing MCP SDK | Protocol compliance, transport handling |
| Retry with backoff | Simple retry loop | Circuit breaker pattern | Prevents cascading failures, respects rate limits |
| Credential storage | Custom secret storage | Environment variables | Standard pattern, works across agents |

**Key insight:** MCP servers handle the complexity of external API integration. The agent's job is configuration, not implementation.

## Common Pitfalls

### Pitfall 1: Assuming MCP Knowledge

**What goes wrong:** Documentation starts with "Configure your MCP server in ~/.claude.json" - user has no idea what this means.

**Why it happens:** Developer-focused documentation, not user-focused.

**How to avoid:** Explain MCP from scratch: "Integrations let your AI assistant connect to other tools like Trello. The connection happens automatically - you just provide your Trello account details."

**Warning signs:** Documentation mentions JSON, config files, environment variables without explanation.

### Pitfall 2: Mid-Operation Failures Without Rollback

**What goes wrong:** Sync operation fails halfway - some tasks updated in Trello, some not. State is inconsistent.

**Why it happens:** No transaction-like behavior for multi-step operations.

**How to avoid:**
1. Collect all changes before applying
2. Create checkpoint before sync
3. Apply all or nothing (rollback on failure)
4. Report partial progress if rollback impossible

**Warning signs:** User sees "Sync failed" but some changes already applied.

### Pitfall 3: Rate Limit Crashes

**What goes wrong:** Agent makes many rapid API calls, hits rate limit, operation fails.

**Why it happens:** Trello limits: 300 requests/10s per API key, 100 requests/10s per token.

**How to avoid:**
1. Use MCP servers with built-in rate limiting
2. Batch operations where possible
3. Fail gracefully with "Rate limited. Try again in a few minutes."

**Warning signs:** 429 errors, operation succeeds sometimes but not others.

### Pitfall 4: Credential Exposure in Logs

**What goes wrong:** API keys/tokens appear in logs or error messages.

**Why it happens:** Insufficient sanitization of error details.

**How to avoid:**
1. Never log full credentials
2. Log "TRELLO_TOKEN=***" not actual value
3. Check error messages before displaying

**Warning signs:** Full API keys visible in ops/logs/agent-actions.log.

### Pitfall 5: Sync Loops

**What goes wrong:** Agent syncs local->external, then external->local picks up the same change, triggering another local->external sync.

**Why it happens:** No change origin tracking.

**How to avoid:**
1. Track change source (local vs external)
2. Use sync tokens/timestamps to detect self-originated changes
3. Only sync genuine changes from each source

**Warning signs:** Same task appears multiple times, infinite sync cycles.

## Code Examples

### Claude Code MCP Server Addition

```bash
# Source: https://code.claude.com/docs/en/mcp

# Add Trello server with environment variables
claude mcp add --transport stdio --env TRELLO_API_KEY=${TRELLO_API_KEY} --env TRELLO_TOKEN=${TRELLO_TOKEN} trello -- npx -y @delorenj/mcp-server-trello

# Verify installation
claude mcp list

# Check status (inside Claude Code)
/mcp
```

### opencode MCP Configuration

```json
// Source: https://opencode.ai/docs/mcp-servers/

{
  "mcp": {
    "trello": {
      "type": "local",
      "command": ["npx", "-y", "@delorenj/mcp-server-trello"],
      "enabled": true,
      "environment": {
        "TRELLO_API_KEY": "${TRELLO_API_KEY}",
        "TRELLO_TOKEN": "${TRELLO_TOKEN}",
        "TRELLO_BOARD_ID": "${TRELLO_BOARD_ID}"
      }
    }
  }
}
```

### Graceful Degradation Logging

```json
// Source: docs/protocols/action-logging.md pattern

// Integration error logged
{
  "timestamp": "2026-01-25T10:30:00Z",
  "action": "integration_error",
  "integration": "trello",
  "details": {
    "error_type": "connection_failed",
    "retry_count": 1,
    "fallback": "local_only",
    "user_notified": true
  }
}

// Integration recovered
{
  "timestamp": "2026-01-25T10:35:00Z",
  "action": "integration_recovered",
  "integration": "trello",
  "details": {
    "pending_sync_items": 3,
    "user_prompted": true
  }
}
```

### Trello Authentication URL Generation

```bash
# Source: https://developer.atlassian.com/cloud/trello/guides/rest-api/api-introduction/

# User gets API key from: https://trello.com/power-ups/admin (create Power-Up first)
# Then generate token with:
https://trello.com/1/authorize?expiration=never&name=AgentPM&scope=read,write&response_type=token&key={API_KEY}
```

### Integration Status Display (Session Startup)

```markdown
# Session Startup Integration Status

**Integrations:**
- Trello: Connected (last sync: 2 minutes ago)
- Calendar: Not configured

---
Ready for instructions.
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Custom API clients | MCP servers | Nov 2024 | Standardized integration pattern |
| SSE transport | HTTP Streamable | 2025 | SSE deprecated, HTTP preferred |
| Manual JSON config | CLI commands | 2025 | User-friendly setup |
| Single registry | Multiple registries | Sep 2025 | Easier server discovery |

**Deprecated/outdated:**
- SSE (Server-Sent Events) transport: Deprecated. Use HTTP or stdio instead.
- Manual ~/.claude.json editing: CLI commands (`claude mcp add`) are preferred.
- Custom MCP server building: Extensive community servers exist; build only if truly needed.

## Open Questions

Things that couldn't be fully resolved:

1. **Exact MCP server recommendations for Trello**
   - What we know: Multiple servers exist (@delorenj, @iflow-mcp, mcp-trello)
   - What's unclear: Which is most maintained/reliable for this specific use case
   - Recommendation: Start with @delorenj/mcp-server-trello (in official registry)

2. **Hook requirements for integration framework**
   - What we know: Skills need setup, sync, and error hooks
   - What's unclear: Exact required vs optional hooks for custom integrations
   - Recommendation: Make sync and error-handling required; setup optional (can be manual)

3. **Exact format of trusted sources list**
   - What we know: Official npm, verified publishers, curated list in docs
   - What's unclear: How to format/maintain the curated list
   - Recommendation: Simple markdown table in INTEGRATION_GUIDE.md with links

## Sources

### Primary (HIGH confidence)
- Claude Code MCP Docs: https://code.claude.com/docs/en/mcp - Full configuration reference
- MCP Official Site: https://modelcontextprotocol.io/ - Protocol specification and concepts
- MCP Official Registry: https://registry.modelcontextprotocol.io/ - Server discovery
- opencode MCP Docs: https://opencode.ai/docs/mcp-servers/ - Configuration format
- Trello MCP Server (delorenj): https://github.com/delorenj/mcp-server-trello - Reference implementation

### Secondary (MEDIUM confidence)
- Trello API Docs: https://developer.atlassian.com/cloud/trello/guides/rest-api/api-introduction/ - Authentication flow
- MCP Error Handling Guide: https://mcpcat.io/guides/error-handling-custom-mcp-servers/ - Best practices
- GitHub MCP Registry: https://github.blog/ai-and-ml/github-copilot/meet-the-github-mcp-registry-the-fastest-way-to-discover-mcp-servers/ - Server discovery

### Tertiary (LOW confidence)
- Bidirectional sync patterns: https://medium.com/@prayagvakharia/the-architects-guide-to-data-integration-patterns-migration-broadcast-bi-directional-a4c92b5f908d - General patterns
- MCP Fallback strategies: https://www.byteplus.com/en/topic/541948 - Degradation approaches

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - Official documentation, established ecosystem
- Architecture: HIGH - Well-documented patterns, existing implementations
- Pitfalls: MEDIUM - Based on general integration experience, some MCP-specific

**Research date:** 2026-01-25
**Valid until:** 2026-02-25 (30 days - stable ecosystem)

---

*Phase: 08-integration-patterns*
*Research completed: 2026-01-25*
