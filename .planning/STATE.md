# Agent PM - Project State

**Purpose:** Track current position, progress, and accumulated context across sessions.

---

## Project Reference

**Core Value:** Non-technical PMs can operate a powerful AI project management system without understanding git, command line, or code.

**Target User:** Non-technical project managers comfortable with email and spreadsheets but not command line or git.

**Key Constraints:**
- Must work across Claude Code, opencode, and codex
- Git must be invisible to users
- Standalone operation must work without integrations
- Tutorial-ready for YouTube walkthroughs

---

## Current Position

**Phase:** 3 of 8 (Entity Management Workflows)
**Plan:** 2 of 3 in phase (EXECUTING)
**Status:** Phase 3 Execution In Progress - Wave 2
**Last activity:** 2026-01-24 - Completed 03-02-PLAN.md (Profile Building & Note Processing Protocols)

### Progress

```
Phase 1: Core File Structure     [x] Complete (Plans 01-01, 01-02 done)
Phase 2: Agent Instruction Layer [x] Complete (Plans 02-01, 02-02, 02-03 done)
Phase 3: Entity Management       [-] In Progress (Plans 03-01, 03-02 done; 03-03 pending)
Phase 4: Skill System Foundation [ ] Pending
Phase 5: Specialist Pattern      [ ] Pending
Phase 6: Voice Training System   [ ] Pending
Phase 7: Documentation/Onboard   [ ] Pending
Phase 8: Integration Patterns    [ ] Pending

Overall: [##-_____] 2/8 phases complete, 1 in progress
```

---

## Phase Details

### Phase 1: Core File Structure

**Goal:** Users can create and organize entities using a consistent, semantic file structure.

**Requirements:** CORE-01, CORE-02, CORE-03, CORE-04, CORE-05, CORE-06, ENTY-01, ENTY-02

**Success Criteria:**
1. User can manually create an entity folder that follows the standard structure
2. Entity profile and roadmap templates exist with clear placeholders
3. Master ROADMAP.md shows all active entities with checkbox status tracking
4. Folder names are self-documenting
5. All data is human-readable markdown

**Status:** COMPLETE - Plan 01-01 (folder structure, CONFIG.md, ROADMAP.md) + Plan 01-02 (entity templates)

---

## Performance Metrics

| Metric | Value |
|--------|-------|
| Phases Complete | 2/8 |
| Requirements Complete | 13/42 |
| Plans Created | 6 |
| Plans Executed | 8 |

---

## Accumulated Context

### Key Decisions

| Decision | Rationale | Date |
|----------|-----------|------|
| 8-phase structure | Follows natural delivery boundaries from research; matches YouTube segment structure | 2026-01-24 |
| Sequential phases | Each phase builds on previous; no parallel work streams | 2026-01-24 |
| Foundation before features | File structure and agent instructions must exist before workflows | 2026-01-24 |
| Voice training after specialists | Voice applies to specialist outputs, needs them first | 2026-01-24 |
| Integrations last | Standalone value must be complete before optional enhancements | 2026-01-24 |

### Technical Decisions

| Decision | Rationale | Date |
|----------|-----------|------|
| INSTRUCTIONS.md as source of truth | Agent-agnostic; thin wrappers point to single canonical file | 2026-01-24 |
| Markdown for all data | Human-readable, AI-parseable, no dependencies, future-proof | 2026-01-24 |
| Checkbox syntax for status | Universal: [ ] to-do, [-] in-progress, [x] done | 2026-01-24 |
| Entity-based folder structure | entities/[Name]/ with standard subfolders | 2026-01-24 |
| Entity type defaults to 'client' | Common use case, but configurable in CONFIG.md | 2026-01-24 |
| ENTITY_ prefix for core documents | Distinguishes template-based docs from custom files | 2026-01-24 |
| Priority sections in ROADMAP.md | High/Medium/Low for at-a-glance status | 2026-01-24 |
| [Placeholder Text] format | Visual, self-documenting placeholders users replace | 2026-01-24 |
| Templates standalone | No external dependencies; copy and fill in | 2026-01-24 |
| Session protocol: silent start | No greeting or status summary - just ready to work | 2026-01-24 |
| Hybrid command patterns | Natural language and slash commands both work equally | 2026-01-24 |
| Core behavior + pointers | ~90 lines in INSTRUCTIONS.md with details in docs/protocols/ | 2026-01-24 |
| Entity name normalization | Title case for consistency (ACME CORP → Acme Corp); preserve hyphens, apostrophes, acronyms | 2026-01-24 |
| Duplicate detection before creation | Exact and fuzzy (>80% similarity) matching to prevent duplicate entities | 2026-01-24 |
| Minimal entity creation | Create folder structure immediately, enhance afterward; no upfront questions | 2026-01-24 |
| Next-step suggestions | After creation, proactively suggest what user can do next | 2026-01-24 |
| Protocol documentation format | Trigger detection → normalization → core logic → error handling → examples → integration | 2026-01-24 |
| Smart section placement | Profile updates map fact types to appropriate sections without user needing to specify | 2026-01-24 |
| Silent updates with post-confirmation | Make changes first, confirm after; no pre-confirmation prompts | 2026-01-24 |
| Conflicting info replacement | New information replaces old (profile shows current state only, no history) | 2026-01-24 |
| Three-source note input | Notes from inline paste, entity notes folder, or top-level NOTES.md inbox | 2026-01-24 |
| Four-category extraction | Triage notes into tasks, profile facts, calendar items, and follow-ups | 2026-01-24 |
| Processed note archiving | Mark with header + archive to notes/archive/ (never delete originals) | 2026-01-24 |
| Discussion mode for ambiguity | Pause and clarify when content is prompt-like or genuinely ambiguous | 2026-01-24 |
| Zero git exposure | Users never see git commands, terminology, or errors; automated and silent | 2026-01-24 |
| Batched commits at breakpoints | One commit per logical operation, not per file modification | 2026-01-24 |
| JSON line logging | Structured logs (one JSON object per line) for parseability and debugging | 2026-01-24 |
| Logs gitignored | Logs stay local for debugging, not shared in repository | 2026-01-24 |
| Specialist logging pattern | Document Phase 5 specialist logging now to establish pattern early | 2026-01-24 |

### Discovered During Research

- Reference implementation (ppmc) validates architecture in production
- Three-layer pattern: files as data, agents as processors, natural language as UI
- Dual source of truth works: external tool for status, local repo for context
- Non-technical users need complete git abstraction - zero exposure
- Session protocol critical for context continuity across restarts

### TODOs

- [x] Create Phase 1 detailed plan (01-01-PLAN.md created and executed)
- [x] Validate folder structure against reference implementation (entities/, templates/, ops/)
- [x] Define template placeholder conventions - [Placeholder Text] format established in 01-02
- [x] Establish entity naming conventions - Title case normalization in 03-01

### Blockers

None currently.

---

## Session Continuity

### What to Load on Session Start

1. Read this file (STATE.md) for current position
2. Read ROADMAP.md for phase structure and requirements
3. Read current phase's plan (when created)
4. Check REQUIREMENTS.md for requirement details

### What to Update on Session End

1. Update "Current Position" section with latest status
2. Add any new decisions to "Key Decisions"
3. Add any new blockers or TODOs
4. Update progress bar if phases completed

### Context Files

| File | Purpose | When to Read |
|------|---------|--------------|
| .planning/PROJECT.md | Core value, constraints | Start of project |
| .planning/REQUIREMENTS.md | Full requirement list | When planning phases |
| .planning/ROADMAP.md | Phase structure | Every session |
| .planning/STATE.md | Current position | Every session |
| .planning/research/SUMMARY.md | Research insights | When making decisions |
| .planning/research/ARCHITECTURE.md | Technical patterns | When implementing |

---

## File Locations

```
.planning/
  PROJECT.md          - Project definition
  REQUIREMENTS.md     - All requirements with traceability
  ROADMAP.md          - Phase breakdown and progress
  STATE.md            - This file (current state)
  config.json         - Build configuration
  research/
    SUMMARY.md        - Research summary
    ARCHITECTURE.md   - Architecture patterns
    FEATURES.md       - Feature analysis
    PITFALLS.md       - Risk analysis
    STACK.md          - Technology decisions
```

---

*State initialized: 2026-01-24*
*Last updated: 2026-01-24 - Phase 3 Plan 02 Complete (Profile Building & Note Processing)*
