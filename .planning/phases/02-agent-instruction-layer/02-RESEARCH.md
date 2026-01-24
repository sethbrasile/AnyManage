# Phase 2: Agent Instruction Layer - Research

**Researched:** 2026-01-24
**Domain:** AI agent configuration, session protocols, and cross-agent instruction design
**Confidence:** HIGH

## Summary

Research focused on how modern AI coding agents (Claude Code, Cursor/Windsurf, opencode/codex) handle instruction files, session initialization, and cross-agent consistency. The ecosystem has converged on several clear patterns in 2026:

1. **AGENTS.md/CLAUDE.md as canonical format** - Over 60,000 open-source projects now use AGENTS.md as the standard instruction file. Claude Code extends this with CLAUDE.md for project memory. Both use plain markdown with no frontmatter required.

2. **Thin agent-specific wrappers** - Each agent platform has optional enhancement mechanisms (`.claude/` directory, `.cursor/rules/`, `.opencode/agents/`) but all can operate from a single shared instruction file, enabling true agent-agnostic design.

3. **Session protocol patterns** - Leading implementations use minimal auto-load (STATE.md equivalent), silent starts, and checkpoint-based recovery. The pattern of "incremental progress + clean state" enables context restoration across sessions.

4. **Hybrid command interfaces** - Natural language + slash commands is now standard. Slash commands provide power-user shortcuts while natural language ensures accessibility for non-technical users.

**Primary recommendation:** Use INSTRUCTIONS.md as single source of truth (AGENTS.md standard), with thin agent-specific wrappers in `.claude/`, `.opencode/`, and `.cursorrules` that reference the canonical file and add platform-specific enhancements (hooks, MCP servers, etc).

## Standard Stack

The established tools and patterns for agent instruction layers:

### Core Files
| File | Location | Purpose | Why Standard |
|------|----------|---------|--------------|
| AGENTS.md | Project root | Canonical agent instructions | 60,000+ OSS projects, works across all agents |
| CLAUDE.md | Project root | Project memory for Claude Code | Auto-loaded, optimized for Claude's context |
| .cursorrules | Project root (deprecated) | Legacy Cursor instructions | Still supported but migrating to .cursor/rules/ |
| STATE.md | Project root | Minimal session state | User decision: lightweight startup context |

### Supporting Infrastructure
| Component | Location | Purpose | When to Use |
|-----------|----------|---------|-------------|
| `.claude/settings.json` | Project root | Claude Code configuration | Project-specific settings, checked into git |
| `.claude/settings.local.json` | Project root | Personal Claude settings | User preferences, not committed |
| `.claude/commands/*.md` | Project root | Slash command definitions | Custom workflow automation |
| `.claude/agents/*.md` | Project root | Subagent definitions | Specialized parallel workflows |
| `.claude/rules/*.md` | Project root | Persistent skills | Context that applies conditionally |
| `.cursor/rules/*.mdc` | Project root | Cursor Project Rules | Replacing .cursorrules (2026 standard) |
| `.opencode/agents/*.md` | Project root | opencode agent definitions | Specialized agents per directory |
| `.mcp.json` | Project root | Model Context Protocol config | External integrations (optional) |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| AGENTS.md | README.md | README is for humans; AGENTS.md separates agent context |
| Single instruction file | Per-agent files only | Single source enables consistency; wrappers add enhancements |
| Slash commands | Natural language only | Hybrid approach serves both power users and non-technical users |
| Session state files | Full context reload | Lightweight STATE.md avoids token waste, scales better |

**Installation:**
No packages required - all configuration files are plain markdown/JSON created manually or via agent commands.

## Architecture Patterns

### Recommended Project Structure
```
project-root/
├── INSTRUCTIONS.md          # Canonical agent instructions (AGENTS.md standard)
├── STATE.md                 # Minimal session state (loaded on startup)
├── PAUSE.md                 # Optional pause marker (resume detection)
├── .claude/                 # Claude Code enhancements
│   ├── settings.json        # Project settings (committed)
│   ├── settings.local.json  # Personal settings (gitignored)
│   ├── commands/            # Slash commands
│   │   └── process-notes.md # Example: /process-notes command
│   ├── agents/              # Subagent definitions
│   │   └── reviewer.md      # Example: code review agent
│   └── rules/               # Conditional skills
│       └── testing.md       # Example: testing patterns
├── .cursor/                 # Cursor/Windsurf enhancements
│   └── rules/               # Project Rules (.mdc format)
│       └── coding-style.mdc # Example: coding standards
├── .opencode/               # opencode/codex enhancements
│   └── agents/              # Agent definitions
│       └── default.md       # Default agent configuration
├── .cursorrules             # Legacy format (reference INSTRUCTIONS.md)
└── .mcp.json                # Optional: external integrations
```

### Pattern 1: Single Source of Truth with Thin Wrappers
**What:** INSTRUCTIONS.md contains all core agent behavior. Agent-specific wrappers reference it and add platform enhancements.

**When to use:** For multi-agent projects requiring consistency across Claude Code, Cursor, and opencode.

**Example:**
```markdown
<!-- INSTRUCTIONS.md -->
# Project Instructions

## Core Behavior
- Process client notes from `clients/*/notes/` into updates
- Maintain STATE.md with active work context
- Silent startup: load STATE.md, detect PAUSE.md, await instructions

## Commands
- `/process-notes <client>` - Process notes for specified client
- Natural language: "Process notes for Acme Corp" also works

## File Structure
- `clients/<client>/notes/` - Raw notes from client
- `clients/<client>/updates/` - Generated update documents
- `STATE.md` - Current work context
```

```markdown
<!-- .claude/commands/process-notes.md -->
---
name: process-notes
description: Process client notes into update documents
---

# Process Notes Command

References: See INSTRUCTIONS.md for complete workflow

Agent: Execute the note processing workflow defined in INSTRUCTIONS.md
Arguments: $ARGUMENTS (client name)

Steps:
1. Validate client directory exists
2. Read notes from clients/$ARGUMENTS/notes/
3. Generate updates per INSTRUCTIONS.md patterns
4. Save to clients/$ARGUMENTS/updates/
5. Update STATE.md
```

```markdown
<!-- .cursorrules -->
# Cursor Instructions

This project uses INSTRUCTIONS.md as the canonical source.
Read that file for complete agent behavior.

Cursor-specific enhancements in .cursor/rules/
```

### Pattern 2: Session Initialization Protocol
**What:** On startup, agent loads minimal context, detects pause state, then waits silently for user commands.

**When to use:** Every agent session to balance token efficiency with context restoration.

**Example:**
```markdown
<!-- INSTRUCTIONS.md - Session Protocol Section -->
## Session Startup Protocol

1. Load STATE.md (if exists)
   - Contains: active work context, last action, relevant file paths
   - Purpose: minimal context for resuming work

2. Detect PAUSE.md (if exists)
   - Contains: pause reason, resume instructions
   - Action: Offer to resume: "You paused work on <task>. Resume?"

3. Silent Ready
   - No greeting, no status summary
   - Just ready to receive commands

4. Agent-Optimized Behavior
   - Claude Code: Can preload more context (better capabilities)
   - opencode/codex: Minimal load, ask for more as needed
```

**Implementation notes:**
- STATE.md should be <50 lines for token efficiency
- PAUSE.md is optional (only exists when user pauses work)
- Agent capabilities detection enables optimization (Claude Code vs opencode)

### Pattern 3: Hybrid Command Interface
**What:** Natural language and slash commands both work. Agent parses intent and executes workflow.

**When to use:** All user-facing commands to support both non-technical and power users.

**Example:**
```markdown
<!-- INSTRUCTIONS.md - Command Pattern Section -->
## Command Patterns

All commands support both formats:

Natural Language:
- "Process notes for Acme Corp"
- "Show me updates for Beta Industries"
- "Process notes for Acme, Beta, and Gamma" (batch)

Slash Commands:
- `/process-notes Acme`
- `/show-updates Beta`
- `/process-notes Acme Beta Gamma` (batch)

Parsing Logic:
1. Extract intent (process-notes, show-updates, etc)
2. Extract entities (client names)
3. Detect batch operations (multiple entities)
4. Execute workflow

Confirmation Rules:
- Destructive operations (delete, overwrite): Always confirm first
- Read operations (show, list): Execute immediately
- Add operations (create, process): Execute immediately
- Batch operations: Show plan, ask to proceed
```

### Pattern 4: Checkpoint-Based Recovery
**What:** On failure, save progress to checkpoint, mark failure point, enable resume or fix.

**When to use:** Any multi-step operation that could fail partway through.

**Example:**
```markdown
<!-- INSTRUCTIONS.md - Error Recovery Section -->
## Error Recovery Pattern

When multi-step operations fail:

1. Save Checkpoint
   - Record completed steps in .checkpoints/<operation-id>.json
   - Include: timestamp, operation, completed_steps, failed_step, context

2. Mark Failure
   - Create FAILED.md with plain English explanation
   - Include: what went wrong, why it happened, options to fix

3. Offer Recovery
   - "Step 3 failed (file not found). Options:"
   - "1. Fix the issue and I'll resume from step 3"
   - "2. Skip step 3 and continue"
   - "3. Start over"

Example Checkpoint:
{
  "operation": "process-notes",
  "client": "Acme",
  "timestamp": "2026-01-24T10:30:00Z",
  "completed_steps": [
    "validated_directory",
    "read_notes"
  ],
  "failed_step": "generate_update",
  "error": "Template file not found: templates/update.md",
  "context": {
    "notes_count": 5,
    "notes_paths": ["clients/Acme/notes/2026-01-20.md", ...]
  }
}
```

### Pattern 5: Graceful Degradation with Fallbacks
**What:** When agent lacks capability, offer manual alternative or simplified approach.

**When to use:** Cross-agent compatibility, especially for integrations opencode/codex can't handle.

**Example:**
```markdown
<!-- INSTRUCTIONS.md - Graceful Degradation Section -->
## Graceful Degradation

When capabilities are limited:

Tier 1 (Full Functionality):
- All tools available (file ops, git, external APIs)
- Claude Code with MCP servers: full automation

Tier 2 (Core Functionality):
- File ops and git available
- No external APIs (Trello, email, etc)
- Offer manual checklists for external steps

Tier 3 (Basic Functionality):
- Read-only operations
- Generate instructions for user to execute

Example Fallback:
Agent: "I can't update Trello directly (no MCP integration).
I'll generate a checklist of cards to update.
Would you like me to create manual-steps.md with the updates?"
```

### Anti-Patterns to Avoid
- **Duplicating instructions across files**: Use references to INSTRUCTIONS.md instead of copying content to `.claude/commands/` or `.cursorrules`
- **Verbose session greetings**: Silent starts are more efficient and professional
- **Loading full codebase on startup**: Use STATE.md for minimal context, load more on demand
- **Vague error messages**: Non-technical users need plain English, not tech jargon
- **Per-agent divergence**: Keep INSTRUCTIONS.md as single source of truth to prevent drift

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Semantic code search | Custom embeddings system | grepai + Ollama | Local, privacy-first, proven, actively maintained |
| Session state persistence | Custom JSON schema | STATE.md (markdown) + checkpoints | Human-readable, git-friendly, agent-parseable |
| Command parsing | Regex-based intent detection | Natural language parsing in instructions | LLMs excel at intent extraction, no regex maintenance |
| Agent configuration format | Custom YAML/JSON | AGENTS.md (markdown) | Industry standard, 60k+ projects, human-readable |
| Cross-agent sync | Custom config compiler | Single source (INSTRUCTIONS.md) + references | Simpler, prevents drift, easier to maintain |
| External integrations | Custom API wrappers | Model Context Protocol (MCP) | Standardized, growing ecosystem, supported by all major agents |

**Key insight:** The agent instruction layer domain has standardized rapidly in 2025-2026. Using established patterns (AGENTS.md, MCP, STATE.md) provides compatibility, community support, and future-proofing. Custom solutions risk abandonment and incompatibility as the ecosystem evolves.

## Common Pitfalls

### Pitfall 1: Configuration Scope Confusion
**What goes wrong:** Agent-specific files (`.claude/settings.json`, `.cursor/rules/`) override INSTRUCTIONS.md in unexpected ways, causing inconsistent behavior across agents.

**Why it happens:** Hierarchical configuration isn't clearly documented. Developers assume INSTRUCTIONS.md is always authoritative.

**How to avoid:**
- Document configuration precedence in INSTRUCTIONS.md
- Use agent-specific files only for enhancements (hooks, MCP), not core behavior
- Test with multiple agents to verify consistent behavior

**Warning signs:**
- "It works in Claude Code but not in Cursor"
- "The agent ignores INSTRUCTIONS.md rules"
- Duplicate instructions in multiple files

### Pitfall 2: Token Budget Exhaustion on Startup
**What goes wrong:** Loading full project context (README, all code files, complete history) on startup exhausts token budget before user asks anything.

**Why it happens:** Assumption that "more context = better performance" leads to over-eager loading.

**How to avoid:**
- Load only STATE.md on startup (<50 lines)
- Use grepai semantic search for on-demand context retrieval
- Implement "load more as needed" pattern in instructions
- Set token budget targets: startup <2000 tokens, typical session <10000 tokens

**Warning signs:**
- Slow startup times
- "Context limit exceeded" errors early in session
- High costs with minimal user interaction

### Pitfall 3: Consent Fatigue from Over-Confirmation
**What goes wrong:** Confirming every operation annoys users, leading them to blindly approve or abandon the agent.

**Why it happens:** Fear of destructive operations leads to conservative "confirm everything" approach.

**How to avoid:**
- Confirm only destructive operations (delete, overwrite, bulk changes)
- Read operations execute immediately
- Add operations execute immediately
- Batch operations show plan once, then execute
- Fine-grained permissions: distinguish read/write/delete

**Warning signs:**
- Users complaining about "too many confirmations"
- Rapid "yes yes yes" approval without reading
- Users asking "can you just do it?"

### Pitfall 4: Agent Identity Leakage
**What goes wrong:** Instructions announce which agent is running ("I'm Claude Code") or hide capabilities based on agent detection.

**Why it happens:** Desire to optimize per agent leads to hardcoded agent checks.

**How to avoid:**
- Agent identity is user-managed (they know which they're using)
- Graceful degradation based on capabilities, not agent name
- If feature unavailable, offer fallback without revealing agent type

**Warning signs:**
- Instructions contain "if Claude Code then..., if Cursor then..."
- Error messages like "Claude Code doesn't support..."
- Different instructions showing in different agents

### Pitfall 5: Slash Command Namespace Collision
**What goes wrong:** Custom `/process-notes` command conflicts with agent built-in `/process` or another project's `/notes` command.

**Why it happens:** No namespace planning when creating custom commands.

**How to avoid:**
- Check agent built-in commands first (`/help` in Claude Code)
- Use specific names: `/process-notes` not `/process`
- Document all custom commands in INSTRUCTIONS.md
- For monorepos, use namespaces: `/frontend:build`, `/backend:test`

**Warning signs:**
- Command does unexpected thing
- "Command not found" when it should exist
- Behavior differs across agent versions

### Pitfall 6: PAUSE.md Staleness
**What goes wrong:** User paused work weeks ago, PAUSE.md still exists, agent offers to resume stale task every session.

**Why it happens:** No expiration logic for pause state.

**How to avoid:**
- Include timestamp in PAUSE.md
- On startup, check age: if >7 days, ask "This pause is old. Still relevant?"
- Offer to archive or delete stale pause markers
- Alternative: rename to PAUSE-ARCHIVE-<date>.md after resolution

**Warning signs:**
- "Resume paused work?" on every startup
- User confusion: "What was I working on?"
- Stale context in PAUSE.md

## Code Examples

Verified patterns from official sources and community best practices:

### AGENTS.md Standard Format
```markdown
<!-- INSTRUCTIONS.md (AGENTS.md format) -->
# Agent Instructions: Project Name

## Overview
Brief project description, target user, key goals.

## File Structure
- `clients/<client>/notes/` - Client notes requiring processing
- `clients/<client>/updates/` - Generated update documents
- `STATE.md` - Current session state
- `docs/protocols/` - Operational documentation

## Core Workflows

### Process Notes Workflow
1. Validate client directory exists
2. Read all .md files from clients/<client>/notes/
3. Extract key information per protocol in docs/protocols/note-processing.md
4. Generate update document
5. Save to clients/<client>/updates/<date>.md
6. Update STATE.md with completion

## Commands
- `/process-notes <client>` - Process notes for client
- `/show-updates <client>` - Display recent updates
- Natural language equivalents also supported

## Code Style
- Plain English for all documentation
- No tech jargon (target: non-technical PM)
- Markdown for all text files
- Git operations invisible to user (automated)

## Testing
Not applicable (documentation-focused project)

## Security
- No API keys in files (use environment variables)
- Client data stays local (no cloud sync)
```
Source: [AGENTS.md specification](https://agents.md/)

### Claude Code CLAUDE.md Format
```markdown
<!-- CLAUDE.md -->
# Project Memory: Agent PM

## Quick Facts
- Target User: Non-technical project manager
- Key Files: INSTRUCTIONS.md, STATE.md, clients/*/notes/
- Main Command: `/process-notes <client>`

## Session Protocol
1. Load STATE.md (minimal context)
2. Check for PAUSE.md (offer resume if exists)
3. Silent ready (no greeting)

## Coding Conventions
- Markdown for all documentation
- Plain English (no jargon)
- Git automated (invisible to user)

## Key Directories
- `clients/` - Client workspaces
- `docs/protocols/` - Agent operational docs
- `docs/guides/` - User guides
- `.planning/` - Project planning (committed)

## Common Commands
```bash
# Note processing (automated via /process-notes)
ls clients/*/notes/*.md

# State management
cat STATE.md
```

## MCP Servers
None (standalone operation required)

## Hooks
- Pre-commit: Validate STATE.md format
- Post-checkout: Remind to check STATE.md

## Special Notes
- User is non-technical (email/spreadsheet level)
- Git must be invisible
- No command-line exposure
```
Source: [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)

### Cursor Project Rule (.mdc format)
```markdown
<!-- .cursor/rules/coding-style.mdc -->
---
title: "Coding Style"
description: "Project coding standards"
globs: ["**/*.md"]
alwaysApply: true
---

# Coding Style Rules

Reference: See INSTRUCTIONS.md for complete guidelines

## Markdown Standards
- Use ATX headers (# not underlines)
- One sentence per line (easier git diffs)
- Code blocks always fenced with language
- Tables for structured data

## Voice and Tone
- Plain English (6th grade reading level)
- Active voice ("Process the notes" not "Notes should be processed")
- No jargon (avoid: refactor, deploy, merge, etc)
- Use user's domain language (client, project, update)
```
Source: [Cursor Documentation](https://cursor.com/docs)

### Slash Command Definition
```markdown
<!-- .claude/commands/process-notes.md -->
---
name: process-notes
description: Process client notes into update documents. Use when user says "process notes" or "/process-notes <client>"
---

# Process Notes Command

## Purpose
Convert raw client notes into structured update documents.

## Arguments
- `$ARGUMENTS` - Client name (required)
- Supports batch: `/process-notes Acme Beta Gamma`

## Workflow

### 1. Parse Input
Extract client names from $ARGUMENTS (space or comma separated)

### 2. For Each Client
- Validate: `clients/<client>/notes/` exists
- Read: All .md files in notes directory
- Process: Follow docs/protocols/note-processing.md
- Generate: Update document per template
- Save: `clients/<client>/updates/<YYYY-MM-DD>.md`

### 3. Update State
Append to STATE.md:
```
Last action: Processed notes for <clients> at <timestamp>
Files created: <list of update files>
```

### 4. Confirmation
If batch (>1 client):
  Show plan: "Will process notes for: Acme, Beta, Gamma"
  Ask: "Proceed?"

If single client:
  Execute immediately

### 5. Error Handling
If client directory missing:
  Create FAILED.md with explanation
  Offer: "Create clients/<client>/ directory structure?"
```
Source: [Claude Code Showcase](https://github.com/ChrisWiles/claude-code-showcase)

### STATE.md Minimal Format
```markdown
<!-- STATE.md -->
# Current Work State

**Last Updated:** 2026-01-24 10:30 AM

## Active Work
Processing notes for Acme Corp (batch: Acme, Beta, Gamma)

## Progress
- [x] Acme Corp - completed
- [x] Beta Industries - completed
- [ ] Gamma Solutions - in progress

## Last Action
Generated update: clients/Beta/updates/2026-01-24.md

## Next Steps
- Complete Gamma processing
- Review all three updates
- Mark batch complete

## Context Files
- clients/Acme/notes/2026-01-20.md
- clients/Beta/notes/2026-01-22.md
- clients/Gamma/notes/2026-01-23.md
```
Source: Anthropic long-running agent patterns

### Checkpoint Format
```json
{
  "checkpoint_id": "process-notes-20260124-103045",
  "operation": "process-notes",
  "timestamp": "2026-01-24T10:30:45Z",
  "clients": ["Acme", "Beta", "Gamma"],
  "completed": ["Acme", "Beta"],
  "failed": "Gamma",
  "error": {
    "step": "generate_update",
    "message": "Template file not found: templates/update.md",
    "file_path": "templates/update.md"
  },
  "artifacts": [
    "clients/Acme/updates/2026-01-24.md",
    "clients/Beta/updates/2026-01-24.md"
  ],
  "state_backup": "STATE-backup-20260124-103045.md"
}
```
Source: [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)

### grepai Configuration
```yaml
# .grepai/config.yaml
# Generated via: grepai init

embedder:
  provider: ollama           # Local embeddings (privacy-first)
  model: nomic-embed-text    # Fast, 768 dimensions
  endpoint: http://localhost:11434
  dimensions: 768

store:
  backend: gob               # File-based (zero config)
  path: .grepai/vectors.gob

chunking:
  size: 512                  # Tokens per chunk
  overlap: 50                # Context overlap

watch:
  debounce_ms: 500          # Wait before processing changes

search:
  top_k: 10                 # Return top 10 results
  hybrid: true              # Combine semantic + text search
```
Source: [grepai configuration guide](https://yoanbernabeu.github.io/grepai/configuration/)

## State of the Art

| Old Approach (2024) | Current Approach (2026) | When Changed | Impact |
|---------------------|------------------------|--------------|--------|
| Per-agent instruction files | AGENTS.md standard | August 2025 | 60k+ projects unified, cross-agent compatibility |
| .cursorrules only | .cursor/rules/ with .mdc | Early 2026 | Better organization, metadata support |
| README-as-instructions | AGENTS.md separate from README | August 2025 | Clear separation: humans vs agents |
| Full context loading | Minimal STATE.md + on-demand | 2025-2026 | 70% token reduction in benchmarks |
| Custom API integrations | Model Context Protocol (MCP) | November 2025 | Standardized integrations, ecosystem growth |
| Text search only | Semantic search (grepai) | 2025-2026 | 71% token savings (Claude Code benchmark) |
| Monolithic prompts | Multi-agent orchestration | 2025-2026 | Specialized agents, better scalability |
| Imperative commands | Natural language + slash commands | 2025-2026 | Accessibility + power user efficiency |

**Deprecated/outdated:**
- `.cursorrules` as primary config: Migrating to `.cursor/rules/*.mdc` format
- `.claude.json` in project root: Replaced by `.claude/settings.json` (hierarchical config)
- Custom slash command parsing: Agent SDK now provides built-in parsing
- Manual context assembly: MCP and semantic search replace manual file reading

## Open Questions

1. **Efficiency mode toggle**
   - What we know: User expressed interest in high/normal/verbose modes
   - What's unclear: Best implementation (config file vs command vs auto-detect)
   - Recommendation: Start without toggle (default to balanced verbosity), add if user requests after testing

2. **Agent-optimized vs identical behavior**
   - What we know: User wants to document this tradeoff, Claude Code has better capabilities than opencode/codex
   - What's unclear: Specific optimization points, how much divergence is acceptable
   - Recommendation: Document in docs/protocols/agent-optimization.md, include testing matrix

3. **grepai watch lifecycle**
   - What we know: Should run during file operations, stop on session close
   - What's unclear: How to reliably detect "session close" across different agents
   - Recommendation: Agent starts watch on first file operation, user manually stops (document in session protocol)

4. **PAUSE.md expiration**
   - What we know: Stale pause markers cause confusion
   - What's unclear: Best expiration threshold (7 days? 30 days?)
   - Recommendation: Start with 7 days, include timestamp, ask user if stale

5. **Cross-agent configuration testing**
   - What we know: Need to verify consistent behavior across Claude Code, Cursor, and opencode
   - What's unclear: Best testing methodology for agent behavior validation
   - Recommendation: Create test scenarios in docs/protocols/testing-agents.md, manual execution required

## Sources

### Primary (HIGH confidence)
- [Windsurf AGENTS.md Documentation](https://docs.windsurf.com/windsurf/cascade/agents-md) - Official AGENTS.md specification
- [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices) - Anthropic engineering blog
- [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) - Session protocol patterns
- [grepai Installation](https://yoanbernabeu.github.io/grepai/installation/) - Official installation guide
- [grepai Configuration](https://yoanbernabeu.github.io/grepai/configuration/) - Official configuration guide
- [AGENTS.md Standard](https://agents.md/) - Canonical specification site
- [Claude Code Showcase GitHub](https://github.com/ChrisWiles/claude-code-showcase) - Comprehensive examples

### Secondary (MEDIUM confidence)
- [Cursor Documentation](https://cursor.com/docs) - Official Cursor docs (verified Project Rules migration)
- [Slash Commands in Theia AI](https://eclipsesource.com/blogs/2026/01/08/slash-commands-theia-ai/) - Slash command patterns
- [Batch Query Processing for Agentic Workflows](https://arxiv.org/html/2509.02121v1) - Batch processing patterns
- [Multi-Agent System Failure Recovery](https://galileo.ai/blog/multi-agent-ai-system-failure-recovery) - Checkpoint and recovery patterns
- [User Consent Best Practices for AI Agents](https://curity.io/blog/user-consent-best-practices-in-the-age-of-ai-agents/) - Confirmation patterns
- [grepai Benchmark vs grep](https://yoanbernabeu.github.io/grepai/blog/benchmark-grepai-vs-grep-claude-code/) - Performance validation

### Tertiary (LOW confidence - validated where possible)
- Various community blog posts on agent configuration patterns
- GitHub issues and discussions (cross-referenced with official docs)
- Stack Overflow discussions (verified against official sources)

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - AGENTS.md/CLAUDE.md format is well-documented and widely adopted (60k+ projects)
- Architecture: HIGH - Session protocol patterns verified in Anthropic engineering blogs, AGENTS.md spec is authoritative
- Pitfalls: MEDIUM-HIGH - Based on community experiences cross-referenced with official guidance, some based on common sense extrapolation
- grepai integration: HIGH - Official documentation with clear installation and configuration
- Slash commands: MEDIUM-HIGH - Multiple sources agree, verified in Theia AI and Claude SDK docs

**Research date:** 2026-01-24
**Valid until:** 2026-02-24 (30 days - agent configuration domain is evolving but patterns are stabilizing)

**Notes:**
- AGENTS.md standard very recent (August 2025) but rapidly adopted
- MCP ecosystem growing fast - may add new integration options
- Cursor .mdc migration ongoing - both formats currently supported
- grepai is actively maintained, version changes may affect configuration
