# Phase 06 Plan 01: Voice Training Infrastructure Summary

Voice training infrastructure established - storage conventions, templates, and INSTRUCTIONS.md integration complete.

**One-liner:** Voice storage directories, training exercise template (5-step workflow), voice profile template (Core DNA + patterns), and INSTRUCTIONS.md integration with dual-location support.

**Phase:** 6 - Voice Training System
**Plan:** 01
**Status:** Complete
**Date:** 2026-01-25

---

## What Was Built

### Storage Infrastructure
- Created `ops/voice/` directory for project-level voice profiles (fallback location)
- Documented storage priority: `~/.agent-pm/voice/` (primary, portable) → `ops/voice/` (fallback)
- Established CORRECTIONS.md convention for async learning

### User-Facing Templates
- **Voice Training Exercise** (`templates/VOICE_TRAINING_EXERCISE.md`): 5-step guided workflow for non-technical users
  - Step 1: Choose content types (emails, technical docs, chat, reports)
  - Step 2: Gather 5-10 writing samples per type
  - Step 3: Run voice training via natural language or `/train-voice`
  - Step 4: Review and approve extracted profile
  - Step 5: Learn from corrections (in-session or async)

- **Voice Profile Template** (`templates/VOICE_PROFILE_TEMPLATE.md`): Structured template for LLM extraction
  - Core Voice DNA (sentence structure, punctuation, signature traits)
  - Email Patterns (openings, body, closings - DO/DON'T format)
  - Technical Communication patterns
  - Chat/Informal patterns
  - Relationship calibration (professional/team/casual)
  - AI anti-patterns (what AI does wrong vs. user's actual style)
  - Quality checklist

### System Integration
- Added Voice Training section to INSTRUCTIONS.md (after Specialists, before Code Style)
- Documented discovery triggers ("Train my voice", `/train-voice`)
- Documented content types and sample requirements
- Documented storage locations and fallback behavior
- Documented correction learning paths (in-session and CORRECTIONS.md)
- Referenced future protocol (`docs/protocols/voice-training.md` - to be created in Plan 02)

---

## Tasks Completed

| Task | Name | Commit | Files |
|------|------|--------|-------|
| 1 | Create voice storage directory and conventions | a04864e | ops/voice/.gitkeep |
| 2 | Create voice training exercise template | 4baa2b4 | templates/VOICE_TRAINING_EXERCISE.md |
| 3 | Create voice profile template | a382d63 | templates/VOICE_PROFILE_TEMPLATE.md |
| 4 | Add Voice Training section to INSTRUCTIONS.md | 204b6ff | INSTRUCTIONS.md |

---

## Key Files

**Created:**
- `ops/voice/.gitkeep` - Storage directory with conventions documentation
- `templates/VOICE_TRAINING_EXERCISE.md` - User-facing 5-step training guide (110 lines)
- `templates/VOICE_PROFILE_TEMPLATE.md` - LLM-fillable voice profile structure (158 lines)

**Modified:**
- `INSTRUCTIONS.md` - Added Voice Training section with comprehensive documentation

---

## Decisions Made

| Decision | Rationale | Impact |
|----------|-----------|--------|
| Dual-location storage (~/.agent-pm/voice/ primary, ops/voice/ fallback) | User portability across projects while supporting project-specific overrides | Voice profiles portable by default, project-specific when needed |
| Pure markdown templates (no YAML frontmatter) | Matches established agent-pm conventions; readable by users and LLMs | Consistent with all other templates in system |
| 5-step training workflow | Matches reference implementation; appropriate complexity for non-technical users | Clear path from samples to working voice profile |
| Voice profile structure matches reference | Proven structure from production usage | High confidence in coverage and effectiveness |
| CORRECTIONS.md for async learning | Allows users to log corrections without interrupting session | Supports continuous improvement without session overhead |
| Content-type based training | Different writing contexts need different voice patterns | Single profile with contextual awareness |
| Capability assessment note | Terminal-only environments can't upload files; need portable prompt fallback | Graceful degradation for limited environments |

---

## Technical Implementation

### Storage Convention
```
Primary:  ~/.agent-pm/voice/voice_profile.md (portable)
Fallback: ops/voice/voice_profile.md (project-specific)
```

Agent checks primary first, falls back if not found. Home directory created by training process (not pre-created).

### Template Structure
Both templates follow agent-pm conventions:
- Pure markdown (no YAML)
- Clear placeholders for LLM or user filling
- User-facing language (no technical jargon)
- Checkbox syntax for quality checklist

### INSTRUCTIONS.md Integration
Voice Training section positioned after Specialists (both are advanced features) and before Code Style. Includes:
- Discovery triggers
- Content types
- Sample requirements
- Storage locations
- Voice application rules
- Correction learning
- Capability assessment note
- References to detailed protocol and training exercise

---

## Dependencies

**Requires:**
- Phase 5 (Specialist Pattern) - Voice system follows specialist conventions for deliverables and sensitivity

**Provides:**
- Voice training infrastructure for Plan 02 (voice extraction protocol)
- Storage conventions for voice profiles
- User-facing training workflow

**Affects:**
- Plan 02 (Voice Extraction Protocol) - Will implement the training workflow documented here
- Plan 03 (Voice Application Protocol) - Will use stored voice profiles to generate content

---

## What's Next

**Plan 02 - Voice Extraction Protocol:**
- Implement voice training workflow
- Create docs/protocols/voice-training.md
- Build portable extraction prompt for capability-limited environments
- Handle sample collection and pattern extraction

**Plan 03 - Voice Application Protocol:**
- Implement voice profile loading
- Content-type detection and auto-activation
- Correction learning implementation
- Voice-aware content generation

---

## Validation

**Must-have truths verified:**
- ✓ User can find voice training documentation in INSTRUCTIONS.md
- ✓ Voice training template exists for guided sample collection
- ✓ Voice profile template matches reference implementation structure
- ✓ Storage conventions documented for both primary and fallback locations

**Artifact checks:**
- ✓ `templates/VOICE_TRAINING_EXERCISE.md` exists (110 lines, min 80 required)
- ✓ `templates/VOICE_PROFILE_TEMPLATE.md` exists (158 lines, min 100 required)
- ✓ `INSTRUCTIONS.md` contains "Voice Training" section
- ✓ `ops/voice/.gitkeep` exists with storage documentation
- ✓ INSTRUCTIONS.md references `docs/protocols/voice-training.md`

**Success criteria:**
1. ✓ User can find voice training documentation in INSTRUCTIONS.md
2. ✓ Voice training exercise template provides clear 5-step workflow
3. ✓ Voice profile template matches reference implementation structure (Core Voice DNA, patterns, anti-patterns, checklist)
4. ✓ Storage conventions documented for both `~/.agent-pm/voice/` and `ops/voice/`
5. ✓ All files are pure markdown with no YAML frontmatter

---

## Deviations from Plan

None - plan executed exactly as written.

---

## Metrics

- **Duration:** ~2 minutes
- **Tasks completed:** 4/4
- **Files created:** 3
- **Files modified:** 1
- **Lines added:** 283 (110 exercise + 158 profile + 15 gitkeep)
- **Commits:** 4 (one per task)

---

## Tags

`voice-training` `infrastructure` `templates` `storage` `user-facing` `markdown`

---

## Context for Future Work

**Voice training system architecture:**
1. **Plan 01 (this plan):** Infrastructure - storage, templates, documentation
2. **Plan 02 (next):** Extraction - implement training workflow, pattern extraction
3. **Plan 03 (future):** Application - use profiles to generate content, learn from corrections

**Key design principles established:**
- User portability (home directory primary)
- Project flexibility (ops/voice/ fallback)
- Single profile with contextual adaptation (not multiple profiles)
- Continuous learning (CORRECTIONS.md)
- Graceful degradation (portable prompt for limited environments)
- Non-technical user focus (5-step guided workflow)

**Reference implementation validation:**
Seth's voice profile structure proved comprehensive in production usage. This plan directly adopts that structure for agent-pm users.
