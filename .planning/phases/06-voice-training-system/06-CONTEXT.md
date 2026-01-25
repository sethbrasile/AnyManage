# Phase 6: Voice Training System - Context

**Gathered:** 2026-01-25
**Status:** Ready for planning

<domain>
## Phase Boundary

Users can train the system to write in their personal voice and tone. The system extracts voice patterns from writing samples and produces content that sounds like the user wrote it. This phase covers voice extraction, profile storage, voice-aware writing, and profile refinement.

**Starting point:** User has provided a complete reference implementation in `.planning/my_voice_example/` with voice profile, LLM implementation guide, quick start guide, and before/after examples. This is the template to productize.

</domain>

<decisions>
## Implementation Decisions

### Capability Assessment (Training Exercise Flow)
- Agent performs capability assessment FIRST before starting voice training
- Check: Do I have Gmail MCP? File upload capability? Or am I terminal-only?
- If terminal-only (e.g., Claude Code without MCP): Steer user to more capable harness (Claude Desktop, ChatGPT web) for the extraction exercise
- Terminal copy-paste requires terminal skills — our target users are non-technical
- Agent provides setup guidance if user wants to configure MCP (e.g., "You could set up Gmail MCP — here's how")

### Handoff for Limited Environments
- Generate a **ready-to-paste extraction prompt** the user takes to Claude Desktop/ChatGPT
- User completes voice extraction in the more capable environment
- User brings the resulting voice profile back to agent-pm
- Prompt should be self-contained with all instructions

### Content Types for Training
- User chooses which content types to train on (3-4 options presented)
- Options should include: emails, technical docs, chat messages, reports/proposals
- User picks which they care about — extraction targets those specifically
- Different content types may have different voice characteristics

### Sample Requirements
- Request 5-10 writing samples per selected content type
- Enough variety for pattern detection without being overwhelming
- Samples should be actual content the user wrote (emails sent, docs authored)

### Voice Profile Storage
- **Primary location:** `~/.agent-pm/voice/` (user home directory) — portable across projects
- **Fallback location:** `ops/voice/` (project-level) — if home directory profile not found
- Check home directory first, fall back to project level
- This allows personal voice to follow user across agent-pm installations

### Multiple Voices
- **One profile, context-adapted** (not multiple named profiles)
- Single voice profile with tone calibration modifiers
- Modifiers for: professional client, business partner/team, family member client, formal, casual
- Matches the pattern in user's existing `llm_implementation_guide.md`

### File Format
- Pure markdown, matching structure of `seth_voice_profile.md`
- Human-readable, AI-parseable
- No YAML frontmatter — keep it simple for non-technical users
- Sections: Core Voice DNA, patterns by content type, anti-patterns, quality checklist

### Voice Activation (When Writing)
- **Content-type based auto-activation**
- Claude's discretion on which content types auto-apply voice:
  - Likely auto: client emails, proposals, external communications
  - Likely manual: internal notes, technical documentation, code comments
- User can always explicitly request voice or suppress it

### Post-Extraction Experience
- Display full voice profile for user review before saving
- User can edit/approve the profile
- Clear "save" action after review
- Profile saved to appropriate location based on storage decision

### Profile Refinement
- **Two paths for learning from corrections:**
  1. **In-session:** User says "Here's what I changed: [paste]" — agent updates profile in real-time
  2. **Async:** User pastes corrections to `ops/voice/CORRECTIONS.md` (append-only log)
- Agent suggests conversation when in session, file when working async
- System should ALWAYS learn from corrections, even if user manually edits profile file

### Correction UX (Critical)
- When generating voice-applied content, include reminder: "Did you make edits before sending? Paste your edits so I can learn!"
- Make it clear WHERE to paste (conversation or file location)
- System must track: what was generated vs what user corrected
- Corrections file format: AI output, user's edit, what changed (for pattern extraction)

### Claude's Discretion
- Exact voice extraction prompts and instructions
- How to structure the capability assessment flow
- Which content types auto-apply voice
- How frequently to show correction reminders (not every time — that would be annoying)
- How to process CORRECTIONS.md and update voice profile

</decisions>

<specifics>
## Specific Ideas

- Reference implementation exists in `.planning/my_voice_example/` — this is the template
- Voice profile structure follows `seth_voice_profile.md` format: Core Voice DNA, patterns, anti-patterns
- LLM Implementation Guide pattern (modular prompts per use case) should be adopted
- Before/After Examples document is valuable for user understanding and AI training
- Quick Start Guide provides the 5-minute TL;DR users need
- "lol" as a softener, parenthetical asides, strategic hedging — these are the kind of specific patterns to extract
- Anti-pattern lists ("NEVER USE: 'I hope this email finds you well'") are highly effective

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 06-voice-training-system*
*Context gathered: 2026-01-25*
