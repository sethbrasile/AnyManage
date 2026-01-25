# Phase 3: Entity Management Workflows - Context

**Gathered:** 2026-01-24
**Status:** Ready for planning

<domain>
## Phase Boundary

Users can onboard and manage entities through natural language commands. This includes:
- Creating new entities with folder structure
- Incrementally building entity profiles
- Processing raw notes into structured tasks, profile updates, and follow-ups
- Git operations happen invisibly — users never see git terminology

This phase does NOT include: skills (Phase 4), specialists (Phase 5), or voice training (Phase 6).

</domain>

<decisions>
## Implementation Decisions

### Onboarding Flow
- **Minimal creation** — "Add new client Acme" creates folder structure immediately, no upfront questions
- **Normalized naming** — Entity names converted to title case ("ACME Corp" → "Acme Corp")
- **Next step prompt** — After creation, confirm + suggest what to do next ("Add some details? Process notes?")
- **Duplicate handling** — If entity exists, offer to create variant ("Acme Corp 2") or let user specify different name

### Profile Building
- **Smart placement** — New facts inserted contextually within relevant profile sections
- **Silent updates** — No confirmation before changes; confirm after ("Added to Background section")
- **Replace old info** — Conflicting facts replace outdated ones (no history tracking in profile itself)
- **Mixed format** — Bullet points for quick facts, prose for context/narrative sections

### Note Processing
- **Three input sources:**
  1. Pasted inline in chat
  2. File in entity's notes/ folder
  3. Top-level NOTES.md — AI routes to correct entity or internal ops
- **Full triage extraction:**
  - Tasks → entity roadmap
  - Profile facts → entity profile
  - Calendar items → noted in roadmap or ops
  - Follow-ups → tracked appropriately
- **Processed notes:** Mark with "Processed by Claude on [date]" header AND archive to notes/archive/
- **Itemized reporting:** Show each extracted item and where it went
- **Discussion mode:** Ambiguous items or "prompt-like" notes trigger clarification dialogue with user
- **Reference methodology:** Adapt process-client-notes skill pattern (harvest tasks, update knowledge, refine strategy, mark complete, report)

### Git Abstraction
- **Zero git exposure** — Users never see git terminology, commands, errors, or concepts
- **Batched commits** — Changes accumulated, committed at natural workflow breakpoints
- **Descriptive commit messages** — Human-readable history ("Updated Acme Corp profile with CEO change")
- **Silent retry on errors** — Automatic retry, persistent failures surface as generic "couldn't save"
- **Error logging** — Failures logged to gitignored ops/logs/ folder for admin debugging

### Claude's Discretion
- Exact folder structure within entities/[Name]/
- How to detect duplicate entities (exact match, fuzzy match)
- When exactly to trigger batch commits (session end, after workflows, etc.)
- Prompt phrasing for next step suggestions

</decisions>

<specifics>
## Specific Ideas

- Refer to ppmc's process-client-notes skill for methodology:
  - Harvest tasks, update knowledge, refine strategy, mark complete, report
  - Never delete source notes
  - Check for duplicate tasks before adding
- Notes can be "prompt-like" — some notes are actually instructions for the agent, not just data to process
- Top-level NOTES.md is a catch-all inbox; AI figures out routing (internal vs entity-specific)

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 03-entity-management*
*Context gathered: 2026-01-24*
