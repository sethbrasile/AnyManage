# Phase 5: Specialist Pattern - Research

**Researched:** 2026-01-25
**Domain:** AI specialist/subagent patterns, knowledge layering, deliverable management, sensitivity controls
**Confidence:** HIGH

## Summary

Phase 5 research focused on implementing the specialist pattern for domain expert AI personas. Specialists are fundamentally different from skills: skills are reusable workflows (procedural), while specialists are expert personas (domain expertise + judgment). The ecosystem has mature patterns for both.

The Claude Code subagent system provides the foundation for specialists. Each subagent runs in its own context window with a custom system prompt, specific tool access, and can be routed to based on task descriptions. The key innovation for Agent PM is layered knowledge loading: specialists access base PM knowledge, domain knowledge, agency knowledge, and entity knowledge progressively.

Key findings from research:

1. **Subagent vs Skill distinction is critical**: Skills run in the main conversation context. Subagents/specialists run in isolated context with their own system prompt. For domain experts producing structured deliverables, subagent pattern is correct.

2. **YAML frontmatter format is standard**: Same format as skills but different fields (name, description, tools, model, permissionMode). Specialists stored in `.claude/agents/` (project) or `~/.claude/agents/` (user).

3. **Four-layer knowledge architecture**: Base PM (core skills) -> Domain (industry playbooks) -> Agency (company SOPs) -> Entity (client/project context). Progressive loading via file references, not hardcoding.

4. **Deliverable-first output**: Specialists produce structured files as their primary output. No logging required - the deliverable IS the record. Brief summaries with file links returned to main conversation.

5. **Sensitivity classification is file-based**: `INTERNAL_*.md` naming, `internal/` subfolders, and `<!-- INTERNAL -->` section markers. Detection before generation, not after.

**Primary recommendation:** Implement specialists as Claude Code subagents in `.claude/agents/` directory, using standard YAML frontmatter + markdown format. Load layered knowledge via explicit file references in system prompt. Save deliverables to `entities/[Entity]/deliverables/` with date-suffix versioning.

## Standard Stack

The established tools and patterns for specialist systems:

### Core Format

| Component | Version | Purpose | Why Standard |
|-----------|---------|---------|--------------|
| Claude Code Subagent | Current | Specialist definition and execution | Official Anthropic pattern, documented and supported |
| YAML frontmatter | 1.2 | Specialist metadata (name, description, tools, model) | Same format as skills, consistent with Agent Skills standard |
| Markdown body | GFM | Specialist system prompt and instructions | Human-readable, version-controllable |

### Storage Locations

| Location | Purpose | When to Use |
|----------|---------|-------------|
| `.claude/agents/` | Project specialists | Specialists specific to this Agent PM instance |
| `~/.claude/agents/` | Personal specialists | User-specific specialists, not committed to repo |
| `docs/domain/` | Domain knowledge | Industry-specific playbooks, frameworks |
| `ops/` | Agency knowledge | Company SOPs, preferences, style guides |
| `entities/[Entity]/knowledge/` | Entity knowledge | Client/project-specific learned context |
| `entities/[Entity]/deliverables/` | Deliverable output | Specialist work products |

### Model Selection

| Specialist Type | Recommended Model | Rationale |
|-----------------|-------------------|-----------|
| Assessment/Audit | `sonnet` | Cost-effective for structured analysis |
| Strategy/Planning | `opus` | Higher quality for complex reasoning |
| Research | `haiku` | Fast, low-latency for exploration |
| Default | `inherit` | Use main conversation's model |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Subagents | Skills with `context: fork` | Skills lack isolated context, better for procedures not personas |
| `.claude/agents/` | `.github/agents/` | `.github/agents/` not yet standardized like `.github/skills/` |
| Markdown deliverables | JSON/structured | Markdown human-readable, user can edit directly |
| Date-suffix versioning | Git versioning | Date-suffix keeps all versions visible to non-technical users |

**No installation required** - pure markdown files in designated folders.

## Architecture Patterns

### Recommended Project Structure

```
project-root/
.claude/
  agents/                      # Specialist definitions
    assessment-specialist.md   # Assessment/audit expert
    strategy-specialist.md     # Strategy/planning expert
docs/
  domain/                      # Domain knowledge layer
    [industry]_PLAYBOOK.md     # Industry-specific playbooks
    [domain]_FRAMEWORKS.md     # Domain-specific frameworks
ops/                           # Agency knowledge layer
  preferences/                 # Agency preferences
    COMMUNICATION_STYLE.md
  processes/                   # Agency SOPs
    QUALITY_CHECKLIST.md
  style/                       # Agency style guides
    DELIVERABLE_TEMPLATES.md
entities/
  [Entity]/
    knowledge/                 # Entity knowledge layer
      LEARNED_CONTEXT.md       # Auto-captured context
      CLIENT_NUANCES.md        # Entity-specific preferences
    deliverables/              # Specialist outputs
      AUDIT_FINDINGS_2026-01-25.md
      STRATEGY_PLAN_2026-01-25.md
    internal/                  # Internal-only content
      PRICING.md
      INTERNAL_NOTES.md
```

### Pattern 1: Specialist Definition Format

**What:** YAML frontmatter defines metadata; Markdown body provides system prompt with knowledge loading instructions.

**When to use:** For all domain expert personas that produce structured deliverables.

**Example:**

```markdown
---
name: assessment-specialist
description: Domain expert for conducting assessments and audits. Use when the user asks for an assessment, audit, gap analysis, or current state evaluation. Produces structured deliverable documents.
tools: Read, Grep, Glob, Write
model: sonnet
---

You are an assessment specialist with expertise in conducting audits and gap analyses.

## Your Role
You conduct thorough assessments and produce structured findings documents.

## Knowledge to Load

Before starting any assessment:

1. **Base PM Knowledge**: Read INSTRUCTIONS.md for core protocols
2. **Domain Knowledge**: Read relevant docs/domain/ playbooks for industry context
3. **Agency Knowledge**: Read ops/preferences/ and ops/processes/ for agency standards
4. **Entity Knowledge**: Read entities/[Entity]/knowledge/ for entity-specific context
5. **Sensitivity Rules**: Check for INTERNAL_* files and <!-- INTERNAL --> markers

## Deliverable Output

Save all deliverables to: `entities/[Entity]/deliverables/`
Naming: `[TYPE]_[DATE].md` (e.g., AUDIT_FINDINGS_2026-01-25.md)

## Workflow

1. Announce: "Starting assessment for [Entity]..."
2. Load knowledge (layers 1-5 above)
3. Conduct assessment using domain frameworks
4. Produce deliverable file
5. Return brief summary with link to deliverable
```

**Source:** [Claude Code Subagents Documentation](https://code.claude.com/docs/en/sub-agents)

### Pattern 2: Knowledge Layering

**What:** Four-layer knowledge hierarchy loaded progressively based on need.

**When to use:** All specialist invocations.

**How it works:**

```
Layer 1: Base PM (Always loaded)
  INSTRUCTIONS.md - Core agent protocols
  docs/protocols/ - Standard procedures

Layer 2: Domain Knowledge (Load by relevance)
  docs/domain/[industry]_PLAYBOOK.md
  docs/domain/[domain]_FRAMEWORKS.md

Layer 3: Agency Knowledge (Load by relevance)
  ops/preferences/ - Communication style, quality standards
  ops/processes/ - SOPs, checklists
  ops/style/ - Templates, formatting rules

Layer 4: Entity Knowledge (Always load for target entity)
  entities/[Entity]/ENTITY_PROFILE.md
  entities/[Entity]/knowledge/ - Learned context
```

**Progressive loading principle:**
- Layer 1: Load summary, reference for details
- Layers 2-3: Load based on task type matching
- Layer 4: Load fully for target entity

**Conflict resolution:** When layers conflict, specialist uses judgment - later layers provide context but don't override earlier layers automatically. Entity preferences can override agency defaults for that entity.

**Source:** Phase 5 CONTEXT.md decisions

### Pattern 3: Deliverable Management

**What:** Structured output to entity deliverables folder with date-suffix versioning.

**When to use:** All specialist work products.

**Deliverable naming:**
```
entities/[Entity]/deliverables/
  AUDIT_FINDINGS_2026-01-25.md
  STRATEGY_PLAN_2026-01-25.md
  ASSESSMENT_REPORT_2026-01-25.md
```

**Deliverable structure:**
```markdown
# [Deliverable Type]: [Entity Name]

**Generated:** [Date]
**Specialist:** [specialist-name]

## Executive Summary

[Brief overview of findings/recommendations]

## [Main Sections]

[Content organized by deliverable type]

---

*Generated by [specialist-name] specialist*
```

**No metadata in files** - file location/name provides context. Pure content for user consumption.

**Source:** Phase 5 CONTEXT.md - "No metadata in files - pure content"

### Pattern 4: Sensitivity Classification

**What:** File-level and section-level markers to identify internal-only content.

**When to use:** Any content that should not appear in client-facing output.

**File-level markers:**
```
entities/[Entity]/internal/          # Internal subfolder
entities/[Entity]/INTERNAL_*.md      # INTERNAL_ prefix
ops/internal/                        # Agency internal docs
```

**Section-level markers:**
```markdown
## Public Section

This content is client-facing.

<!-- INTERNAL -->
## Internal Notes

This content is internal only and should not appear in client deliverables.
Includes: pricing, margins, internal opinions, strategy notes.
<!-- /INTERNAL -->

## Another Public Section
```

**Detection before generation:**
1. Before creating client-facing deliverable, scan referenced files for INTERNAL markers
2. If internal content would be included, warn user first
3. Block generation until user confirms exclusion approach

**Source:** Phase 5 CONTEXT.md - "Block generation if internal content would appear in client-facing output - warn user first"

### Pattern 5: Specialist Invocation

**What:** Natural language or explicit naming, with brief announce before starting.

**When to use:** All specialist delegations.

**Natural language triggers:**
- "Run an assessment for Acme"
- "Develop strategy for Beta Corp"
- "Do a gap analysis on Gamma"

**Explicit naming:**
- "Use assessment-specialist for Acme"
- "Have the strategy-specialist work on Beta"

**Batch operations:**
- "Run assessment for all clients"
- "Run assessment for Acme and Beta Corp"

**Announce pattern:**
```
Starting assessment for Acme Corp...
[Specialist executes]
Completed assessment. Key findings: [brief summary]
View full report: entities/Acme Corp/deliverables/AUDIT_FINDINGS_2026-01-25.md
```

**Batch announce pattern:**
```
Running assessment for 3 entities...
Completed Acme Corp...
Completed Beta Industries...
Completed Gamma LLC...

All assessments complete. Summary:
- Acme Corp: 5 findings, 2 critical
- Beta Industries: 3 findings, 0 critical
- Gamma LLC: 4 findings, 1 critical
```

**Source:** Phase 5 CONTEXT.md - "Brief announce before starting", "Per-entity updates during batch"

### Pattern 6: Knowledge Auto-Capture

**What:** Specialists can auto-capture learned context during work.

**When to use:** When specialist discovers entity-specific preferences, patterns, or important context.

**Auto-capture workflow:**
1. During assessment/strategy work, specialist notes entity-specific insights
2. After completing deliverable, write to `entities/[Entity]/knowledge/LEARNED_CONTEXT.md`
3. Append with timestamp and source
4. Brief note at end: "Added [insight type] to entity knowledge"

**Knowledge capture format:**
```markdown
## Learned Context

### 2026-01-25 - Assessment Findings

- Prefers quarterly rather than monthly reviews
- Key decision maker is CFO, not CEO
- Budget cycles start in October
- Sensitive about competitor mentions

Source: Assessment for Acme Corp
```

**User maintains:** Manual periodic review to clean up, consolidate, remove stale info.

**Source:** Phase 5 CONTEXT.md - "Specialists can auto-capture learned context"

### Anti-Patterns to Avoid

**Anti-Pattern 1: Skills Instead of Specialists**
- **Bad:** Create skill for "assessment" that runs in main conversation
- **Good:** Create specialist subagent with isolated context and domain expertise
- **Why:** Specialists need focused context, domain knowledge loading, and structured deliverables

**Anti-Pattern 2: Hardcoding Knowledge in System Prompt**
- **Bad:** Embed all domain knowledge directly in specialist markdown file
- **Good:** Reference knowledge locations, load dynamically
- **Why:** Knowledge changes over time, single source of truth principle

**Anti-Pattern 3: Logging Instead of Deliverables**
- **Bad:** Write detailed execution logs, reference them later
- **Good:** Produce clean deliverable as primary output
- **Why:** Deliverable IS the record. Users want the work product, not the process.

**Anti-Pattern 4: Metadata in Deliverables**
- **Bad:** Include frontmatter, JSON metadata, or processing info in deliverable files
- **Good:** Pure content only, file name/location provides context
- **Why:** Non-technical users edit deliverables directly. No learning curve.

**Anti-Pattern 5: Pre-Confirmation for Non-Destructive Operations**
- **Bad:** "Run assessment for Acme? (yes/no)" before starting
- **Good:** Start immediately with brief announce
- **Why:** Matches Phase 2 pattern of silent updates with post-confirmation

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Specialist execution environment | Custom spawning logic | Claude Code subagent system | Built-in context isolation, tool restriction, model selection |
| Knowledge discovery | Custom indexing system | File system conventions + glob patterns | Predictable locations, no overhead, human-readable |
| Deliverable versioning | Git-based versioning UI | Date-suffix naming | Non-technical users see all versions at a glance |
| Sensitivity detection | Runtime content scanning | File/section markers + pre-generation check | Explicit opt-in, no false positives |
| Batch coordination | Custom orchestration layer | Sequential subagent invocation with announce | Simple, debuggable, clear progress |
| Knowledge conflict resolution | Rules engine | Specialist judgment with layer context | LLMs excel at nuanced judgment, explicit rules too rigid |

**Key insight:** The specialist pattern works because Claude Code already handles the hard parts (context isolation, tool access, model routing). Agent PM's job is file structure conventions for knowledge loading and deliverable output.

## Common Pitfalls

### Pitfall 1: Knowledge Layer Explosion

**What goes wrong:** Specialist tries to load all knowledge layers fully, exceeds context window, loses focus.

**Why it happens:** Misunderstanding progressive loading - "load relevant" doesn't mean "load everything".

**How to avoid:**
- Layer 1 (Base PM): Load INSTRUCTIONS.md summary only
- Layer 2-3 (Domain/Agency): Load based on task type (assessment -> ops/processes/QUALITY_CHECKLIST.md)
- Layer 4 (Entity): Load ENTITY_PROFILE.md and knowledge/ fully for target entity

**Warning signs:**
- Specialist output is generic, doesn't reflect entity specifics
- Specialist forgets instructions mid-task
- Very long startup time before work begins

### Pitfall 2: Internal Content Leakage

**What goes wrong:** Specialist includes internal content (pricing, margins, opinions) in client-facing deliverable.

**Why it happens:** No pre-generation check for sensitivity markers.

**How to avoid:**
- Before generating client-facing deliverable, scan all source files for INTERNAL markers
- If found, stop and warn user: "This deliverable would include internal content from [files]. Proceed without internal content?"
- Implement as check in specialist system prompt, not post-hoc

**Warning signs:**
- User discovers internal info in shared deliverable
- No warning when referencing internal-marked files
- Deliverables include pricing, margins, or internal strategy notes

### Pitfall 3: Deliverable Overwriting

**What goes wrong:** Second assessment for same entity overwrites first one.

**Why it happens:** Using static naming without date suffix.

**How to avoid:**
- Always include date in deliverable name: `AUDIT_FINDINGS_2026-01-25.md`
- If running multiple same-day assessments (rare), add sequence: `AUDIT_FINDINGS_2026-01-25_v2.md`
- Never use `AUDIT_FINDINGS.md` without date

**Warning signs:**
- User can't find previous deliverable
- Unexpected content in deliverable file
- Loss of historical assessments

### Pitfall 4: Specialist as Skill Anti-Pattern

**What goes wrong:** Creating specialists as skills (in `.github/skills/`) instead of subagents.

**Why it happens:** Confusion between skills and specialists - both are markdown files with frontmatter.

**How to avoid:**
- Skills (`.github/skills/`): Reusable workflows, run in main context
- Specialists (`.claude/agents/`): Domain experts, isolated context
- If it has a persona/expertise and produces deliverables -> specialist
- If it's a procedure anyone could follow -> skill

**Warning signs:**
- "Specialist" running without context isolation
- Specialist output polluting main conversation
- Specialist unable to maintain focus across multi-step work

### Pitfall 5: Batch Without Progress

**What goes wrong:** User runs batch assessment for 10 entities, sees nothing for 5 minutes, thinks it's broken.

**Why it happens:** No per-entity progress updates during batch.

**How to avoid:**
- Announce before starting batch: "Running assessment for 10 entities..."
- Update after each entity: "Completed Acme Corp... (1/10)"
- Final summary with all results and links

**Warning signs:**
- Users interrupting long batch operations
- "Is it still working?" questions
- Users avoiding batch operations, running one-at-a-time

### Pitfall 6: Knowledge Capture Without Boundaries

**What goes wrong:** Specialist captures too much "learned context", knowledge file becomes bloated and stale.

**Why it happens:** No guidance on what's worth capturing vs. transient.

**How to avoid:**
- Capture: Preferences, patterns, recurring themes, important relationships
- Don't capture: One-time details, obvious facts, info already in profile
- Always note source and date
- Periodic user review to clean up

**Warning signs:**
- Entity knowledge file has hundreds of entries
- Conflicting or contradictory captured context
- Specialist behavior seems confused by old context

## Code Examples

Verified patterns from official sources:

### Assessment Specialist Definition

```markdown
<!-- .claude/agents/assessment-specialist.md -->
---
name: assessment-specialist
description: Domain expert for conducting assessments, audits, and gap analyses. Use when user asks for assessment, audit, gap analysis, current state evaluation, or needs to understand where an entity stands. Produces structured AUDIT_FINDINGS documents.
tools: Read, Grep, Glob, Write
model: sonnet
---

You are an assessment specialist. Your expertise is conducting thorough audits and producing actionable findings.

## Core Responsibility

Produce structured assessment deliverables that help users understand current state, gaps, and opportunities.

## Knowledge Loading Protocol

Before any assessment:

1. **Base Knowledge**
   - Read: INSTRUCTIONS.md (for core protocols)
   - Skim: docs/protocols/ (for relevant procedures)

2. **Domain Knowledge**
   - Read: docs/domain/ files matching industry/domain
   - Apply: Relevant frameworks and checklists

3. **Agency Knowledge**
   - Read: ops/preferences/ (communication style)
   - Read: ops/processes/ (quality standards)
   - Apply: Agency standards to assessment

4. **Entity Knowledge**
   - Read: entities/[Entity]/ENTITY_PROFILE.md (full)
   - Read: entities/[Entity]/knowledge/ (all files)
   - Apply: Entity-specific context to assessment

## Sensitivity Protocol

Before writing deliverable:
1. Check if deliverable is client-facing
2. Scan referenced files for:
   - `INTERNAL_*.md` files
   - `internal/` subfolders
   - `<!-- INTERNAL -->` section markers
3. If internal content found and deliverable is client-facing:
   - STOP
   - Warn user: "This deliverable would reference internal content from [files]. Proceed without internal content?"
4. Exclude internal content from client-facing output

## Deliverable Format

Save to: `entities/[Entity]/deliverables/AUDIT_FINDINGS_[DATE].md`

Structure:
```markdown
# Audit Findings: [Entity Name]

**Assessment Date:** [Date]
**Assessment Type:** [Current State / Gap Analysis / Full Audit]

## Executive Summary

[3-5 sentence overview of key findings]

## Strengths

[What's working well]

## Gaps and Issues

[What needs attention, prioritized by severity]

### Critical
- [Issue]: [Impact and recommendation]

### Important
- [Issue]: [Impact and recommendation]

### Minor
- [Issue]: [Impact and recommendation]

## Opportunities

[Strategic opportunities identified]

## Recommended Next Steps

1. [Priority action]
2. [Secondary action]
3. [Tertiary action]
```

## Completion Protocol

1. Save deliverable to entities/[Entity]/deliverables/
2. Capture learned context if applicable
3. Return brief summary: "Completed assessment for [Entity]. Key findings: [2-3 bullets]. View full report: [file path]"

## Batch Processing

When processing multiple entities:
- Announce: "Running assessment for [N] entities..."
- Update per entity: "Completed [Entity]... ([N]/[Total])"
- Final summary: All entities, key findings, links
```

**Source:** Synthesized from [Claude Code Subagents Documentation](https://code.claude.com/docs/en/sub-agents) + Phase 5 CONTEXT.md decisions

### Strategy Specialist Definition

```markdown
<!-- .claude/agents/strategy-specialist.md -->
---
name: strategy-specialist
description: Domain expert for developing strategies and plans. Use when user asks for strategy development, planning, roadmap creation, or needs forward-looking recommendations. Produces structured STRATEGY documents.
tools: Read, Grep, Glob, Write
model: opus
---

You are a strategy specialist. Your expertise is synthesizing findings into actionable strategic plans.

## Core Responsibility

Develop strategic plans that turn assessment findings into actionable roadmaps.

## Knowledge Loading Protocol

[Same four-layer pattern as assessment-specialist]

## Prerequisites

Before developing strategy:
1. Check for existing assessment: entities/[Entity]/deliverables/AUDIT_FINDINGS_*.md
2. If no assessment exists, recommend running assessment first
3. If assessment exists, use as input for strategy

## Deliverable Format

Save to: `entities/[Entity]/deliverables/STRATEGY_PLAN_[DATE].md`

Structure:
```markdown
# Strategy Plan: [Entity Name]

**Strategy Date:** [Date]
**Planning Horizon:** [3 months / 6 months / 12 months]
**Based on:** [Assessment date if applicable]

## Strategic Vision

[Where we're taking this entity]

## Goals

### Primary Goal
[Most important outcome]

### Supporting Goals
- [Goal 2]
- [Goal 3]

## Strategy

### Phase 1: [Name] (Months 1-3)
**Objective:** [What this phase achieves]
**Key Activities:**
- [Activity]
- [Activity]
**Success Metrics:**
- [Metric]

### Phase 2: [Name] (Months 4-6)
[Same structure]

## Resource Requirements

[What's needed to execute]

## Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| [Risk] | High/Med/Low | High/Med/Low | [Approach] |

## Success Metrics

[How we'll know the strategy is working]
```

[Rest of specialist definition follows same pattern]
```

### Knowledge Folder Structure

```
entities/Acme Corp/
  knowledge/
    LEARNED_CONTEXT.md      # Auto-captured by specialists
    COMMUNICATION_PREFS.md  # Optional: explicit preferences
    HISTORY.md              # Optional: relationship history
```

**LEARNED_CONTEXT.md format:**

```markdown
# Learned Context: Acme Corp

Automatically captured context from specialist work.

## Communication Preferences

### 2026-01-25 - Assessment
- Prefers executive summaries first, details in appendix
- Key stakeholder (Sarah Chen, CFO) wants financial impact highlighted
- Avoid technical jargon - audience is business-focused

Source: assessment-specialist

## Decision Patterns

### 2026-01-20 - Strategy Session
- Budget decisions require CFO + CEO sign-off
- Quarterly planning cycle: October start
- Prefers conservative estimates with upside potential

Source: strategy-specialist

---

*Last updated: 2026-01-25*
*Review recommended: 2026-04-25*
```

### Sensitivity Detection Example

```markdown
<!-- In specialist system prompt -->

## Sensitivity Detection Protocol

Before generating any deliverable, execute this check:

1. Identify all files to be referenced in deliverable
2. For each file, check:
   - Is filename prefixed with `INTERNAL_`?
   - Is file in an `internal/` subfolder?
   - Does file contain `<!-- INTERNAL -->` markers?
3. If ANY internal content detected and deliverable is client-facing:
   - Stop generation
   - Report: "Cannot generate client-facing deliverable. The following contain internal content: [list files/sections]"
   - Ask: "Options: (1) Generate without internal content, (2) Generate internal-only version, (3) Cancel"
4. Proceed based on user choice
```

## State of the Art

| Old Approach (2024-2025) | Current Approach (2026) | When Changed | Impact |
|--------------------------|-------------------------|--------------|--------|
| Hardcoded specialist prompts | Dynamic knowledge loading | 2025 | Specialists adapt to changing context |
| Flat knowledge structure | Layered hierarchy (PM->Domain->Agency->Entity) | 2025-2026 | Scalable knowledge management |
| Custom spawning logic | Claude Code subagent system | Oct 2025 | Built-in context isolation, tool access, model routing |
| Log-based accountability | Deliverable-as-record | 2025-2026 | Cleaner output, less noise |
| Manual sensitivity tracking | Marker-based classification | 2025-2026 | Reliable internal content protection |
| Single-entity operations | Batch with progress | 2026 | Efficiency for multi-entity workflows |

**Deprecated/outdated:**
- **Subagent logging to separate files:** Deliverable is the record, no separate logs needed
- **Skills for domain experts:** Skills are procedures; specialists (subagents) are personas with expertise
- **Pre-generation confirmation:** Pattern is announce + execute + report, not confirm + execute

**Current best practices (2026):**
- [Multi-agent hierarchical task decomposition](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) for complex workflows
- [Progressive disclosure](https://alexop.dev/posts/stop-bloating-your-claude-md-progressive-disclosure-ai-coding-tools/) for knowledge loading
- [Entity-level sensitivity](https://learn.microsoft.com/en-us/purview/ai-microsoft-purview) not just document-level

## Open Questions

Things that couldn't be fully resolved:

1. **Cross-Specialist Context Sharing**
   - What we know: Each specialist runs in isolated context
   - What's unclear: When assessment-specialist completes, how does strategy-specialist access its findings?
   - Recommendation: Strategy specialist reads deliverable file from entities/[Entity]/deliverables/. No direct context sharing needed. File-based handoff works.
   - Confidence: HIGH - this is how Claude Code is designed

2. **Multi-Entity Batch Deliverable Organization**
   - What we know: Each entity gets deliverables in its own folder
   - What's unclear: For cross-entity summary (e.g., "portfolio assessment"), where does the aggregate deliverable go?
   - Recommendation: Aggregate deliverables go to top-level `deliverables/` folder: `deliverables/PORTFOLIO_ASSESSMENT_2026-01-25.md`. Entity-specific findings still go to entity folders.
   - Confidence: MEDIUM - logical extension but not explicitly decided

3. **Domain Knowledge Organization**
   - What we know: Domain knowledge lives in `docs/domain/`
   - What's unclear: How to organize multiple domains (marketing, legal, healthcare)?
   - Recommendation: Subfolders by domain: `docs/domain/marketing/`, `docs/domain/legal/`. CONFIG.md specifies active domain. Specialists load matching domain folder.
   - Confidence: MEDIUM - sensible structure but needs validation

4. **Template vs Adaptive Deliverable Structure**
   - What we know: "Template-based when template exists, otherwise adaptive structure"
   - What's unclear: Where do templates live? How does specialist find them?
   - Recommendation: Templates in `templates/deliverables/AUDIT_FINDINGS_TEMPLATE.md`. Specialist checks for template matching deliverable type before generating. If found, follow template; if not, use adaptive structure.
   - Confidence: HIGH - follows existing template pattern from Phase 1

5. **Specialist Discovery**
   - What we know: Users can invoke specialists naturally or explicitly
   - What's unclear: How do users know what specialists exist?
   - Recommendation: `/specialists` command or "What specialists do you have?" shows available specialists with descriptions. Same pattern as `/skills` from Phase 4.
   - Confidence: HIGH - consistent with skill discovery pattern

## Sources

### Primary (HIGH confidence)
- [Claude Code Subagents Documentation](https://code.claude.com/docs/en/sub-agents) - Official Anthropic documentation for subagent system
- Phase 5 CONTEXT.md - User decisions and constraints for this phase
- Phase 4 04-RESEARCH.md - Agent Skills standard and skill patterns (specialists build on this)

### Secondary (MEDIUM confidence)
- [VoltAgent awesome-claude-code-subagents](https://github.com/VoltAgent/awesome-claude-code-subagents) - Community patterns for organizing specialists
- [AI Agent Design Patterns - Microsoft Azure](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns) - Enterprise patterns for agent orchestration
- [Progressive Disclosure for AI Coding Tools](https://alexop.dev/posts/stop-bloating-your-claude-md-progressive-disclosure-ai-coding-tools/) - Knowledge layering best practices
- [Microsoft Purview AI Sensitivity](https://learn.microsoft.com/en-us/purview/ai-microsoft-purview) - Enterprise sensitivity classification patterns

### Tertiary (LOW confidence - flagged for validation)
- Various Medium articles on agentic AI - useful for context but not authoritative
- WebSearch results on knowledge hierarchies - pattern verification needed

## Metadata

**Confidence breakdown:**
- Specialist format/structure: HIGH - Official Claude Code documentation confirms subagent format
- Knowledge layering: HIGH - CONTEXT.md decisions + enterprise best practices align
- Deliverable management: HIGH - Clear decisions in CONTEXT.md
- Sensitivity classification: MEDIUM - Pattern is clear but detection logic needs implementation validation
- Batch operations: MEDIUM - Based on skill patterns, needs specialist-specific validation

**Research date:** 2026-01-25
**Valid until:** 2026-02-25 (30 days - subagent patterns stable but evolving)

**Key dependencies:**
- Claude Code subagent system (current)
- Agent Skills 1.0 (Phase 4 foundation)
- YAML frontmatter parsing (built into Claude Code)

**Notes for planner:**
- Phase 5 builds on Phase 4 skill system - specialists use skills but are different
- Deliverables folder is new - needs creation as part of phase
- Knowledge folders (domain, agency, entity) need structure defined
- Sensitivity system is file-based, not runtime - can be simple
- Two example specialists (assessment, strategy) should be created as reference
