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
| "Add CEO is John to Acme" | (natural only) | Update entity profile |
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
- **Profile Building:** Add facts with "Add [fact] to [entity]'s profile". Facts are placed in contextually appropriate sections. See `docs/protocols/profile-building.md` for placement rules.
- **Note Processing:** Process meeting notes or voice memos with "Process notes for [entity]". Extracts tasks, profile facts, calendar items, and follow-ups. Processed notes are archived. See `docs/protocols/note-processing.md` for full protocol.
- **Status Reporting:** Generate summaries from entity roadmaps
- **Priority Management:** Reorder entities on ROADMAP.md dashboard

## Skills

Skills are reusable workflows that encode best practices. Users don't need to know skill names - just describe what you want.

**Discovery:** User asks "What skills do you have?" -> Show available workflows. See `docs/protocols/skill-discovery.md`.

**Invocation:** Natural language and slash commands both work:
- "Process notes for Acme" = `/process-notes Acme`
- "Run weekly review" = `/weekly-review`

**Batch operations:** "Process notes for all clients" works as one request.

**PM Base Skills:**
- process-notes - Extract tasks from meeting notes
- get-status - Show entity status
- weekly-review - Generate weekly status report

Skills are stored in `.github/skills/` (cross-platform) and `.claude/skills/` (agent-specific).

## Specialists

Specialists are domain expert AI personas that produce structured deliverables. Unlike skills (which are procedures), specialists have expertise and judgment for complex work.

**Discovery:** User asks "What specialists do you have?" or `/specialists` -> Show available experts.

**Invocation:** Natural language and explicit naming both work:
- "Run an assessment for Acme" (natural language)
- "Use assessment-specialist for Acme" (explicit)
- "Develop strategy for Beta Corp" (natural language)

**Batch operations:** "Run assessment for all clients" works as one request.

**Available Specialists:**
- assessment-specialist - Conducts audits, gap analyses, current state evaluations
- strategy-specialist - Develops strategic plans, roadmaps, forward-looking recommendations

**Knowledge Layers:** Specialists access layered knowledge:
1. Base PM (INSTRUCTIONS.md, protocols)
2. Domain (docs/domain/ playbooks)
3. Agency (ops/ preferences and processes)
4. Entity (entities/[Entity]/knowledge/ learned context)

**Deliverables:** Specialists save work to `entities/[Entity]/deliverables/` with date-suffix naming (e.g., AUDIT_FINDINGS_2026-01-25.md).

**Sensitivity:** Internal content (INTERNAL_*.md, internal/ folders, <!-- INTERNAL --> markers) is never included in client-facing deliverables.

Specialists are stored in `.claude/agents/` (project) and `~/.claude/agents/` (personal).

See `docs/protocols/specialist-coordination.md` for detailed protocol.

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
- `docs/SKILL_AUTHORING_GUIDE.md` - How to create custom skills
