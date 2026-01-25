# Phase 7: Documentation and Onboarding - Context

**Gathered:** 2026-01-25
**Status:** Ready for planning

<domain>
## Phase Boundary

Documentation that enables non-technical users to set up and operate the system without external help. Includes beginner tutorials, reference example, troubleshooting guide, and one-time video scripts for YouTube walkthrough. Must support standalone operation (no integrations required).

</domain>

<decisions>
## Implementation Decisions

### Tutorial Structure
- Separate tutorials per agent (Claude Code, opencode, codex) — tailored experience for each
- Assumes complete beginner — covers installing the agent, terminal basics, everything from zero
- Conversational guide tone — "Think of this like..." explains why, uses analogies, builds mental model
- Claude decides on single-flow vs chaptered organization based on content needs

### Reference Example Scope
- Progressive layers approach — start minimal, show "before/after" states for each workflow
- Generic marketing agency (digital marketing, social media, content) — broadly relatable
- Include generated deliverables (assessment reports, strategy docs) so users see specialist outputs
- Claude decides client count based on what demonstrates features well

### Video Scripts (One-Time Deliverable)
- **Not a permanent system feature** — ephemeral need for initial YouTube launch
- Separate VIDEO_SCRIPT.md files with full production notes (script, screen actions, zoom cues, transitions)
- Series of short videos: intro/demo with setup overview, standalone detailed setup video, then feature videos
- Intro video alludes to detailed setup video and upcoming series
- Use voice heavily from `.planning/my_voice_example/` — should sound like Seth wrote it
- Script + actions paired: "SAY: ..." then "SHOW: type 'add client Acme'"

### Troubleshooting
- FAQ format for user-facing docs — scannable, direct answers
- Detailed troubleshooting docs AND skill for agent — agent has full context to help users diagnose
- GitHub issues link for escalation — docs direct users to open issues for uncovered problems
- Claude identifies likely issues based on system design (not just basics)

### Claude's Discretion
- Tutorial organization (single flow vs chapters)
- Number of example clients in reference agency
- Troubleshooting doc structure (unified vs per-agent) based on actual issue differences
- Specific issues to cover based on system architecture analysis

</decisions>

<specifics>
## Specific Ideas

- "I will be recording the video" — scripts must be in Seth's voice, first-person delivery
- Voice example in `.planning/my_voice_example/` is the reference for video script tone
- Video series structure: intro/demo → detailed setup → feature walkthroughs
- Agent should understand system well enough to help users who describe issues or missed expectations

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 07-documentation-onboarding*
*Context gathered: 2026-01-25*
