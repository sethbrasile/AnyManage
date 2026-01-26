# Specialist Coordination Protocol

Complete documentation for working with domain expert AI specialists.

## Overview

Specialists are domain expert AI personas that produce structured deliverables. Unlike skills (which are reusable workflows), specialists are AI subagents that run in isolated contexts with domain expertise, custom system prompts, and focused tool access.

**Key Principles:**
- Specialists run in isolated context windows (not main conversation)
- Knowledge loaded progressively across four layers
- Deliverables are the primary output (not logs)
- Sensitivity classification prevents internal content leakage
- Natural language invocation with brief announces

## Quick Reference

| Aspect | Pattern |
|--------|---------|
| **Storage** | `.claude/agents/` (project), `~/.claude/agents/` (personal) |
| **Format** | YAML frontmatter + Markdown system prompt |
| **Invocation** | Natural language OR explicit naming |
| **Output Location** | `entities/[Entity]/deliverables/` |
| **Output Naming** | `[TYPE]_[DATE].md` (e.g., `AUDIT_FINDINGS_2026-01-25.md`) |
| **Knowledge Layers** | Base PM → Domain → Agency → Entity |
| **Internal Content** | `INTERNAL_*.md`, `internal/`, `<!-- INTERNAL -->` |
| **Model Selection** | `sonnet` (default), `opus` (complex), `haiku` (fast), `inherit` |

## Trigger Detection

The agent should recognize these patterns as specialist invocation requests:

### Natural Language Patterns

| Pattern | Example | Specialist Invoked |
|---------|---------|-------------------|
| "Run assessment for [Entity]" | "Run assessment for Acme Corp" | assessment-specialist |
| "Do a gap analysis on [Entity]" | "Do a gap analysis on Beta" | assessment-specialist |
| "Audit [Entity]" | "Audit Gamma LLC" | assessment-specialist |
| "Develop strategy for [Entity]" | "Develop strategy for Acme" | strategy-specialist |
| "Create strategic plan for [Entity]" | "Create strategic plan for Beta" | strategy-specialist |
| "Plan roadmap for [Entity]" | "Plan roadmap for Gamma" | strategy-specialist |

### Explicit Naming

Users can explicitly name the specialist:

```
Use assessment-specialist for Acme Corp
Have the strategy-specialist work on Beta Industries
Run the assessment-specialist on Gamma LLC
```

### Batch Operations

Users can process multiple entities:

```
Run assessment for all clients
Run assessment for Acme and Beta Corp
Do strategy for all entities
```

The agent detects batch patterns by:
- "all clients" / "all entities" → Process all folders in `entities/`
- "Acme and Beta Corp" → Process listed entities
- "Acme, Beta, and Gamma" → Process comma-separated entities

## Specialist Discovery

Users can discover available specialists:

### Discovery Commands

| Command | Agent Response |
|---------|---------------|
| "What specialists do you have?" | List available specialists with descriptions |
| "Show specialists" | List available specialists with descriptions |
| "/specialists" | List available specialists with descriptions |

### Discovery Response Format

```
Available specialists:

**assessment-specialist**
Domain expert for conducting assessments, audits, and gap analyses.
Use for: current state evaluation, finding gaps, audit work

**strategy-specialist**
Domain expert for developing strategies and plans.
Use for: forward planning, strategic roadmaps, recommendations
```

### Discovery Implementation

1. Scan `.claude/agents/` for `*.md` files
2. Read YAML frontmatter for `name` and `description`
3. Present in human-friendly format with use cases

## Knowledge Loading Protocol

Specialists access knowledge progressively across four layers.

### Layer 1: Base PM Knowledge (Always Load)

**What:** Core AnyManage protocols and workflows

**Location:**
- `INSTRUCTIONS.md` (main agent instructions)
- `docs/protocols/` (detailed protocol documentation)

**Loading Pattern:**
- Read `INSTRUCTIONS.md` summary for core protocols
- Reference specific protocols from `docs/protocols/` as needed
- Do NOT load all protocols — only relevant ones

**Example:**
```
Assessment specialist loads:
- INSTRUCTIONS.md (summary)
- docs/protocols/entity-onboarding.md (if checking entity structure)
- docs/protocols/profile-building.md (if referencing profile facts)
```

### Layer 2: Domain Knowledge (Load by Relevance)

**What:** Industry-specific playbooks, frameworks, checklists

**Location:**
- `docs/domain/[industry]_PLAYBOOK.md`
- `docs/domain/[domain]_FRAMEWORKS.md`

**Loading Pattern:**
- Load only domain files matching the task type
- If no domain files exist, skip this layer
- Domain knowledge provides industry best practices

**Example:**
```
Healthcare assessment loads:
- docs/domain/healthcare_PLAYBOOK.md
- docs/domain/compliance_FRAMEWORKS.md

Marketing strategy skips healthcare domain, looks for:
- docs/domain/marketing_PLAYBOOK.md
```

### Layer 3: Agency Knowledge (Load by Relevance)

**What:** Company SOPs, preferences, style guides

**Location:**
- `ops/preferences/` (communication style, quality standards)
- `ops/processes/` (SOPs, checklists)
- `ops/style/` (templates, formatting rules)

**Loading Pattern:**
- Load preference files first (communication style, quality standards)
- Load process files matching task type (QUALITY_CHECKLIST.md for assessments)
- Load style files if generating formatted deliverable

**Example:**
```
Assessment specialist loads:
- ops/preferences/COMMUNICATION_STYLE.md
- ops/processes/QUALITY_CHECKLIST.md

Strategy specialist loads:
- ops/preferences/COMMUNICATION_STYLE.md
- ops/style/DELIVERABLE_TEMPLATES.md
```

### Layer 4: Entity Knowledge (Always Load for Target Entity)

**What:** Entity-specific context, learned preferences, history

**Location:**
- `entities/[Entity]/ENTITY_PROFILE.md` (full profile)
- `entities/[Entity]/knowledge/` (all files in folder)

**Loading Pattern:**
- Load ENTITY_PROFILE.md completely
- Load all files from `entities/[Entity]/knowledge/`
- This is the most specific context — load fully

**Example:**
```
Assessment for Acme Corp loads:
- entities/Acme Corp/ENTITY_PROFILE.md
- entities/Acme Corp/knowledge/LEARNED_CONTEXT.md
- entities/Acme Corp/knowledge/COMMUNICATION_PREFS.md
```

### Progressive Loading Principle

**Summary First, Details On-Demand:**

1. Load file summaries/headers to understand what's available
2. Load full content only when directly relevant to current task
3. Avoid loading everything "just in case"

**Conflict Resolution:**

When layers provide conflicting information:
- Specialist uses judgment to weigh context
- Later layers (Entity) provide more specific context but don't automatically override
- Entity preferences can override agency defaults for that specific entity

**Example Conflict:**
```
Agency standard: Monthly reports
Entity preference: Quarterly reports (noted in knowledge)
→ Specialist uses quarterly for this entity
```

## Sensitivity Classification

Protect internal-only content from appearing in client-facing deliverables.

### File-Level Markers

**INTERNAL_ Prefix:**

Files prefixed with `INTERNAL_` are internal-only:

```
entities/Acme Corp/INTERNAL_PRICING.md
entities/Acme Corp/INTERNAL_NOTES.md
ops/INTERNAL_MARGINS.md
```

**internal/ Subfolder:**

Files in `internal/` subfolders are internal-only:

```
entities/Acme Corp/internal/PRICING.md
entities/Acme Corp/internal/STRATEGY_NOTES.md
ops/internal/MARGINS.md
```

### Section-Level Markers

**<!-- INTERNAL --> Blocks:**

Mixed-content files can have internal sections:

```markdown
## Public Section

This content is client-facing and can appear in deliverables.

<!-- INTERNAL -->
## Internal Notes

This content is internal-only:
- Pricing: $50k/month
- Margin: 40%
- Internal opinion: Great client, push for upsell

<!-- /INTERNAL -->

## Another Public Section

This content is also client-facing.
```

### Detection Protocol (Before Generation)

Before generating any client-facing deliverable:

**Step 1: Identify deliverable type**
- Is this deliverable going to the client? (assessment, strategy, report)
- Or is this internal-only? (internal analysis, pricing doc)

**Step 2: Scan source files**

For each file to be referenced:
1. Check if filename starts with `INTERNAL_`
2. Check if file is in `internal/` subfolder
3. Check if file contains `<!-- INTERNAL -->` markers

**Step 3: Detect conflicts**

If internal content would be included in client-facing deliverable:
- STOP before generating
- Warn user with specific details
- Wait for user decision

**Step 4: User options**

Present clear options:
```
Cannot generate client-facing deliverable. The following contain internal content:
- entities/Acme Corp/INTERNAL_PRICING.md
- ops/internal/MARGINS.md (section: "Pricing Strategy")

Options:
1. Generate without internal content (exclude these sources)
2. Generate internal-only version (saved to internal/ folder)
3. Cancel generation
```

### Warning Message Format

```
⚠️  Internal content detected

This deliverable would include internal-only content:
- [File 1]: [Reason - full file or specific section]
- [File 2]: [Reason]

Deliverable type: [Client-facing / Internal]

How should I proceed?
1. Generate without internal content
2. Generate as internal-only document
3. Cancel
```

## Deliverable Management

All specialist outputs follow consistent structure and naming.

### Output Location

```
entities/[Entity]/deliverables/
```

All specialist work products save to the entity's `deliverables/` subfolder.

### Naming Convention

Format: `[TYPE]_[DATE].md`

Examples:
```
AUDIT_FINDINGS_2026-01-25.md
STRATEGY_PLAN_2026-01-25.md
ASSESSMENT_REPORT_2026-01-25.md
GAP_ANALYSIS_2026-01-25.md
```

**Same-day duplicates (rare):**
```
AUDIT_FINDINGS_2026-01-25.md
AUDIT_FINDINGS_2026-01-25_v2.md
```

### Deliverable Structure

All deliverables follow this pattern:

```markdown
# [Deliverable Type]: [Entity Name]

**Generated:** [Date]
**Specialist:** [specialist-name]

## Executive Summary

[Brief overview - 3-5 sentences of key points]

## [Main Sections]

[Content organized by deliverable type]

[Details, findings, recommendations]

---

*Generated by [specialist-name] specialist*
```

**Key principles:**
- No YAML frontmatter (not needed - file location/name provide context)
- No JSON metadata (pure content for user editing)
- Human-readable throughout
- Executive summary always first

### Template-Based vs Adaptive Structure

**If template exists:**
- Check `templates/deliverables/[TYPE]_TEMPLATE.md`
- If found, follow template structure exactly
- Templates in `templates/deliverables/` folder

**If no template:**
- Use adaptive structure based on deliverable type
- Structure should be logical and industry-standard

**User can request templatization:**
```
User: "Can you templatize this deliverable format?"
Agent: Creates template in templates/deliverables/ for future use
```

### Versioning

**Date-suffix versioning keeps all versions visible:**

```
entities/Acme Corp/deliverables/
  AUDIT_FINDINGS_2026-01-15.md   <- First assessment
  AUDIT_FINDINGS_2026-01-25.md   <- Follow-up assessment
  STRATEGY_PLAN_2026-01-20.md    <- Initial strategy
```

Non-technical users can:
- See all historical deliverables at a glance
- Open any version directly
- Compare versions manually
- No git knowledge required

## Knowledge Auto-Capture

Specialists can automatically capture learned context during their work.

### When to Capture

Capture when specialist discovers:
- Entity-specific preferences (communication style, format preferences)
- Recurring patterns (decision cycles, approval workflows)
- Important relationships (key stakeholders, dependencies)
- Strategic context (budget cycles, sensitive topics)

### What NOT to Capture

Do not capture:
- One-time transient details
- Information already in ENTITY_PROFILE.md
- Obvious facts that don't add value
- Speculation without basis

### Capture Format

Save to: `entities/[Entity]/knowledge/LEARNED_CONTEXT.md`

Append to existing file with timestamp and source:

```markdown
## Learned Context

### 2026-01-25 - Assessment Findings

- Prefers quarterly reviews rather than monthly
- Key decision maker is CFO (Sarah Chen), not CEO
- Budget cycles start in October
- Sensitive about competitor mentions (especially CompanyX)
- Technical team prefers detailed documentation

Source: assessment-specialist
Specialist: assessment-specialist
```

### Capture Workflow

1. During specialist work, note entity-specific insights
2. After completing deliverable, append to `LEARNED_CONTEXT.md`
3. Include timestamp, source specialist, and clear context
4. Brief note at end of specialist output: "Added [insight type] to entity knowledge"

### User Maintenance

Users periodically review and clean up knowledge:
- Consolidate redundant entries
- Remove stale or outdated context
- Move important facts to ENTITY_PROFILE.md if they become permanent
- Archive old context if no longer relevant

## Batch Processing

Specialists can process multiple entities in a single invocation.

### Batch Patterns

| User Input | Agent Interpretation |
|------------|---------------------|
| "Run assessment for all clients" | Process all entities in `entities/` |
| "Run assessment for Acme and Beta" | Process Acme Corp and Beta Industries |
| "Do strategy for Acme, Beta, and Gamma" | Process three specific entities |

### Batch Announce Pattern

**Step 1: Initial announce**
```
Running assessment for 3 entities...
```

**Step 2: Per-entity updates**
```
Completed Acme Corp... (1/3)
Completed Beta Industries... (2/3)
Completed Gamma LLC... (3/3)
```

**Step 3: Final summary**
```
All assessments complete.

Summary:
- Acme Corp: 5 findings, 2 critical
  → entities/Acme Corp/deliverables/AUDIT_FINDINGS_2026-01-25.md

- Beta Industries: 3 findings, 0 critical
  → entities/Beta Industries/deliverables/AUDIT_FINDINGS_2026-01-25.md

- Gamma LLC: 4 findings, 1 critical
  → entities/Gamma LLC/deliverables/AUDIT_FINDINGS_2026-01-25.md
```

### Progress Visibility

Batch operations provide clear progress:
- User knows how many entities to process
- User sees progress after each completes
- User sees summary with all results and links
- User doesn't wonder "is it still working?"

## Coordination Between Specialists

Specialists can hand off work through file-based coordination.

### File-Based Handoff Pattern

**Scenario:** Strategy specialist needs assessment findings

**Process:**

1. User invokes strategy specialist: "Develop strategy for Acme Corp"
2. Strategy specialist checks: `entities/Acme Corp/deliverables/AUDIT_FINDINGS_*.md`
3. If assessment exists: Read it as input, proceed with strategy
4. If no assessment: Recommend running assessment first

**Example:**
```
User: "Develop strategy for Acme Corp"

Strategy specialist checks:
- entities/Acme Corp/deliverables/ folder
- Finds: AUDIT_FINDINGS_2026-01-25.md
- Reads findings as input
- Develops strategy based on assessment

Output: STRATEGY_PLAN_2026-01-25.md
```

### Prerequisite Pattern

Some specialists have prerequisites:

| Specialist | Prerequisite | Behavior if Missing |
|------------|--------------|---------------------|
| strategy-specialist | Recent assessment | Recommend: "Run assessment first for better strategy" |
| follow-up-assessment | Prior assessment | Required: "No baseline assessment found. Run initial assessment first." |

### No Direct Context Sharing

Specialists run in isolated contexts — they cannot share memory directly. All coordination happens through files:

- Assessment specialist writes deliverable to `deliverables/`
- Strategy specialist reads that deliverable
- File-based handoff is explicit and debuggable

## Error Handling

Handle common failure scenarios gracefully.

### Missing Entity

**Scenario:** User invokes specialist for non-existent entity

**Agent response:**
```
Entity 'Acme Corp' doesn't exist. Create it first?
```

**Options:**
- "Yes, create it" → Create entity, then run specialist
- "Show entities" → List existing entities
- "Nevermind" → Cancel operation

### Internal Content Detected

**Scenario:** Specialist would include internal content in client-facing deliverable

**Agent response:**
```
⚠️  Internal content detected

This deliverable would include internal-only content:
- entities/Acme Corp/INTERNAL_PRICING.md

Generate without internal content?
```

**Options:**
- "Yes, exclude it" → Generate without internal sources
- "Make it internal" → Save to `internal/` subfolder
- "Cancel" → Stop generation

### Assessment Prerequisite

**Scenario:** Strategy specialist invoked without assessment

**Agent response:**
```
No recent assessment found for Acme Corp.

Run assessment first for a stronger strategy? (Recommended)
```

**Options:**
- "Yes, run assessment" → Run assessment first, then strategy
- "Skip assessment" → Proceed without assessment baseline
- "Show deliverables" → Check what exists already

### Template Not Found

**Scenario:** Specialist expects template but it's missing

**Agent behavior:**
- Use adaptive structure (don't stop)
- Log missing template to `ops/logs/`
- Continue with intelligent defaults
- DO NOT expose error to user

### Batch Partial Failure

**Scenario:** Batch operation fails for one entity

**Agent response:**
```
Completed 2 of 3 entities:
✓ Acme Corp
✓ Beta Industries
✗ Gamma LLC - Error: Missing entity profile

Process remaining entities or retry Gamma LLC?
```

**Options:**
- "Retry Gamma" → Fix issue and retry
- "Skip Gamma" → Continue with successful completions
- "Stop" → Cancel remaining batch

## Examples

Full invocation examples showing realistic workflows.

### Example 1: Basic Assessment

**User:**
```
Run assessment for Acme Corp
```

**Agent:**
```
Starting assessment for Acme Corp...

[Specialist loads:
- INSTRUCTIONS.md (summary)
- entities/Acme Corp/ENTITY_PROFILE.md
- entities/Acme Corp/knowledge/LEARNED_CONTEXT.md
- ops/preferences/COMMUNICATION_STYLE.md
- ops/processes/QUALITY_CHECKLIST.md]

[Specialist conducts assessment]
[Specialist writes deliverable]
[Specialist captures learned context]

Completed assessment for Acme Corp.

Key findings:
- 5 total findings identified
- 2 critical issues requiring immediate attention
- 3 opportunities for improvement

Added communication preferences to entity knowledge.

View full report: entities/Acme Corp/deliverables/AUDIT_FINDINGS_2026-01-25.md
```

### Example 2: Strategy with Assessment

**User:**
```
Develop strategy for Acme Corp
```

**Agent:**
```
Starting strategy development for Acme Corp...

[Specialist checks for assessment]
[Found: AUDIT_FINDINGS_2026-01-25.md]
[Specialist loads assessment as input]

[Specialist loads:
- INSTRUCTIONS.md (summary)
- entities/Acme Corp/ENTITY_PROFILE.md
- entities/Acme Corp/knowledge/LEARNED_CONTEXT.md
- entities/Acme Corp/deliverables/AUDIT_FINDINGS_2026-01-25.md
- ops/preferences/COMMUNICATION_STYLE.md
- ops/style/DELIVERABLE_TEMPLATES.md]

[Specialist develops strategy based on assessment]
[Specialist writes deliverable]

Completed strategy plan for Acme Corp.

Strategic priorities:
- Phase 1 (Months 1-3): Address critical findings
- Phase 2 (Months 4-6): Implement opportunities
- Phase 3 (Months 7-12): Scale improvements

View full plan: entities/Acme Corp/deliverables/STRATEGY_PLAN_2026-01-25.md
```

### Example 3: Batch Assessment

**User:**
```
Run assessment for all clients
```

**Agent:**
```
Running assessment for 3 entities...

[Specialist processes Acme Corp]
Completed Acme Corp... (1/3)

[Specialist processes Beta Industries]
Completed Beta Industries... (2/3)

[Specialist processes Gamma LLC]
Completed Gamma LLC... (3/3)

All assessments complete.

Summary:
- Acme Corp: 5 findings, 2 critical
  Key issues: Infrastructure gaps, security vulnerabilities
  → entities/Acme Corp/deliverables/AUDIT_FINDINGS_2026-01-25.md

- Beta Industries: 3 findings, 0 critical
  Key issues: Documentation needs, process optimization
  → entities/Beta Industries/deliverables/AUDIT_FINDINGS_2026-01-25.md

- Gamma LLC: 4 findings, 1 critical
  Key issues: Compliance gap, workflow inefficiencies
  → entities/Gamma LLC/deliverables/AUDIT_FINDINGS_2026-01-25.md
```

### Example 4: Internal Content Warning

**User:**
```
Generate client report for Acme Corp
```

**Agent:**
```
⚠️  Internal content detected

This deliverable would include internal-only content:
- entities/Acme Corp/INTERNAL_PRICING.md (entire file)
- entities/Acme Corp/knowledge/LEARNED_CONTEXT.md (section: "Margin Analysis")

Deliverable type: Client-facing report

How should I proceed?
1. Generate without internal content (recommended)
2. Generate as internal-only document
3. Cancel generation
```

**User:**
```
Generate without internal content
```

**Agent:**
```
Generating client report for Acme Corp (excluding internal content)...

Completed report.

Note: Internal pricing and margin data excluded from client version.

View report: entities/Acme Corp/deliverables/CLIENT_REPORT_2026-01-25.md
```

### Example 5: Specialist Discovery

**User:**
```
What specialists do you have?
```

**Agent:**
```
Available specialists:

**assessment-specialist**
Conducts assessments, audits, and gap analyses.
Use when you need: current state evaluation, finding gaps, audit work
Model: sonnet
Tools: Read, Grep, Glob, Write

**strategy-specialist**
Develops strategies, plans, and roadmaps.
Use when you need: forward planning, strategic recommendations, goal-setting
Model: opus
Tools: Read, Grep, Glob, Write

To use: "Run assessment for [Entity]" or "Develop strategy for [Entity]"
```

## Integration with Other Protocols

### With Entity Onboarding

After entity creation, specialists can immediately work:
```
User: Create entity Acme Corp
Agent: Created Acme Corp with profile and roadmap.
User: Run assessment for Acme Corp
Agent: Starting assessment... [specialist executes]
```

### With Profile Building

Specialists read entity profiles as knowledge layer 4:
```
User: Add to Acme Corp profile: They're in healthcare
Agent: Added to profile.
User: Run assessment for Acme Corp
Agent: [Specialist loads profile with healthcare context]
```

### With Note Processing

Specialists can reference processed notes:
```
User: Process notes for Acme Corp
Agent: Processed meeting notes, added 3 facts to profile.
User: Run assessment for Acme Corp
Agent: [Specialist loads profile with note-derived facts]
```

## Voice Integration

Specialists can apply the user's voice to client-facing deliverables, ensuring consistency with the user's natural writing style.

### When to Apply Voice

| Deliverable Type | Client-Facing? | Apply Voice? | Modifier |
|------------------|----------------|--------------|----------|
| Assessment findings | Yes | Yes | professional_client |
| Strategy plan | Yes | Yes | professional_client |
| Client report | Yes | Yes | professional_client |
| Proposal | Yes | Yes | professional_client |
| Internal analysis | No | No | N/A |
| Internal notes | No | No | N/A |
| INTERNAL_* files | No | No | N/A |
| Files in internal/ | No | No | N/A |

**Decision logic:**

```
Is deliverable going to the client/external party?
  YES → Apply voice with professional_client modifier
  NO → Skip voice (internal content stays neutral)
```

### Voice Loading for Specialists

Specialists load voice profile during startup, after loading entity knowledge.

**Specialist startup sequence:**
1. Load Layer 1: Base PM Knowledge (INSTRUCTIONS.md)
2. Load Layer 2: Domain Knowledge (if relevant)
3. Load Layer 3: Agency Knowledge (preferences, processes)
4. Load Layer 4: Entity Knowledge (profile, knowledge/)
5. **Load Voice Profile:** Check `~/.anymanage/voice/voice_profile.md` → `ops/voice/voice_profile.md`
6. Begin specialist work

**Voice profile loading:**

```
Check primary location:
  ~/.anymanage/voice/voice_profile.md

If not found, check fallback:
  ops/voice/voice_profile.md

If found and valid:
  Load profile for voice-applied sections
  Note: Voice available for client-facing deliverables

If not found or invalid:
  Continue without voice
  Generate client-facing deliverables in neutral professional tone
```

**Silent failure:** If voice profile is missing or corrupted, specialist continues without voice. No error exposed to user.

### Voice Application in Deliverables

Different deliverable sections get different voice treatment.

#### Assessment Specialist

| Section | Apply Voice? | Rationale |
|---------|--------------|-----------|
| Executive Summary | Yes | Client reads this first - should sound like user |
| Findings (descriptive text) | Yes | Narrative sections benefit from user's voice |
| Findings (data/lists) | No | Factual lists stay neutral |
| Recommendations | Yes | Actionable guidance sounds better in user's voice |
| Technical details | No | Specs and requirements stay neutral |

**Example:**
```markdown
## Executive Summary

[Voice applied - sounds like user explaining findings]

We identified five key areas where Acme Corp can improve...

## Findings

### Finding 1: Infrastructure Gaps

[Voice applied for description]
The current infrastructure setup has some significant gaps...

**Technical Details:**
[No voice - factual]
- Servers: 3x AWS EC2 t3.large
- Database: RDS PostgreSQL 13.4
- Backup: Daily snapshots, 7-day retention

### Recommendations

[Voice applied]
Here's what I'd recommend tackling first...
```

#### Strategy Specialist

| Section | Apply Voice? | Rationale |
|---------|--------------|-----------|
| Executive Summary | Yes | Client reads this first |
| Strategic Vision | Yes | Vision benefits from user's authentic voice |
| Goals & Objectives | Partial | Goal statements: No. Explanations: Yes. |
| Action Plans | Yes | Recommendations sound better in user's voice |
| Timeline/Milestones | No | Dates and deliverables stay neutral |
| Success Metrics | No | KPIs stay factual |

**Example:**
```markdown
## Executive Summary

[Voice applied]
Based on the assessment, here's the strategic plan for Acme Corp...

## Strategic Vision

[Voice applied]
Over the next 12 months, we'll focus on three core areas...

## Goals & Objectives

### Goal 1: Improve Infrastructure Reliability

[Goal statement - no voice]
**Target:** Achieve 99.9% uptime by Q3 2026

[Explanation - voice applied]
This matters because downtime is currently costing Acme about $15k/month...

## Timeline

[No voice - factual]
- Q1 2026: Phase 1 implementation
- Q2 2026: Phase 2 rollout
- Q3 2026: Phase 3 optimization
```

### Modifier Selection for Specialists

Specialists use specific modifiers for voice application.

**Default modifier: `professional_client`**

All client-facing deliverables default to `professional_client` modifier unless:
1. Entity knowledge indicates different relationship (warm/family_client)
2. Deliverable type requires different tone (rare)
3. User explicitly requested different modifier

**Relationship-based modifier override:**

Check entity knowledge for relationship indicators:

```
Check entities/[Entity]/knowledge/LEARNED_CONTEXT.md:
  If contains: "long-term client", "warm relationship", "friendly rapport"
    → Consider family_client modifier

  If contains: "formal relationship", "conservative culture", "legal context"
    → Consider formal modifier

  Default: professional_client
```

**Example:**
```
Entity: Acme Corp
LEARNED_CONTEXT.md contains:
  "10-year client relationship"
  "Weekly casual check-ins with CEO"
  "Very warm and friendly rapport"

→ Strategy specialist uses family_client modifier for deliverable
→ Maintains professionalism but adds warmth from voice profile
```

### Post-Generation Reminder

After generating client-facing deliverable with voice, specialist offers correction learning.

**Reminder pattern:**

```
[Specialist completes deliverable]
[Saves to entities/[Entity]/deliverables/]

Agent announces:
  Completed [deliverable type] for [Entity].

  View: entities/[Entity]/deliverables/[FILE].md

  [If voice was applied]
  This deliverable uses your voice profile. If anything doesn't sound like you, let me know or log it to CORRECTIONS.md for learning.
```

**Correction learning integration:**

Users can provide corrections:
1. **In-session:** "Actually, I'd phrase that section differently..."
2. **Async:** Log to CORRECTIONS.md, process later with `/process-corrections`

See: `docs/protocols/correction-learning.md`

### Voice Profile Age Check

Specialists check voice profile age when loading.

**Age detection:**
```
Check file modification date of voice_profile.md

If older than 6 months:
  Log note (don't interrupt specialist)

If older than 1 year:
  After deliverable completion, suggest refresh:
    "Your voice profile is 14 months old. Want to refresh it with recent writing samples?"
```

**Rationale:** Writing styles evolve. Old profiles may not match current voice.

### Integration Protocol References

Voice integration relies on these protocols:

- **Voice Application:** See `docs/protocols/voice-application.md`
  - Profile loading (primary → fallback)
  - Modifier calibration (professional_client default)
  - Quality verification checklist

- **Correction Learning:** See `docs/protocols/correction-learning.md`
  - In-session correction path
  - Async CORRECTIONS.md processing
  - Pattern extraction and profile updates

- **Voice Training:** See `docs/protocols/voice-training.md`
  - Initial profile creation
  - Content type selection
  - Sample collection and extraction

## Related Protocols

- **Entity Onboarding:** See `docs/protocols/entity-onboarding.md`
- **Profile Building:** See `docs/protocols/profile-building.md`
- **Note Processing:** See `docs/protocols/note-processing.md`
- **Session Protocol:** See `docs/protocols/session-protocol.md`
- **Voice Application:** See `docs/protocols/voice-application.md`
- **Correction Learning:** See `docs/protocols/correction-learning.md`
- **Voice Training:** See `docs/protocols/voice-training.md`

---

*Protocol: specialist-coordination*
*Version: 1.1*
*Last updated: 2026-01-25 (Added Voice Integration)*
