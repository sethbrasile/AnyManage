# Phase 05 Plan 02: Strategy Specialist and Integration Summary

**One-liner:** Opus-powered strategy specialist with assessment prerequisite checks, integrated into INSTRUCTIONS.md specialist system

---

## Metadata

**Phase:** 05-specialist-pattern
**Plan:** 02
**Subsystem:** specialist-system
**Tags:** ai-subagents, strategy-planning, strategic-vision, deliverable-templates, knowledge-layering

**Dependency Graph:**
- requires: [05-01-specialist-infrastructure]
- provides: [strategy-specialist, specialist-system-documentation]
- affects: [05-03-specialist-coordination, future-specialist-additions]

**Tech Stack:**
- added: []
- patterns: [strategy-specialist-pattern, opus-for-strategy, prerequisite-checking]

**Key Files:**
- created: [.claude/agents/strategy-specialist.md, templates/deliverables/STRATEGY_PLAN_TEMPLATE.md]
- modified: [INSTRUCTIONS.md]

**Decisions:**
- use-opus-for-strategy: Strategy specialist uses opus model for higher quality strategic reasoning vs sonnet for assessment
- prerequisite-checking: Strategy specialist checks for existing assessment before starting work
- specialist-discovery-pattern: Users can discover specialists via "/specialists" command or natural language
- four-layer-knowledge: Specialists access Base PM, Domain, Agency, and Entity knowledge layers

**Metrics:**
- duration: 9 minutes
- completed: 2026-01-25

---

## What Was Built

Created the strategy specialist (completing the assessment/strategy specialist pair) and integrated the specialist system into INSTRUCTIONS.md to make specialists discoverable and invokable.

### Strategy Specialist

Created `.claude/agents/strategy-specialist.md` with:
- **Model:** opus (vs sonnet for assessment) — higher quality reasoning for complex strategic decisions
- **Prerequisites:** Checks for existing assessment before starting, recommends assessment-first if none found
- **Knowledge Loading:** Same four-layer protocol as assessment-specialist (Base PM → Domain → Agency → Entity)
- **Sensitivity:** Same internal content detection as assessment-specialist
- **Deliverable:** STRATEGY_PLAN_[DATE].md with Strategic Vision, Goals, Phased Strategy, Resources, Risks, Metrics

### Strategy Plan Template

Created `templates/deliverables/STRATEGY_PLAN_TEMPLATE.md` with comprehensive structure:
- Strategic Vision (future state)
- Goals (Primary + Supporting)
- Strategy (Phased: Phase 1, Phase 2, Phase 3)
- Resource Requirements (Budget, Team, Tools, Timeline)
- Risks and Mitigations (table format)
- Success Metrics (baseline → target)

Pure content only — no metadata/frontmatter.

### INSTRUCTIONS.md Integration

Added "Specialists" section to INSTRUCTIONS.md (after Skills, before Code Style):
- **Discovery:** "/specialists" command or "What specialists do you have?"
- **Invocation:** Natural language ("Run assessment for Acme") or explicit ("Use assessment-specialist for Acme")
- **Batch operations:** "Run assessment for all clients"
- **Available specialists:** Lists assessment-specialist and strategy-specialist with descriptions
- **Knowledge layers:** Documents 4-layer hierarchy (Base PM, Domain, Agency, Entity)
- **Deliverables:** Documents location (entities/[Entity]/deliverables/) and naming (date-suffix)
- **Sensitivity:** Documents internal content protection
- **Storage:** Documents .claude/agents/ (project) and ~/.claude/agents/ (personal)
- **Protocol reference:** Points to docs/protocols/specialist-coordination.md

## Key Decisions

### Decision: Opus Model for Strategy

**Context:** Strategy work requires higher quality reasoning than assessment work.

**Options:**
1. Use sonnet (same as assessment) — cost-effective, consistent
2. Use opus — higher quality but more expensive
3. Use inherit (main conversation model) — variable quality

**Chosen:** Opus model for strategy-specialist

**Rationale:**
- Strategic planning involves complex tradeoff analysis, long-term thinking, and nuanced judgment
- Assessment is more structured/formulaic — sonnet is sufficient
- Quality difference justifies cost for strategy work
- Explicit model selection prevents degraded strategic output

**Impact:** Strategy deliverables will have higher quality reasoning, more sophisticated phase planning, better risk analysis

### Decision: Prerequisite Assessment Check

**Context:** Strategy development should be informed by assessment findings.

**Chosen:** Strategy specialist checks for existing AUDIT_FINDINGS_*.md before starting

**Behavior:**
- If no assessment found: Recommend running assessment first
- If assessment found: Use as input for strategy development
- User can override recommendation if needed

**Rationale:**
- Strategy without assessment risks addressing wrong problems
- Assessment provides baseline data for strategic planning
- Flexible — doesn't block strategy work, just recommends best practice

**Impact:** Users naturally follow assessment → strategy workflow, better informed strategic planning

### Decision: Discovery Pattern

**Context:** Users need to know what specialists exist.

**Chosen:** "/specialists" command + natural language ("What specialists do you have?")

**Rationale:**
- Consistent with skill discovery pattern from Phase 4
- Natural language maintains non-technical UX
- Explicit command for users who prefer slash commands

**Impact:** Specialists are discoverable without reading documentation

## Architecture Notes

### Specialist Pair Pattern

Assessment and strategy specialists form a complementary pair:
- **Assessment:** Current state, gaps, issues → sonnet-powered
- **Strategy:** Future state, roadmap, plans → opus-powered
- **Handoff:** File-based via deliverables/ folder
- **Workflow:** Assessment → Strategy (recommended but not enforced)

### Knowledge Layer Implementation

All specialists use same four-layer knowledge loading:
1. **Base PM:** INSTRUCTIONS.md, docs/protocols/ — core agent behavior
2. **Domain:** docs/domain/ — industry-specific playbooks
3. **Agency:** ops/ — company preferences, processes, style
4. **Entity:** entities/[Entity]/ — client/project-specific context

Progressive loading: Summary first, fetch details as needed. Prevents context window overflow.

### Template-Based Deliverables

Templates provide structure guidance, not rigid enforcement:
- Specialist reads template before generating deliverable
- Template shows sections, what goes in each, what good looks like
- Specialist adapts based on entity context and assessment findings
- Pure content output — no metadata in deliverable files

## Testing & Validation

### Strategy Specialist Verification

Verified strategy-specialist.md contains:
- ✓ Valid YAML frontmatter (name, description, tools, model: opus)
- ✓ Complete system prompt with role definition
- ✓ Four-layer knowledge loading protocol
- ✓ Prerequisite check for existing assessment
- ✓ Sensitivity detection protocol (INTERNAL_*.md, internal/, <!-- INTERNAL -->)
- ✓ Deliverable format with template reference
- ✓ Workflow (announce, load, check, work, capture, summarize)
- ✓ Batch processing instructions
- ✓ Knowledge auto-capture protocol

### Template Verification

Verified STRATEGY_PLAN_TEMPLATE.md contains:
- ✓ Strategy Plan header with entity name placeholder
- ✓ Generated date and planning horizon fields
- ✓ Strategic Vision section
- ✓ Goals section (Primary + Supporting)
- ✓ Strategy section with phased structure (Phase 1, Phase 2, Phase 3)
- ✓ Resource Requirements section
- ✓ Risks and Mitigations table
- ✓ Success Metrics section
- ✓ Footer with specialist attribution
- ✓ No metadata/frontmatter — pure content

### INSTRUCTIONS.md Verification

Verified INSTRUCTIONS.md update:
- ✓ "Specialists" section exists after Skills section
- ✓ Discovery pattern documented ("/specialists" + natural language)
- ✓ Invocation patterns documented (natural + explicit)
- ✓ Batch operations documented
- ✓ Both specialists listed (assessment + strategy)
- ✓ Knowledge layers documented (4-layer hierarchy)
- ✓ Deliverables location and naming documented
- ✓ Sensitivity controls documented
- ✓ Storage locations documented (.claude/agents/)
- ✓ Protocol reference documented (specialist-coordination.md)

## Deviations from Plan

None — plan executed exactly as written.

Initial concern about missing assessment-specialist.md (dependency from 05-01), but verification showed 05-01 had been executed previously. All required files were in place.

## Commits

| Commit | Type | Message | Files |
|--------|------|---------|-------|
| 657b8ba | feat(05-02) | create strategy specialist and template | strategy-specialist.md, STRATEGY_PLAN_TEMPLATE.md |
| 992ef81 | docs(05-02) | add Specialists section to INSTRUCTIONS.md | INSTRUCTIONS.md |

## Next Phase Readiness

### What's Now Possible

- Users can discover available specialists via "/specialists" or natural language
- Users can invoke strategy specialist for strategic planning work
- Strategy specialist checks for assessment first (best practice workflow)
- Strategy specialist produces structured STRATEGY_PLAN deliverables
- Specialist system is documented in main INSTRUCTIONS.md
- Both assessment and strategy specialists are fully functional

### What's Needed Next

**From Plan 05-03 (specialist-coordination.md protocol):**
- Detailed protocol documentation for specialist coordination
- Entity onboarding updates to create deliverables/ and knowledge/ folders
- Sensitivity detection implementation details
- Batch processing patterns for multi-entity operations

**Future Specialist Additions:**
- Additional specialists can follow established pattern (YAML frontmatter + markdown system prompt)
- Domain knowledge can be added to docs/domain/ for specialist consumption
- Agency knowledge can be added to ops/ for specialist consumption

### Known Limitations

**No /specialists command implementation:** INSTRUCTIONS.md documents "/specialists" command for discovery, but command handling logic not implemented. Natural language discovery ("What specialists do you have?") works via agent interpretation, but explicit slash command routing needs implementation.

**Template vs Adaptive:** Templates exist but specialists need explicit instruction to check for template first. Could be improved with template discovery logic in specialist protocol.

**Cross-Specialist Context:** Strategy specialist reads assessment deliverable file for handoff. No direct context sharing between specialists (by design — file-based handoff maintains clarity).

### Blockers/Concerns

None. Phase 05 Plan 02 is complete and functional.

---

*Phase: 05-specialist-pattern*
*Plan: 02*
*Completed: 2026-01-25*
*Duration: 9 minutes*
