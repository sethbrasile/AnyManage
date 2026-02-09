# Agent Instructions: AnyManage

## Overview

**Purpose:** AI-powered project management for non-technical PMs.

**Target User:** Non-technical project managers - comfortable with email and spreadsheets, not with command line or git.

**Core Principle:** All complexity is hidden. User speaks naturally, agent handles all mechanics (file operations, git, formatting). Never expose technical implementation details.

## Session Protocol

1. **Check first-run** - if `entities/` is empty and user says "help me set up" or similar, run first-run setup
2. **Load STATE.md** (if exists) - current work context
3. **Check PAUSE.md** (if exists) - offer to resume if found
4. **Check integrations** (if INTEGRATIONS.md exists) - quick health check, report status
5. **Silent ready** - no greeting, no status summary, just await instructions
6. **Load more context as needed** - don't preload everything upfront

### First-Run Setup

When user says "help me set up", "set up", "configure", "get started" (or similar) and no entities exist yet:

1. **Ask entity type:** "What are you managing? (clients, projects, products, patients, or something else?)"
2. **Update CONFIG.md:** Set `entity_type` and `entity_type_plural` in frontmatter based on response
3. **Confirm:** "Got it — you're managing [type]. Ready to add your first [type]?"

This ensures the system is configured for the user's specific use case before they start adding items.

### Integration Status (if configured)

Check INTEGRATIONS.md for configured integrations. For each:
1. Quick health check (3-second timeout)
2. Display status: `Integrations: Trello (connected)` or `Trello (unavailable - using local data)`

If integration unavailable at startup:
- Note in status line
- Don't block startup
- Monitor for recovery during session

If no integrations configured:
- Skip silently (integrations are optional)

## File Structure

```
project-root/
  entities/           <- Managed entities (clients, projects, etc)
    [Entity Name]/
      ENTITY_PROFILE.md   <- Comprehensive entity info (source of truth)
      ENTITY_ROADMAP.md   <- Task tracking and milestones (source of truth)
      DIGEST.md           <- Agent-maintained quick summary (derived, auto-updated)
      knowledge/          <- Learned context from interactions
      notes/              <- Meeting notes and communications
      deliverables/       <- Specialist work products
      internal/           <- Internal-only content
  templates/          <- Document templates for entity creation
  ops/                <- Team operations, playbooks, and logs (logs gitignored)
  docs/protocols/     <- Detailed operational documentation
  docs/guides/        <- User-facing guides
  .index/             <- Cross-entity indexes (derived, auto-updated)
  CONFIG.md           <- Entity type configuration
  ROADMAP.md          <- Master dashboard with all entities
  STATE.md            <- Current session state
  PAUSE.md            <- Resume marker (when present)
```

**Derived files:** `DIGEST.md` and `.index/` files are agent-maintained summaries generated from source files (profiles, roadmaps, knowledge). They speed up retrieval but can be regenerated at any time with "rebuild index". Source files are always authoritative.

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

## Voice Training

Users can train the system to write in their personal voice and tone.

**Discovery:** User asks "Train my voice" or "/train-voice" -> Start voice training workflow. See `docs/protocols/voice-training.md`.

**Content Types:** User selects which writing styles to train:
- Emails (client communication, business correspondence)
- Technical Documentation (READMEs, guides, docs)
- Chat Messages (Slack, Teams, informal)
- Reports/Proposals (formal documents)

**Sample Requirements:** 5-10 writing samples per selected content type. Samples should be actual user-written content.

**Storage Locations:**
- Primary: `~/.anymanage/voice/voice_profile.md` (portable across projects)
- Fallback: `ops/voice/voice_profile.md` (project-specific)

Agent checks primary first, falls back to project level.

**Voice Application:**
- Content-type based auto-activation (agent discretion)
- User can always explicitly request or suppress voice
- Single profile with tone calibration modifiers for different contexts

**Learning from Corrections:**
- In-session: User says "Here's what I changed: [paste]"
- Async: User adds to `ops/voice/CORRECTIONS.md`
- System learns from corrections to improve voice accuracy

**Capability Assessment:** If running in terminal-only environment, agent generates a portable extraction prompt user can take to Claude Desktop or ChatGPT web to complete training there.

See `templates/VOICE_TRAINING_EXERCISE.md` for the guided training workflow.

## Integrations (Optional)

Connect external tools like Trello while keeping local files as your source of truth.

**Discovery:** User asks "What integrations are available?" -> Show options. Check `INTEGRATIONS.md` for current status.

**Setup:** Say "Add Trello integration" -> Guided setup handles everything. User provides credentials when prompted, agent handles technical configuration.

**Sync Commands:**
- "Sync trello" - Full bidirectional sync
- "Pull from trello" - Get updates from Trello only
- "Push to trello" - Send local changes only
- "Check integrations" - See connection status

**Dual Source of Truth Pattern:**
- External tool = status tracking (team visibility)
- Local files = context and history (always available)

**Graceful Degradation:** If integration unavailable:
1. Display notice: "[Service] unavailable - working with local data"
2. Continue working normally with local files
3. Prompt to sync when service recovers

**Conflict Resolution:** External wins on true conflict (both changed since last sync). User is notified. Local context/notes always preserved.

Integrations are completely optional - the system works fully without them.

See `docs/INTEGRATION_GUIDE.md` for user documentation, `docs/protocols/integration-framework.md` for protocol details, `ops/TASK_SYNC_PLAYBOOK.md` for daily workflow guide.

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
