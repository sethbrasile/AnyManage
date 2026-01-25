---
phase: 07-documentation-onboarding
plan: 04
subsystem: video-scripts
tags: [youtube, video-scripts, av-format, seth-voice, one-time-deliverable]

# Dependency graph
requires:
  - phase: 07-01
    provides: README and foundational guides
  - phase: 07-02
    provides: Reference example to demonstrate
  - phase: 07-03
    provides: Troubleshooting guide for support
provides:
  - VIDEO_SCRIPTS/ directory with 5 video scripts
  - A/V format scripts for YouTube recording
  - Seth's voice applied throughout
  - Series structure: intro, setup, first client, notes, voice
affects: [youtube-launch, user-onboarding]

# Tech tracking
tech-stack:
  added: []
  patterns: [av-format-scripting, voice-application, series-structure]

key-files:
  created:
    - VIDEO_SCRIPTS/01-intro-demo.md
    - VIDEO_SCRIPTS/02-setup-detailed.md
    - VIDEO_SCRIPTS/03-first-client.md
    - VIDEO_SCRIPTS/04-processing-notes.md
    - VIDEO_SCRIPTS/05-voice-training.md

key-decisions:
  - "A/V table format: VISUAL | AUDIO columns for clear screen/narration pairing"
  - "Production notes after each section (pause timing, zoom cues, font size)"
  - "YouTube description text and timestamps included in each script"
  - "Voice checklist at end of each script for recording verification"
  - "Series cross-references: each video references previous/next"

patterns-established:
  - "Video script structure: metadata, sections with A/V tables, production notes, description, timestamps"
  - "Voice application in narration: contractions, parentheticals, 'lol', no formality"
  - "Brief closings (no 'thank you for watching')"

# Metrics
duration: 4min
completed: 2026-01-25
---

# Phase 7 Plan 04: Video Scripts for YouTube Summary

**Five video scripts in Seth's voice using A/V format for YouTube tutorial series launch**

## Performance

- **Duration:** 4 min
- **Started:** 2026-01-25T20:26:19Z
- **Completed:** 2026-01-25T20:30:38Z
- **Tasks:** 3
- **Files created:** 5

## Accomplishments

- Created VIDEO_SCRIPTS/ directory as one-time deliverable for YouTube launch
- All 5 scripts use two-column A/V format (VISUAL | AUDIO)
- Production notes after each section for recording guidance
- YouTube description text and timestamps included
- Seth's voice markers throughout (137 instances of contractions, parentheticals, "lol")
- Series flows coherently: intro -> setup -> client -> notes -> voice

## Task Commits

Each task was committed atomically:

1. **Task 1: Create intro/demo video script** - `bb90fa5` (feat)
2. **Task 2: Create setup and first client scripts** - `5ab2246` (feat)
3. **Task 3: Create processing notes and voice training scripts** - `f86536f` (feat)

## Files Created

- `VIDEO_SCRIPTS/01-intro-demo.md` - Overview and quick demo (153 lines)
- `VIDEO_SCRIPTS/02-setup-detailed.md` - Full terminal-to-running walkthrough (212 lines)
- `VIDEO_SCRIPTS/03-first-client.md` - Entity concept and profile management (180 lines)
- `VIDEO_SCRIPTS/04-processing-notes.md` - Note processing workflow (175 lines)
- `VIDEO_SCRIPTS/05-voice-training.md` - Voice training feature (180 lines)

## Script Structure

Each script contains:

| Section | Purpose |
|---------|---------|
| Metadata | Title, length, audience, tone |
| A/V Sections | Visual/audio paired tables |
| Production Notes | Recording guidance per section |
| Description Text | YouTube description to copy-paste |
| Timestamps | Markers to add after upload |
| Voice Checklist | Final verification before recording |

## Voice Markers Applied

From `.planning/my_voice_example/seth_voice_profile.md`:

- Contractions used naturally (you'll, don't, it's, that's, I'll)
- Parenthetical asides in every script
- "lol" used when genuinely appropriate
- No "I hope this video finds you well" or formal openings
- No "Thank you so much for watching" - brief, direct closings
- Explains WHY not just WHAT

## Decisions Made

| Decision | Rationale |
|----------|-----------|
| A/V table format | Industry standard for video production, clear visual/audio pairing |
| Production notes per section | Reduces editing decisions during recording |
| Description text included | Seth can copy-paste directly to YouTube |
| Timestamps included | Ready to add after upload timing is known |
| Voice checklist | Final verification that script matches voice profile |
| Series cross-references | Each video naturally leads to next |

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None.

## User Setup Required

None - scripts are ready for recording.

## Verification Results

| Check | Result |
|-------|--------|
| VIDEO_SCRIPTS/ directory exists | Yes |
| All 5 scripts present | Yes |
| Each uses A/V table format | 5/5 |
| Production notes in each | 5/5 |
| Description text in each | 5/5 |
| Timestamps in each | 5/5 |
| Seth's voice markers | 137 instances |
| Intro references series | Yes |
| Series flows coherently | Yes |

## Success Criteria Met

- [x] Seth can record videos using scripts as-is (minimal rewriting needed)
- [x] Scripts sound like Seth wrote them, not AI
- [x] A/V format makes recording straightforward
- [x] Series covers: overview, setup, entities, notes, voice training
- [x] Production notes reduce editing decisions during recording

## Next Phase Readiness

- Phase 7 (Documentation and Onboarding) complete
- All 4 plans executed:
  - 07-01: README and foundational guides
  - 07-02: Reference example
  - 07-03: Agent tutorials and troubleshooting
  - 07-04: Video scripts (this plan)
- Ready for Phase 8 (Integration Patterns)

---
*Phase: 07-documentation-onboarding*
*Plan: 04*
*Completed: 2026-01-25*
