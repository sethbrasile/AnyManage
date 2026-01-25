# Phase 8: Integration Patterns - Context

**Gathered:** 2026-01-25
**Status:** Ready for planning

<domain>
## Phase Boundary

Users can optionally connect external tools while maintaining standalone value. This phase delivers integration documentation, a Trello reference implementation, the dual source of truth pattern, and a framework for custom integrations. The agent handles technical complexity; users just confirm choices and grant permissions.

</domain>

<decisions>
## Implementation Decisions

### Documentation Depth
- Assume zero MCP knowledge - explain what MCP is, how servers work, every concept from scratch
- No diagrams - prose explanations only, keep docs simple
- Dual source of truth explained as concept + examples (principle first, then concrete scenarios)
- Troubleshooting in both locations: quick fixes inline per integration, detailed troubleshooting centralized

### Trello Example Scope
- Bidirectional sync: pull from Trello AND push updates back (mark complete, add comments)
- Board-to-entity mapping is configurable - user chooses their approach during setup
- Document existing community MCP servers, show how to configure (don't build custom server)
- Full walkthrough for Trello authentication - step-by-step from developer portal to API key to token

### Graceful Degradation
- Inline notice when integration unavailable: "Trello sync unavailable, working with local data"
- One silent retry before falling back
- On reconnection, prompt user: "Trello available again. Sync pending changes?"
- Conflict resolution: external wins *on actual conflict* (both changed same item), otherwise bidirectional sync works normally
- Notify user on conflict resolution: "Conflict detected on [item]. Both local and Trello had changes. Kept Trello version: [status/change]."
- Show integration status on session startup (which integrations active/inactive)
- Rollback to pre-operation state on mid-operation failure (no partial results)
- Log integration errors to existing logging pattern (Phase 3)
- Rate limits: fail gracefully, note the limit, fall back to local, suggest trying later

### Custom Integration Path
- Both template + example: INTEGRATION_TEMPLATE.md for structure, Trello as reference implementation
- Mention broader integration possibilities: Calendar (Google), CRM (HubSpot), Comms (Slack) - show versatility
- Integration config lives in dedicated INTEGRATIONS.md file
- Guided setup skill: "Add integration" walks through config step by step

### Agent-Driven Setup (Critical)
- Agent does the heavy lifting - user is not a developer
- Agent searches for MCP servers from trusted sources (web search)
- Trusted sources: official npm/verified publishers + curated list in docs + user can add custom sources
- When prerequisites missing (node.js, bun), agent offers to install with permission
- Agent auto-tests installation: runs connectivity test, reports success/failure
- User never manually edits JSON config files or runs npm commands directly

### Claude's Discretion
- Hook requirements for integration framework (required vs optional hooks)
- Specific MCP server recommendations for Trello
- Exact format of curated trusted sources list

</decisions>

<specifics>
## Specific Ideas

- "Let the agent do most of the work when it comes to integrations" - non-technical users shouldn't need node.js knowledge
- Dual source of truth principle: external tool = status tracking, local repo = context and history
- The TASK_SYNC_PLAYBOOK.md concept from roadmap for operational guidance

</specifics>

<deferred>
## Deferred Ideas

None - discussion stayed within phase scope

</deferred>

---

*Phase: 08-integration-patterns*
*Context gathered: 2026-01-25*
