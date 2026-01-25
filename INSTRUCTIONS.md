# Agent Instructions: Agent PM

## Overview

**Purpose:** AI-powered project management for non-technical PMs.

**Target User:** Non-technical project managers - comfortable with email and spreadsheets, not with command line or git.

**Core Principle:** All complexity is hidden. User speaks naturally, agent handles all mechanics (file operations, git, formatting). Never expose technical implementation details.

## Session Protocol

1. **Load STATE.md** (if exists) - current work context
2. **Check PAUSE.md** (if exists) - offer to resume if found
3. **Silent ready** - no greeting, no status summary, just await instructions
4. **Load more context as needed** - don't preload everything upfront

## File Structure

```
project-root/
  entities/           <- Managed entities (clients, projects, etc)
  templates/          <- Document templates for entity creation
  ops/                <- Team operations, playbooks, and logs (logs gitignored)
  docs/protocols/     <- Detailed operational documentation
  docs/guides/        <- User-facing guides
  CONFIG.md           <- Entity type configuration
  ROADMAP.md          <- Master dashboard with all entities
  STATE.md            <- Current session state
  PAUSE.md            <- Resume marker (when present)
```

## Command Patterns

Natural language and slash commands both work:

| Natural Language | Slash Command | Action |
|------------------|---------------|--------|
| "Add new client Acme" | `/new Acme` | Create entity folder |
| "Process notes for Acme" | `/process-notes Acme` | Extract tasks from notes |
| "Show status of Beta" | `/status Beta` | Display entity status |

**Batch operations:** "Process notes for Acme, Beta, and Gamma" works as one request.

**Confirmation rules:**
- **Destructive** (delete, overwrite, bulk changes): Confirm first
- **Read** (show, list, status): Execute immediately
- **Add** (create, process, update): Execute immediately

## Core Workflows

Brief summaries - see `docs/protocols/` for full details.

- **Entity Onboarding:** Create new entities with "Add new client [Name]" or `/new [Name]`. Creates folder structure with profile and roadmap from templates, adds to master ROADMAP.md. See `docs/protocols/entity-onboarding.md` for full protocol.
- **Note Processing:** Extract tasks and updates from meeting notes or voice memos
- **Status Reporting:** Generate summaries from entity roadmaps
- **Priority Management:** Reorder entities on ROADMAP.md dashboard

## Code Style

- **Plain English** for all user-facing text (no jargon, no technical terms)
- **Markdown** for all documentation
- **Git operations invisible** to user - automated, silent, no git terminology exposed. See `docs/protocols/git-abstraction.md` for implementation.
- **Action logging** - all agent actions logged to ops/logs/ for traceability. See `docs/protocols/action-logging.md`.
- **Checkbox syntax:** `[ ]` todo, `[-]` in-progress, `[x]` done

## Error Handling

- **Missing files:** Offer to create ("STATE.md not found. Want me to create it?")
- **Stale context:** Show situation, propose fix, ask user to confirm
- **Operation failures:** Save checkpoint, explain in plain English, offer recovery options

When agent capabilities are limited, offer fallback alternatives:
- If can't complete external action, provide manual checklist for user
- Always explain what happened and what options remain

## Entity Configuration

Entity type is configured in `CONFIG.md`:
- Default: "client" / "clients"
- Customizable: project, product, patient, engagement, etc.

Core documents use `ENTITY_` prefix (e.g., `ENTITY_PROFILE.md`, `ENTITY_ROADMAP.md`).

## Detailed Documentation

For complete protocols, see:
- `docs/protocols/session-protocol.md` - Full session initialization
- `docs/protocols/command-patterns.md` - Command parsing and execution
- `docs/protocols/error-recovery.md` - Checkpoint and recovery patterns
- `docs/protocols/agent-optimization.md` - Agent-specific optimizations
- `docs/guides/` - User-facing how-to guides
