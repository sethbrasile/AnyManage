---
phase: 02-agent-instruction-layer
plan: 01
subsystem: agent-instructions
tags: [instructions, session-protocol, commands, agent-agnostic]

# Dependency graph
requires:
  - phase: 01-core-file-structure
    provides: File structure (entities/, templates/, ops/) and CONFIG.md for entity configuration
provides:
  - INSTRUCTIONS.md - canonical agent behavior document
  - Session protocol definition (STATE.md, PAUSE.md, silent ready)
  - Command patterns (natural language + slash commands)
  - Pointers to docs/protocols/ for detailed documentation
affects: [02-02-PLAN (agent wrappers), 02-03-PLAN (protocol docs), all future phases read INSTRUCTIONS.md]

# Tech tracking
tech-stack:
  added: []
  patterns: [AGENTS.md standard, session-protocol, hybrid-commands]

key-files:
  created:
    - INSTRUCTIONS.md
  modified: []

key-decisions:
  - "Core behavior ~90 lines with pointers to docs/protocols/"
  - "Agent-agnostic design - no agent-specific content"
  - "Silent start - no greeting or status summary"
  - "Hybrid commands - natural language and slash commands both work"

patterns-established:
  - "Session Protocol: Load STATE.md, check PAUSE.md, silent ready"
  - "Command confirmation: destructive confirms, read/add executes immediately"
  - "Error recovery: plain English, offer alternatives"

# Metrics
duration: 2min
completed: 2026-01-24
---

# Phase 02 Plan 01: INSTRUCTIONS.md Summary

**Canonical agent instructions created following AGENTS.md standard with session protocol, command patterns, and docs/ pointers**

## Performance

- **Duration:** 2 min
- **Started:** 2026-01-24T20:54:24Z
- **Completed:** 2026-01-24T20:55:48Z
- **Tasks:** 1/1
- **Files modified:** 1

## Accomplishments

- Created INSTRUCTIONS.md as single source of truth for all agent behavior
- Defined session protocol (STATE.md loading, PAUSE.md resume, silent ready)
- Established command patterns (natural language + slash commands with confirmation rules)
- Documented file structure and core workflows with pointers to docs/protocols/

## Task Commits

Each task was committed atomically:

1. **Task 1: Create INSTRUCTIONS.md canonical agent instructions** - `63a1f9c` (feat)

## Files Created/Modified

- `INSTRUCTIONS.md` - Canonical agent instructions (91 lines)

## Decisions Made

- **Token-efficient structure:** Core behavior in ~90 lines with detailed docs in docs/protocols/
- **Agent-agnostic:** No Claude/Cursor/codex-specific content - works for all agents
- **Silent session start:** No greeting or status summary - just ready to work
- **Hybrid command support:** Natural language and slash commands both work equally

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness

- INSTRUCTIONS.md ready for agent wrapper files (02-02-PLAN)
- docs/protocols/ directory needs to be created with detailed documentation (02-03-PLAN)
- All future agent interactions can read INSTRUCTIONS.md for behavior guidance

---
*Phase: 02-agent-instruction-layer*
*Completed: 2026-01-24*
