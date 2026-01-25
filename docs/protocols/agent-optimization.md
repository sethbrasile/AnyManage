# Agent Optimization

Guidance for adapting behavior based on agent capabilities.

## Design Philosophy

This system is designed to be **agent-agnostic**. The core behavior in `INSTRUCTIONS.md` works with any AI agent that can read and write files. Agent-specific wrappers add enhancements but aren't required.

## Capability Tiers

Different agents have different capabilities. The system adapts gracefully.

### Tier 1: Full Functionality

**Agents:** Claude Code with MCP servers, similar full-featured environments

**Capabilities:**
- File read/write
- Git operations
- External API calls (via MCP)
- Background processes
- Rich tool access

**Behavior:**
- Automate everything possible
- Use git for all changes (invisible to user)
- Connect to external tools when configured
- Preload slightly more context (better token budget)

### Tier 2: Core Functionality

**Agents:** opencode, codex, basic Claude Code without MCP

**Capabilities:**
- File read/write
- Basic git (may be limited)
- No external APIs
- Standard tool access

**Behavior:**
- Automate file operations
- Git when available, skip gracefully when not
- Offer manual alternatives for external actions
- Minimal context preload

### Tier 3: Limited Functionality

**Agents:** Read-only environments, chat-only interfaces

**Capabilities:**
- File read (maybe)
- No file write
- No git
- No external access

**Behavior:**
- Generate instructions for user to execute
- Provide copy-paste ready content
- Guide user through manual steps
- Focus on analysis and recommendations

## Graceful Degradation

When an agent can't perform an action, offer alternatives:

### Example: Can't Update External Tool

**Full capability:**
```
Done! I've updated the Trello card for Acme Corp
with the new due date.
```

**Limited capability:**
```
I can't update Trello directly, but here's what
needs to change:

Card: "Acme Corp - Q1 Review"
New Due Date: February 15, 2026

Would you like me to create a reminder note
so you don't forget to update it manually?
```

### Example: Can't Run Git

**Full capability:**
```
Changes saved and committed.
```

**Limited capability:**
```
I've saved the changes to the file.

Note: I wasn't able to commit to git automatically.
If you want to save this version, you might want
to ask your IT person to help commit the changes.
```

## Optimization Tradeoffs

### "Agent-Optimized" vs "Identical Behavior"

There are two schools of thought:

**Agent-Optimized:**
- Each agent does what it's best at
- Claude Code preloads more, opencode stays minimal
- Better performance per-agent
- Behavior varies slightly between agents

**Identical Behavior:**
- All agents behave exactly the same
- Lowest common denominator
- Predictable across tools
- May underutilize capable agents

**This system uses Agent-Optimized** with these constraints:
- Core workflows are identical
- Only startup and optimization differs
- User experience feels consistent
- Fallbacks ensure nothing breaks

## Testing Matrix

When testing across agents, verify:

| Test | Claude Code | opencode | Cursor | codex |
|------|-------------|----------|--------|-------|
| Reads INSTRUCTIONS.md | ✓ | ✓ | ✓ | ✓ |
| Silent startup | ✓ | ✓ | ✓ | ✓ |
| Loads STATE.md | ✓ | ✓ | ✓ | ✓ |
| Natural language commands | ✓ | ✓ | ✓ | ✓ |
| Creates entity folders | ✓ | ✓ | ✓ | ? |
| Git commits (silent) | ✓ | ? | ✓ | ? |
| Offers fallbacks when limited | ✓ | ✓ | ✓ | ✓ |

Legend: ✓ = Works, ? = Test needed, ✗ = Not supported

## Agent-Specific Notes

### Claude Code
- Full MCP support for integrations
- Hooks for pre/post actions
- Slash commands via `.claude/commands/`
- Settings in `.claude/settings.json`

### opencode
- Agent definitions in `.opencode/agents/`
- May have limited git integration
- Focus on file operations

### Cursor
- Uses `.cursorrules` for instructions
- Project Rules in `.cursor/rules/`
- IDE-integrated experience

### codex / Generic
- Uses `.agent/AGENT.md`
- Capabilities vary widely
- Test before relying on features

## When to Offer Manual Fallbacks

Offer a manual alternative when:
1. Agent explicitly reports it can't do something
2. Operation requires external API access
3. User is in a limited environment
4. Operation is critical and automation is uncertain

Don't interrupt workflow for minor limitations - save them for the summary.
