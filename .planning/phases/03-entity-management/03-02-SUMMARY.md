---
phase: 03-entity-management
plan: 02
subsystem: documentation
tags: [profile-building, note-processing, workflows, protocols, agent-instructions]

# Dependency graph
requires:
  - phase: 03-entity-management
    plan: 01
    provides: Entity onboarding protocol foundation
  - phase: 02-agent-instruction-layer
    provides: INSTRUCTIONS.md as agent entry point
  - phase: 01-core-file-structure
    provides: Entity templates and profile structure
provides:
  - Complete profile building protocol documentation
  - Complete note processing protocol documentation
  - Natural language patterns for profile updates and note processing
  - Integration with INSTRUCTIONS.md for workflow discoverability
affects: [03-03-git-abstraction, phase-04-skills, phase-05-specialists]

# Tech tracking
tech-stack:
  added: []
  patterns:
    - Smart section placement mapping (fact type to profile section)
    - Silent updates with post-confirmation
    - Conflicting information replacement strategy
    - Three-source note input pattern (inline, file, top-level inbox)
    - Four-category extraction triage (tasks, profile, calendar, follow-ups)
    - Duplicate task detection
    - Processed note archiving with header marking
    - Discussion mode for ambiguous content

key-files:
  created:
    - docs/protocols/profile-building.md
    - docs/protocols/note-processing.md
  modified:
    - INSTRUCTIONS.md

key-decisions:
  - "Profile building uses smart placement based on fact type without user needing to specify sections"
  - "Silent updates: make changes first, confirm after (no pre-confirmation prompts)"
  - "Conflicting information: new replaces old (profile shows current state only)"
  - "Note processing supports three input sources: pasted inline, entity notes folder, top-level NOTES.md"
  - "Full triage extraction: tasks, profile facts, calendar items, follow-ups"
  - "Processed notes marked with header and archived (never deleted)"
  - "Duplicate task detection before adding to roadmap"
  - "Discussion mode triggers for prompt-like or ambiguous content"
  - "Itemized reporting shows what was extracted and where it went"

patterns-established:
  - "Profile update workflow: detect trigger → extract fact → map to section → update silently → confirm placement"
  - "Note processing workflow: receive notes → extract by category → check duplicates → apply updates → mark processed → archive → report results"
  - "Section mapping table: fact type → target section → format (table/bullets/prose)"
  - "Cross-protocol integration: note processing calls profile building for profile facts"
  - "Command pattern integration: natural language + slash commands both supported"

# Metrics
duration: 5min
completed: 2026-01-24
---

# Phase 03 Plan 02: Profile Building and Note Processing Protocols Summary

**Complete protocols for profile updates and note processing with smart placement, multi-source input, full triage extraction, and itemized reporting**

## Performance

- **Duration:** 5 min
- **Started:** 2026-01-25T01:31:08Z
- **Completed:** 2026-01-25T01:36:19Z
- **Tasks:** 3
- **Files modified:** 3

## Accomplishments

### Profile Building Protocol (605 lines)

- Natural language trigger patterns for profile updates
- Smart section placement mapping for 15+ fact types
- Update mechanics for tables, bullet lists, numbered lists, and prose sections
- Silent update strategy with post-confirmation
- Conflicting information replacement (new is authoritative)
- Error handling for missing entities, corrupted files, ambiguous facts
- Six detailed example interactions
- Integration with entity onboarding and note processing

### Note Processing Protocol (863 lines)

- Three flexible input sources:
  1. Pasted inline in chat
  2. Files in entity notes/ folder
  3. Top-level NOTES.md inbox with auto-routing
- Four-category extraction triage:
  - Tasks → ENTITY_ROADMAP.md
  - Profile facts → ENTITY_PROFILE.md (uses profile-building protocol)
  - Calendar items → roadmap or profile
  - Follow-ups → reported to user for decision
- Duplicate task detection (exact and similar matching)
- Processed note handling:
  - Add `<!-- Processed by Claude on YYYY-MM-DD -->` header
  - Archive to notes/archive/ folder
  - Never delete original notes
- Itemized reporting showing what was extracted and where it went
- Discussion mode for ambiguous or prompt-like content
- Six detailed example interactions covering all scenarios
- Batch processing support for multiple entities

### INSTRUCTIONS.md Integration

- Added Profile Building entry in Core Workflows with protocol reference
- Expanded Note Processing entry with full details and protocol reference
- Added profile update command pattern in Command Patterns table
- Logical workflow order: onboard → build profile → process notes

## Task Commits

Each task was committed atomically:

1. **Task 1: Create profile building protocol** - `796aabd` (docs)
2. **Task 2: Create note processing protocol** - `7149286` (docs)
3. **Task 3: Update INSTRUCTIONS.md** - `1b8bcb5` (docs)

## Files Created/Modified

- `docs/protocols/profile-building.md` - Complete profile building protocol with smart placement, silent updates, section mapping, update mechanics for all format types, conflict resolution, and integration patterns
- `docs/protocols/note-processing.md` - Complete note processing protocol with three input sources, four extraction categories, duplicate detection, processed note archiving, itemized reporting, and discussion mode
- `INSTRUCTIONS.md` - Added Profile Building and expanded Note Processing entries in Core Workflows; added profile update command pattern

## Decisions Made

**Profile Building Approach:**
- Smart placement based on fact type eliminates user needing to know profile structure
- Section mapping table covers 15+ fact types to appropriate profile sections
- Silent updates (make changes, then confirm) avoids interrupting user flow
- New information replaces conflicting old information (profile shows current state)
- Mixed format support: tables for structured data, bullets for lists, prose for context
- Error handling includes missing entities, corrupted files, and ambiguous facts

**Note Processing Approach:**
- Three input sources provide flexibility for different user workflows
- Top-level NOTES.md serves as catch-all inbox with intelligent routing
- Four-category extraction ensures comprehensive triage (nothing gets missed)
- Duplicate task detection prevents roadmap clutter
- Processed notes marked with header AND archived (audit trail preserved)
- Never delete original notes (storage cheap, context expensive)
- Itemized reporting provides transparency about what was extracted
- Discussion mode handles ambiguous or prompt-like content gracefully

**Protocol Integration:**
- Note processing calls profile-building protocol for profile facts (DRY principle)
- Both protocols reference entity onboarding for name normalization
- INSTRUCTIONS.md provides entry points with pointers to detailed protocols
- Command patterns show both natural language and slash commands

**Documentation Style:**
- Followed entity-onboarding.md protocol structure
- Comprehensive examples showing various scenarios
- Integration sections show how protocols work together
- Error handling covers common and edge cases

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None - protocol documentation created smoothly following established patterns from 03-01.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness

**Ready for next plan:**
- Profile building and note processing protocols documented and referenced in INSTRUCTIONS.md
- Foundation established for 03-03 (Git Abstraction Protocol)
- Core "data in" workflows complete (entity creation, profile building, note processing)
- Natural language patterns can be extended for other workflows

**For git abstraction (03-03):**
- Profile building and note processing commit behavior already specified in protocols
- Batched commits pattern established
- Silent operation with error logging pattern established
- Can formalize git abstraction as standalone protocol

**For Phase 4 (Skills):**
- Note processing pattern can be adapted for skill-specific note processing
- Profile building can be used by skills for entity updates
- Entity structure supports skill-specific enhancements

**For Phase 5 (Specialists):**
- Specialist agents can leverage profile building to update entity context
- Note processing can route to specialist-specific tasks
- Profile sections can be extended for specialist-specific information

**No blockers or concerns.**

---
*Phase: 03-entity-management*
*Completed: 2026-01-24*
