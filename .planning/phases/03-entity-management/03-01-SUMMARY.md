---
phase: 03-entity-management
plan: 01
subsystem: documentation
tags: [entity-onboarding, workflows, protocols, agent-instructions]

# Dependency graph
requires:
  - phase: 02-agent-instruction-layer
    provides: INSTRUCTIONS.md as agent entry point
  - phase: 01-core-file-structure
    provides: Entity templates and folder structure patterns
provides:
  - Complete entity onboarding protocol documentation
  - Natural language and slash command patterns for entity creation
  - Integration with INSTRUCTIONS.md for workflow discoverability
affects: [03-02-profile-building, 03-03-note-processing, phase-04-skills, phase-05-specialists]

# Tech tracking
tech-stack:
  added: []
  patterns:
    - Protocol documentation format (trigger detection, normalization, error handling, examples)
    - Name normalization to title case for consistency
    - Duplicate detection with exact and fuzzy matching
    - Silent git commits with descriptive messages

key-files:
  created:
    - docs/protocols/entity-onboarding.md
  modified:
    - INSTRUCTIONS.md

key-decisions:
  - "Protocol documentation includes trigger patterns, normalization rules, duplicate handling, and example interactions"
  - "Entity names normalized to title case for consistency across file system"
  - "Duplicate detection with both exact and fuzzy (>80% similarity) matching"
  - "Minimal creation pattern: create folder immediately, enhance afterward"
  - "Next-step suggestions after creation to guide user workflow"
  - "Git operations remain completely invisible to users"

patterns-established:
  - "Protocol documentation structure: Overview → Trigger Detection → Core Logic → Error Handling → Example Interactions → Integration with Other Workflows"
  - "Entity creation workflow: detect trigger → normalize name → check duplicates → create folder structure → update ROADMAP.md → confirm with suggestions"
  - "Command pattern documentation in INSTRUCTIONS.md references detailed protocols in docs/protocols/"

# Metrics
duration: 2min
completed: 2026-01-24
---

# Phase 03 Plan 01: Entity Onboarding Protocol Summary

**Complete entity onboarding protocol with natural language triggers, title case normalization, duplicate detection, and integration with master ROADMAP.md**

## Performance

- **Duration:** 2 min
- **Started:** 2026-01-25T01:24:14Z
- **Completed:** 2026-01-25T01:26:52Z
- **Tasks:** 2
- **Files modified:** 2

## Accomplishments

- Comprehensive entity onboarding protocol documentation (523 lines) covering all aspects of entity creation
- Natural language and slash command trigger patterns ("Add new client X", "/new X")
- Title case name normalization with special character handling (hyphens, apostrophes, acronyms)
- Duplicate detection with exact and fuzzy matching (>80% similarity) before creation
- Standard folder structure creation (profile, roadmap, notes, notes/archive)
- Template population with entity name replacement and timestamps
- Master ROADMAP.md integration in Medium Priority section
- Confirmation messages with proactive next-step suggestions
- Error handling and fallback templates
- Six detailed example interactions demonstrating various scenarios
- Integration patterns with other Phase 3 workflows (profile building, note processing)
- Updated INSTRUCTIONS.md with expanded workflow description and protocol reference

## Task Commits

Each task was committed atomically:

1. **Task 1: Create entity onboarding protocol documentation** - `9364997` (docs)
2. **Task 2: Update INSTRUCTIONS.md with onboarding workflow** - `1e0bf09` (docs)

## Files Created/Modified

- `docs/protocols/entity-onboarding.md` - Complete entity onboarding protocol with trigger detection, normalization, duplicate handling, folder creation, ROADMAP.md integration, confirmation patterns, error handling, and example interactions
- `INSTRUCTIONS.md` - Expanded Entity Onboarding entry in Core Workflows section with protocol reference; reordered Command Patterns table to show creation before processing

## Decisions Made

**Protocol Documentation Structure:**
- Followed existing protocol documentation style (session-protocol.md format)
- Organized as: Overview → Trigger Detection → Name Normalization → Duplicate Detection → Folder Creation → ROADMAP Update → Confirmation → Error Handling → Examples → Integration
- Included 6 detailed example interactions covering basic creation, normalization, exact duplicates, fuzzy duplicates, slash commands, and special characters

**Name Normalization Approach:**
- Title case as standard (ACME CORP → Acme Corp, acme corp → Acme Corp)
- Preserve hyphens and capitalize both parts (acme-corp → Acme-Corp)
- Handle apostrophes correctly (o'reilly → O'Reilly)
- Preserve common acronyms (IBM stays uppercase)
- Folder name = normalized entity name (no slug conversion)

**Duplicate Detection Strategy:**
- Check existing entities/ folder before creation
- Exact match: offer to update existing or specify different name
- Fuzzy match (>80% similarity): offer to create new anyway or use existing
- User choice required - no automatic resolution

**Creation Pattern:**
- Minimal creation first (no upfront questions)
- Standard folder structure: entities/[Name]/ with notes/, notes/archive/
- Copy templates with [Entity Name] placeholder replacement
- Add to ROADMAP.md Medium Priority section by default
- Silent git commit after creation (invisible to user)
- Confirmation with proactive next-step suggestions

**INSTRUCTIONS.md Integration:**
- Expanded Core Workflows section with detailed entry including natural language and slash command patterns
- Added explicit reference to docs/protocols/entity-onboarding.md
- Reordered Command Patterns table to show creation before processing (logical workflow order)
- Kept changes minimal and focused on onboarding workflow

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None - protocol documentation created smoothly following existing patterns from session-protocol.md and command-patterns.md.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness

**Ready for next plans:**
- Entity onboarding protocol documented and referenced in INSTRUCTIONS.md
- Foundation established for 03-02 (Profile Building Protocol)
- Foundation established for 03-03 (Note Processing Protocol)
- Natural language and slash command patterns can be extended for other workflows

**For profile building (03-02):**
- Entity structure defined (ENTITY_PROFILE.md exists in onboarded entities)
- Profile template sections documented
- Update patterns can build on onboarding confirmation pattern

**For note processing (03-03):**
- Entity folder structure includes notes/ and notes/archive/
- Processing can reference entities created via onboarding
- Top-level NOTES.md routing can leverage entity detection logic

**No blockers or concerns.**

---
*Phase: 03-entity-management*
*Completed: 2026-01-24*
