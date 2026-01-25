---
phase: 07-documentation-onboarding
plan: 03
subsystem: docs
tags: [setup-tutorials, troubleshooting, agent-specific, faq, skill]

# Dependency graph
requires:
  - phase: 07-01
    provides: README entry point and foundational guides
provides:
  - Agent-specific setup tutorials (Claude Code, opencode, codex)
  - FAQ-format troubleshooting guide
  - Troubleshoot skill for agent-assisted diagnosis
affects: [07-04, users, onboarding, standalone-operation]

# Tech tracking
tech-stack:
  added: []
  patterns: [zero-terminal-experience, keystroke-level-detail, faq-format]

key-files:
  created:
    - docs/guides/claude-code-setup.md
    - docs/guides/opencode-setup.md
    - docs/guides/codex-setup.md
    - docs/guides/troubleshooting.md
    - .github/skills/troubleshoot/SKILL.md

key-decisions:
  - "Platform-specific terminal instructions (macOS/Windows/Linux)"
  - "Each step shows what success looks like"
  - "22 FAQ entries covering all major issue categories"
  - "Skill references guide as knowledge base (no duplication)"
  - "GitHub issues as escalation path for unresolved problems"

patterns-established:
  - "Setup tutorials explain WHY not just WHAT"
  - "Troubleshooting follows symptom -> explanation -> fix -> escalation"
  - "Agent-assisted diagnosis via skill pattern"

# Metrics
duration: 4min
completed: 2026-01-25
---

# Phase 7 Plan 03: Agent-Specific Tutorials and Troubleshooting Summary

**Agent-specific setup tutorials for all three platforms plus comprehensive troubleshooting guide with matching skill for agent-assisted diagnosis**

## Performance

- **Duration:** 4 min
- **Started:** 2026-01-25T20:20:32Z
- **Completed:** 2026-01-25T20:23:59Z
- **Tasks:** 3
- **Files created:** 5

## Accomplishments

- Created Claude Code setup tutorial with keystroke-level detail for beginners
- Created opencode and codex tutorials following same structure
- Created FAQ-format troubleshooting guide with 22 common issues
- Created troubleshoot skill so agent can assist with diagnosis
- All tutorials assume zero terminal experience
- Troubleshooting guide has GitHub issues escalation path

## Task Commits

Each task was committed atomically:

1. **Task 1: Create Claude Code setup tutorial** - `c065c53` (docs)
2. **Task 2: Create opencode and codex setup tutorials** - `dc94bab` (docs)
3. **Task 3: Create troubleshooting guide and skill** - `ea070b9` (docs)

## Files Created

- `docs/guides/claude-code-setup.md` - Step-by-step Claude Code setup (243 lines)
- `docs/guides/opencode-setup.md` - Step-by-step opencode setup (229 lines)
- `docs/guides/codex-setup.md` - Step-by-step codex setup (246 lines)
- `docs/guides/troubleshooting.md` - FAQ troubleshooting guide (420 lines)
- `.github/skills/troubleshoot/SKILL.md` - Agent-assisted troubleshooting skill

## Decisions Made

- Each tutorial includes platform-specific terminal instructions (macOS Cmd+Space, Windows key, Linux Ctrl+Alt+T)
- Every step shows what success looks like so users never wonder "did that work?"
- Troubleshooting uses FAQ format with consistent structure: symptom -> explanation -> fix steps -> escalation
- Troubleshoot skill references docs/guides/troubleshooting.md as single source of truth (doesn't duplicate content)
- All GitHub issues links point to placeholder URL (github.com/your-repo/agent-pm/issues) for easy find-replace
- Agent-specific notes document known differences between platforms

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None.

## User Setup Required

None - no external service configuration required.

## Verification Results

| Check | Result |
|-------|--------|
| claude-code-setup.md exists with 100+ lines | 243 lines |
| opencode-setup.md exists with tutorial structure | 229 lines |
| codex-setup.md exists with tutorial structure | 246 lines |
| troubleshooting.md exists with FAQ format | 420 lines, 22 FAQ entries |
| .github/skills/troubleshoot/SKILL.md exists | Created with knowledge base reference |
| All tutorials assume zero terminal experience | Verified - explains terminal, cd, pwd |
| Troubleshooting has escalation path to GitHub issues | 21 GitHub issues links |
| Conversational tone throughout | No "Additionally/Furthermore" |

## Success Criteria Met

- [x] User with no terminal experience can complete any tutorial
- [x] User can self-serve most common issues via troubleshooting guide
- [x] Agent can assist with diagnosis using troubleshooting skill
- [x] All three major agents have tailored tutorials
- [x] System works completely standalone (INTG-01 satisfied)

## Next Phase Readiness

- All three agent platforms now have dedicated setup tutorials
- Troubleshooting guide covers installation, startup, entity management, skills, voice, and API issues
- Agent-assisted troubleshooting skill enables interactive diagnosis
- Ready for 07-04 (Video Scripts)

---
*Phase: 07-documentation-onboarding*
*Plan: 03*
*Completed: 2026-01-25*
