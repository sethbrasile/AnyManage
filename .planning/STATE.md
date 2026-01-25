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

**Phase:** 7 of 8 (Documentation and Onboarding)
**Plan:** 4 of 4 in phase (COMPLETE)
**Status:** Phase complete
**Last activity:** 2026-01-25 - Completed 07-04-PLAN.md (Video Scripts for YouTube)

### Progress

```
Phase 1: Core File Structure     [x] Complete (Plans 01-01, 01-02 done)
Phase 2: Agent Instruction Layer [x] Complete (Plans 02-01, 02-02, 02-03 done)
Phase 3: Entity Management       [x] Complete (Plans 03-01, 03-02, 03-03 done)
Phase 4: Skill System Foundation [x] Complete (Plans 04-01, 04-02, 04-03 done)
Phase 5: Specialist Pattern      [x] Complete (Plans 05-01, 05-02, 05-03 done)
Phase 6: Voice Training System   [x] Complete (Plans 06-01, 06-02, 06-03 done)
Phase 7: Documentation/Onboard   [x] Complete (Plans 07-01, 07-02, 07-03, 07-04 done)
Phase 8: Integration Patterns    [ ] Pending

Overall: [#######_] 7/8 phases complete
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

### Phase 5: Specialist Pattern

**Goal:** Users can delegate domain-specific work to expert AI personas (specialists) that produce structured deliverables.

**Requirements:** SPEC-01, SPEC-02, SPEC-03

**Success Criteria:**
1. Specialists run in isolated context with domain expertise
2. Knowledge loaded progressively across four layers (Base PM, Domain, Agency, Entity)
3. Deliverables produced to entity deliverables/ folder with date-suffix versioning
4. Sensitivity classification protects internal content
5. Batch processing with progress visibility

**Status:** COMPLETE - Plans 05-01 (specialist infrastructure and assessment specialist), 05-02 (strategy specialist and integration), and 05-03 (specialist coordination protocol) COMPLETE

---

### Phase 6: Voice Training System

**Goal:** Users can train the system to write in their personal voice and tone.

**Requirements:** VOIC-01, VOIC-02, VOIC-03

**Success Criteria:**
1. Voice training exercise guides users through sample collection
2. Voice extraction creates structured profile from samples
3. Voice application generates content matching user's natural style
4. Correction learning improves accuracy over time
5. Graceful degradation for capability-limited environments

**Status:** COMPLETE - Plans 06-01 (voice training infrastructure), 06-02 (voice extraction protocol), and 06-03 (voice application and integration) COMPLETE

**Plans:**
- 06-01: Voice Training Infrastructure - COMPLETE (storage, templates, INSTRUCTIONS.md)
- 06-02: Voice Extraction Protocol - COMPLETE (training workflow, capability assessment, handoff prompt)
- 06-03: Voice Application & Integration - COMPLETE (voice application protocol, correction learning protocol, specialist integration)

---

## Performance Metrics

| Metric | Value |
|--------|-------|
| Phases Complete | 7/8 |
| Requirements Complete | 21/42 |
| Plans Created | 22 |
| Plans Executed | 22 |

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
| .github/skills/ as recommended location | Agent Skills 1.0 standard (Dec 2025) for cross-platform portability | 2026-01-24 |
| Agent-specific directories override | .claude/skills/ or .opencode/skills/ can override .github/skills/ for that agent | 2026-01-24 |
| Skills reference protocols, don't duplicate | Single source of truth in docs/protocols/, skills are thin wrappers | 2026-01-24 |
| Progressive disclosure for skills | ~50 tokens/skill at startup, full skill loaded only on match | 2026-01-24 |
| Skill authoring guide for non-technical users | Enable users to extend system with custom workflows | 2026-01-25 |
| Four-layer knowledge hierarchy (Base→Domain→Agency→Entity) | Specialists access progressive context without overwhelming context window | 2026-01-25 |
| Triple-marker sensitivity classification | Protects internal content via INTERNAL_ prefix, internal/ folders, <!-- INTERNAL --> markers | 2026-01-25 |
| Date-suffixed deliverable versioning | Non-technical users see all versions at a glance without git knowledge | 2026-01-25 |
| Knowledge auto-capture by specialists | Specialists append learned context to LEARNED_CONTEXT.md with timestamp and source | 2026-01-25 |
| Batch processing announce pattern | Users see progress: announce → per-entity updates → final summary | 2026-01-25 |
| Entity knowledge infrastructure | Entities now include knowledge/, deliverables/, internal/ folders at creation | 2026-01-25 |
| Opus model for strategy specialist | Strategy work requires higher quality reasoning than assessment; opus vs sonnet | 2026-01-25 |
| Prerequisite assessment check | Strategy specialist checks for existing assessment before starting, recommends assessment-first | 2026-01-25 |
| Specialist discovery pattern | /specialists command and natural language ("What specialists do you have?") for discovery | 2026-01-25 |
| Dual-location voice storage | ~/.agent-pm/voice/ primary (portable), ops/voice/ fallback (project-specific) | 2026-01-25 |
| Pure markdown voice templates | No YAML frontmatter; matches agent-pm conventions, readable by users and LLMs | 2026-01-25 |
| Content-type based voice training | Different contexts need different patterns; single profile with contextual awareness | 2026-01-25 |
| CORRECTIONS.md for async learning | Users can log corrections without session interruption; continuous improvement | 2026-01-25 |
| Voice profile structure from reference | Proven production structure (Core DNA, patterns, anti-patterns, checklist) | 2026-01-25 |
| Five tone calibration modifiers | professional_client, business_partner, formal, casual, family_client cover business contexts | 2026-01-25 |
| Auto-activation based on content type | Voice "just works" for trained types without user needing to request it | 2026-01-25 |
| Reminder frequency: first 3, then every 5th | Balance between gathering feedback and not being annoying with correction offers | 2026-01-25 |
| Dual correction paths (in-session + async) | Support different workflows: immediate conversational or batched CORRECTIONS.md | 2026-01-25 |
| 2+ examples for pattern confidence | Prevent overfitting to single correction; ensure patterns are systematic | 2026-01-25 |
| Profile age check (6mo/1yr thresholds) | Writing styles evolve; prompt refresh at 6 months, encourage at 1 year | 2026-01-25 |
| Specialist default to professional_client | Client-facing deliverables need professional tone by default | 2026-01-25 |
| Section-level voice application | Narrative sections get voice, data/lists stay neutral for natural deliverables | 2026-01-25 |
| Quick start in README first 50 words | Progressive disclosure - users see one action immediately | 2026-01-25 |
| Conversational tone in guides | No Additionally/Furthermore/Moreover - explain WHY not just WHAT | 2026-01-25 |
| Industry examples in customization | Marketing, software, consulting, healthcare, personal notes - show flexibility | 2026-01-25 |
| Zero terminal experience tutorials | Each setup tutorial explains terminal basics, cd, pwd - complete beginner friendly | 2026-01-25 |
| FAQ troubleshooting format | Symptom -> explanation -> fix steps -> escalation path to GitHub issues | 2026-01-25 |
| Troubleshoot skill references guide | Single source of truth in docs/guides/troubleshooting.md, skill doesn't duplicate | 2026-01-25 |
| A/V format for video scripts | VISUAL/AUDIO columns for clear screen/narration pairing | 2026-01-25 |
| Production notes per section | Reduces editing decisions during video recording | 2026-01-25 |
| Video scripts as one-time deliverable | VIDEO_SCRIPTS/ is for YouTube launch, not a permanent system feature | 2026-01-25 |

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
*Last updated: 2026-01-25 - Completed 07-04-PLAN.md (Video Scripts for YouTube) - Phase 7 complete*
