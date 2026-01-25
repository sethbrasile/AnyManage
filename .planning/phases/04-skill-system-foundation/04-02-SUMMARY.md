---
phase: 04-skill-system-foundation
plan: 02
subsystem: skills
tags: [skills, process-notes, get-status, weekly-review, agent-skills]

# Dependency graph
requires:
  - phase: 04-01
    provides: Skill directory structure and discovery protocol
  - phase: 03-entity-management
    provides: Note-processing and profile-building protocols
provides:
  - Three PM base skills (process-notes, get-status, weekly-review)
  - Skill templates demonstrating Agent Skills 1.0 format
  - Batch operation patterns for multi-entity workflows
affects: [04-03-skill-authoring, 05-specialist-pattern]

# Tech tracking
tech-stack:
  added: []
  patterns:
    - Agent Skills 1.0 format (YAML frontmatter + Markdown body)
    - Skills reference protocols (no logic duplication)
    - Entity-type awareness via metadata.compatible_entity_types
    - Batch processing with progress feedback

key-files:
  created:
    - .github/skills/process-notes/SKILL.md
    - .github/skills/get-status/SKILL.md
    - .github/skills/weekly-review/SKILL.md
  modified: []

key-decisions:
  - "Skills reference protocols, don't duplicate logic (single source of truth)"
  - "All skills support batch operations across multiple entities"
  - "Entity-type awareness via metadata for flexible skill matching"
  - "Attention flags for automated issue detection"

patterns-established:
  - "Skill description includes triggering language for LLM matching"
  - "When to Use section lists explicit trigger phrases"
  - "How It Works references protocol docs"
  - "Output Format shows expected user-facing response"
  - "Error Handling covers common failure cases"

# Metrics
duration: 19min
completed: 2026-01-25
---

# Phase 4 Plan 02: PM Base Skills Summary

**Three PM base skills (process-notes, get-status, weekly-review) following Agent Skills 1.0 format with protocol references and batch operation support**

## Performance

- **Duration:** 19 min
- **Started:** 2026-01-25T05:11:57Z
- **Completed:** 2026-01-25T05:30:30Z
- **Tasks:** 3
- **Files created:** 3

## Accomplishments

- Created process-notes skill for extracting tasks and profile facts from meeting notes
- Created get-status skill for entity and cross-entity status reporting
- Created weekly-review skill for comprehensive weekly progress summaries
- All skills follow Agent Skills 1.0 format with proper YAML frontmatter
- Skills reference existing protocols (no duplication of logic)
- Batch operation patterns documented for multi-entity workflows

## Task Commits

Each task was committed atomically:

1. **Task 1: Create process-notes skill** - `7bcbce9` (feat) - previously committed
2. **Task 2: Create get-status skill** - `a246409` (feat) - previously committed
3. **Task 3: Create weekly-review skill** - `e3838b9` (feat) - created this session

## Files Created

- `.github/skills/process-notes/SKILL.md` - 134 lines, references docs/protocols/note-processing.md
- `.github/skills/get-status/SKILL.md` - 146 lines, reads entity ROADMAP.md files
- `.github/skills/weekly-review/SKILL.md` - 165 lines, aggregates cross-entity status

## Skill Capabilities

| Skill | Trigger Examples | Key Feature |
|-------|-----------------|-------------|
| process-notes | "process notes for Acme", "extract tasks from notes" | Four-category extraction (tasks, profile facts, calendar, follow-ups) |
| get-status | "what's the status of Acme", "status update" | Single and cross-entity status with attention flags |
| weekly-review | "run weekly review", "what got done this week" | Aggregated weekly summary with next-week priorities |

## Skill Structure Pattern

All skills follow this consistent structure:

```
---
name: skill-name
description: [Triggering language for LLM matching]
metadata:
  author: agent-pm
  version: "1.0"
  compatible_entity_types: ["client", "project", "product"]
---

# Skill Name

## When to Use
## How It Works
## Output Format
## Batch Processing
## Error Handling
## Slash Command
## Related Files/Protocols
```

## Decisions Made

| Decision | Rationale |
|----------|-----------|
| Skills reference protocols, don't duplicate | Single source of truth in docs/protocols/ prevents divergence |
| All skills support batch operations | Common user need: "process notes for all clients" |
| Entity-type awareness via metadata | Skills can specify which entity types they work with |
| Attention flags in status skills | Proactive issue detection (overdue, stale, overloaded) |

## Deviations from Plan

None - Tasks 1 and 2 were already completed in a prior session. This session completed Task 3 (weekly-review skill).

## Issues Encountered

None.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness

- Three base skills ready for users to invoke naturally
- Skill format established for 04-03 (Skill Authoring Guide)
- Skills demonstrate patterns for future specialist skills (Phase 5)
- Batch processing patterns documented for multi-entity operations

---
*Phase: 04-skill-system-foundation*
*Completed: 2026-01-25*
