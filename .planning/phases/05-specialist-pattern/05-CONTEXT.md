# Phase 5: Specialist Pattern - Context

**Gathered:** 2026-01-25
**Status:** Ready for planning

<domain>
## Phase Boundary

Users can delegate domain-specific work to expert AI personas (specialists) that produce structured deliverables. Specialists access layered knowledge (base PM, domain, agency, entity) and can auto-capture learned context. Example specialists: assessment-specialist, strategy-specialist.

</domain>

<decisions>
## Implementation Decisions

### Specialist Invocation
- Natural language OR explicit naming both work ("Run assessment for Acme" or "Use assessment-specialist for Acme")
- Brief announce before starting: "Starting assessment for Acme..."
- No formal parameters — user gives any instruction, specialist interprets (focus areas, specific commands, etc.)
- Batch operations supported: "Run assessment for all clients" or "for Acme and Beta Corp"

### Deliverable Structure
- Save to dedicated subfolder: `entities/[Entity]/deliverables/`
- No metadata in files — pure content (file location/name provide context)
- Date suffix for versioning: `AUDIT_FINDINGS_2026-01-25.md` — keeps all versions visible
- Template-based when template exists, otherwise adaptive structure; users can request templatization

### Knowledge Layering
- Four layers: Base PM → Domain → Agency → Entity
- Specialist uses judgment to weigh conflicting information across layers (no rigid priority)
- Progressive loading: summary first, fetch details as needed
- Domain knowledge location: `docs/domain/`
- Agency knowledge location: `ops/` organized by topic (`ops/preferences/`, `ops/processes/`, `ops/style/`)
- Entity knowledge location: `entities/[Entity]/knowledge/` subfolder (separate from profile)
- Specialists can auto-capture learned context, with brief note at end ("Added [X] to entity knowledge")
- Manual review for knowledge maintenance — user cleans up periodically

### Sensitivity Classification
- Both file-level and section-level internal markers
- File-level: `INTERNAL_*.md` naming or `internal/` subfolder
- Section-level: `<!-- INTERNAL -->` markers for mixed content
- Block generation if internal content would appear in client-facing output — warn user first

### Coordination Model
- Pointer-based handoff: main agent tells specialist what files to read
- No logging — deliverable is the record
- Summarize results: "Completed assessment. Key findings: [summary]" with link to file
- Per-entity updates during batch: "Completed Acme... Completed Beta Corp..." as each finishes

### Claude's Discretion
- Specific specialist prompt structure
- How to detect client-facing vs internal output context
- Progressive loading implementation details
- Knowledge capture format within entity knowledge folder

</decisions>

<specifics>
## Specific Ideas

- User emphasized: specialists are AI subagents that can handle any instruction, not just pre-defined parameters
- Internal content protection is critical — user stores pricing, margins, internal notes that must never reach clients
- Knowledge accumulates over time — specialists learn about entities through their work

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 05-specialist-pattern*
*Context gathered: 2026-01-25*
