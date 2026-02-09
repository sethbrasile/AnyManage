# Conversational Knowledge Management System

## What This Is

A file-based project management and knowledge management system operated primarily through conversation with an AI coding assistant. It is a git repository of Markdown files that, combined with structured instructions, turns an LLM into a domain-expert project manager capable of reading, writing, querying, and maintaining a rich information graph across many entities over arbitrarily long time horizons.

The repository IS the database. The AI IS the interface. There is no traditional UI, no web app, no dashboard software. The user talks to the AI in natural language, and the AI reads and writes Markdown files following defined protocols. Git provides versioning, history, and backup.

---

## How It Works (UX)

### The Session Loop

1. **User opens a terminal** and starts the AI assistant in the repo directory.
2. **The AI auto-loads context.** The master instruction file is injected as system instructions. The AI reads the master dashboard, identifies active work, and reads relevant entity documents. This takes seconds.
3. **The user talks naturally.** Examples:
   - "Process notes for Guardian 6" -- the AI reads raw meeting notes, extracts action items into the roadmap, updates the entity profile with new information, logs decisions, and marks the notes as processed.
   - "What's the status of Willowood Ranch?" -- the AI reads their roadmap and gives a summary with completion percentages.
   - "Add that Sherman does roofing services too" -- the AI updates the entity profile and logs the change.
   - "Sync Trello" -- the AI reads the Trello board via MCP, compares it to local roadmaps, and reconciles differences following documented conflict resolution rules.
   - "Onboard a new client called Jimmy Johns" -- the AI runs an automation script that creates the entire folder structure from templates.
   - "Draft a proposal for Sherman Restoration" -- the AI reads all entity context and generates a professional document.
   - "Show me all entities using local SEO services" -- the AI queries frontmatter metadata across all entity profiles and returns matching results.
   - "It's been a while since we checked KPIs for Willowood" -- the AI checks the recurring task schedule and initiates a KPI review.
4. **The AI modifies files.** Every action produces a traceable file change in git.
5. **User commits when ready.** The AI can be asked to commit, but does not push without permission.

### What Makes It Different From Traditional Project Management Tools

- **No data entry.** Brain-dump meeting notes or paste a transcript, and the AI structures it into the right places across multiple files.
- **No context loss.** Every session, the AI re-reads current state. Between sessions, a memory plugin maintains a searchable semantic index of past observations, decisions, and discoveries.
- **No training required.** The master instruction file teaches the AI how to behave. New team members (human or AI) just read the instructions.
- **Cross-entity intelligence.** The AI sees all entities simultaneously, identifies patterns, applies learnings from one engagement to another, references shared playbooks, and maintains consistency.
- **Progressive depth.** An entity can start with a 10-line profile and 3 roadmap items. Over months, that grows to hundreds of lines of strategy documents, planning specs, automation workflows, pricing models, and processed meeting transcripts -- all interconnected.
- **Queryable metadata.** YAML frontmatter on every document enables structured queries without reading full content ("show me all entities in phase X", "which entities have overdue tasks").
- **Proactive notifications.** The AI checks recurring task schedules and surfaces overdue reviews, stale entities, and unprocessed notes.

---

## Architecture

### Directory Structure

```
project-root/
|-- INSTRUCTIONS.md              # Master AI instructions (the "brain")
|-- DASHBOARD.md                 # Master status: all entities, all status
|-- README.md                    # Human-readable project docs
|-- Makefile                     # Automation shortcuts
|-- scripts/                     # Shell scripts for onboarding, note creation
|
|-- entities/                    # One folder per managed entity
|   |-- Entity A/
|   |   |-- PROFILE.md           # Static reference: who they are, what they need
|   |   |-- ROADMAP.md           # Active work tracker: tasks, priorities, status
|   |   |-- DECISIONS.md         # Decision log with rationale and dates
|   |   |-- CHANGELOG.md         # Human-readable timeline of all activity
|   |   |-- notes/               # Raw meeting notes, brain dumps, transcripts
|   |   |-- planning/            # Technical specs, architecture docs
|   |   |-- strategy/            # Business strategy, positioning, pricing
|   |   |-- execution/           # Work products in progress
|   |   |-- tracking/            # KPIs, metrics, reports
|   |   |-- current_state/       # Audit findings, gap analysis
|   |   |-- assets/              # Deliverables, content, reference materials
|   |   |-- automations/         # Workflow specifications
|   |   |-- agreements/          # Contracts, proposals, legal documents
|   |   └-- agent_logs/          # Logs from specialist AI agents
|   |-- Entity B/
|   └-- ...
|
|-- ops/                         # Internal operations
|   |-- TEAM.md                  # Team roster and roles
|   |-- PRICING.md               # Service catalog with pricing
|   |-- PROCESSES.md             # SOPs and workflows
|   |-- ONBOARDING.md            # Onboarding checklist
|   |-- RECURRING.md             # Recurring task schedule definitions
|   |-- SYNC_PLAYBOOK.md         # External tool sync protocol
|   |-- team_profiles/           # Individual team member bios
|   └-- integrations/            # Integration configs and sync status
|       |-- trello/
|       |-- crm/
|       └-- calendar/
|
|-- knowledge/                   # Reusable knowledge base
|   |-- playbooks/               # Step-by-step methodology guides
|   |-- frameworks/              # Reusable analytical frameworks
|   |-- best_practices/          # Channel and domain best practices
|   |-- platform_howtos/         # Procedural knowledge per platform
|   └-- lessons_learned/         # Post-mortems and learnings
|
|-- templates/                   # Document templates (implicit schemas)
|   |-- PROFILE_TEMPLATE.md
|   |-- ROADMAP_TEMPLATE.md
|   |-- DECISIONS_TEMPLATE.md
|   |-- CHANGELOG_TEMPLATE.md
|   └-- ...
|
|-- .ai/                         # AI assistant configuration
|   |-- settings.json            # Permission gates
|   |-- agents/                  # Specialist subagent definitions
|   |-- skills/                  # Custom skill definitions
|   └-- rules/                   # Session initialization rules
|
└-- .validation/                 # Validation and linting
    |-- lint.sh                  # Structural validation script
    └-- rules.yaml               # Validation rule definitions
```

### The Two Core Documents Per Entity

Every managed entity has two primary documents that serve complementary purposes:

**PROFILE.md** -- *What is true about this entity* (relatively static)

All profile documents include YAML frontmatter for structured querying:

```yaml
---
entity_type: client
entity_name: Sherman Restoration Services
phase: planning_and_scoping
status: active
tags: [local-seo, restoration, small-business]
related_to: []
last_updated: 2026-02-09
confidence: high
---
```

Content sections include: entity information, products/services, goals, contacts, engagement terms, competitive landscape, and relevant history. The exact sections are defined by the domain-specific template.

**ROADMAP.md** -- *What we're doing about it* (changes frequently)

Frontmatter includes computed metadata:

```yaml
---
entity: Sherman Restoration Services
current_phase: Planning & Scoping
last_updated: 2026-02-09
tasks: { todo: 12, in_progress: 3, done: 5 }
next_review: 2026-03-09
---
```

Content is organized by priority tier (High/Medium/Low/Strategic) with task status tracking:
- `[ ]` todo
- `[-]` in progress (with start date)
- `[x]` completed (with completion date)
- Blocking dependencies noted inline
- Recently Completed section for historical record

This separation means the AI can update work status dozens of times without touching stable reference information, and can update reference information without disturbing the task structure.

### Additional Per-Entity Documents

**DECISIONS.md** -- *Why we chose what we chose*

A chronological log of decisions with context, rationale, alternatives considered, and who made the call. Enables answering "why did we choose X?" months later without searching through notes.

```markdown
## 2026-02-06: Pivot to merchandise-first revenue model

**Context:** Meeting with stakeholders revealed training alone cannot sustain operations in year one.
**Decision:** Lead with merchandise sales (nationwide e-commerce), use training content for brand authority.
**Alternatives Considered:** Training-first launch, hybrid simultaneous launch.
**Rationale:** Merch has lower barrier to entry, broader market, and funds training infrastructure.
**Decision Makers:** Tom Dickson, Martin Davis
**Impact:** Restructures entire roadmap priority order. Website needs dual-audience strategy.
```

**CHANGELOG.md** -- *What happened and when*

An auto-maintained timeline of all significant activity, generated from note processing, roadmap updates, and external sync events. Provides a human-readable history without needing `git log`.

```markdown
## February 2026

- **Feb 09** -- Processed meeting notes from Feb 6 strategy session. 8 decisions logged, 17 action items added to roadmap.
- **Feb 09** -- 15 Trello cards created for strategic pivot work.
- **Feb 06** -- Strategy alignment meeting. Major pivot to merch-first model decided.
```

### Information Flow

```
RAW INPUT (meeting notes, brain dumps, transcripts, emails)
    |
    v
NOTE PROCESSING PROTOCOL (AI reads, extracts, distributes)
    |
    |-->  Action items       ->  ROADMAP.md (prioritized task list)
    |-->  Entity facts       ->  PROFILE.md (reference document)
    |-->  Decisions made     ->  DECISIONS.md (decision log with rationale)
    |-->  Strategic insights  ->  strategy/ folder (positioning, pricing)
    |-->  Technical specs     ->  planning/ folder (architecture, implementation)
    |-->  Activity record     ->  CHANGELOG.md (timeline entry)
    └-->  Processing marker   ->  Appended to original note with timestamp

ONGOING OPERATIONS
    |
    |-->  External Tools  <-->  ROADMAP.md (bidirectional sync via integration plugins)
    |-->  DASHBOARD.md          (auto-generated rollup from entity roadmaps)
    |-->  knowledge/            (lessons learned, playbooks, howtos)
    └-->  ops/                  (internal processes, pricing, team info)
```

### Entity State Machine

Every entity progresses through a defined lifecycle with explicit transitions:

```
Prospect --> Onboarding --> Assessment --> Planning & Scoping --> Execution --> Optimization --> Maintenance
                                                                     |
                                                                     v
                                                                  Archive
```

**Transition criteria are defined per state:**
- Prospect -> Onboarding: Agreement signed, folder created
- Onboarding -> Assessment: Profile populated, access granted
- Assessment -> Planning: Audit complete, gaps identified
- Planning -> Execution: Strategy approved, tasks defined
- Execution -> Optimization: Initial deliverables complete, KPIs being tracked
- Optimization -> Maintenance: Performance targets met, steady-state operations

The current phase is tracked in both PROFILE.md frontmatter and ROADMAP.md, enabling phase-based queries across all entities.

---

## Templates as Schema

Templates are not just blank documents -- they are **implicit schemas** that define what information exists about each entity type. PROFILE_TEMPLATE.md is effectively a database schema written in Markdown. The AI knows what fields to populate and can identify when information is missing.

The template system is parameterizable for different domains:

| Domain | Entity Type | Profile Contains | Roadmap Tracks |
|--------|-------------|------------------|----------------|
| Marketing Agency | Client | Company info, services, budget, contacts | Campaigns, deliverables, milestones |
| Law Practice | Case | Parties, jurisdiction, key dates, evidence | Filings, depositions, motions |
| Real Estate | Property | Address, valuation, tenants, condition | Renovations, showings, offers |
| Healthcare | Patient | History, conditions, medications, contacts | Treatment plans, appointments, labs |
| Education | Student | Enrollment, grades, contacts, accommodations | Assignments, IEP goals, interventions |
| Personal | Life Area | Goals, context, resources, constraints | Tasks, habits, milestones |

The folder structure, document types, and processing protocols remain identical across domains. Only the template content changes.

---

## Specialist Subagents

Complex work is delegated to defined specialist agents, each with:
- A specific AI model tier (cost-optimized or premium reasoning)
- Defined tool access permissions
- Explicit output format and deliverable list
- Domain expertise encoded in their instruction file

Example agent roster (marketing domain):

| Agent | Model Tier | Responsibilities | Output Files |
|-------|-----------|------------------|--------------|
| Assessment | Standard | Audits, competitive analysis, gap identification | AUDIT_FINDINGS.md, GAPS_ANALYSIS.md |
| Strategy | Premium | Positioning, messaging, customer journey mapping | MARKETING_STRATEGY.md, POSITIONING.md |
| Execution | Premium | Campaign planning, content calendars, launch readiness | CAMPAIGN_CALENDAR.md, CONTENT_PLAN.md |
| Analytics | Standard | KPI frameworks, dashboards, performance analysis | KPI_DASHBOARD.md, MONTHLY_REPORTING.md |

Agents log their work in `agent_logs/` within each entity folder, creating an audit trail of AI-assisted work.

---

## Skills (Triggered Workflows)

Skills are named protocols that can be invoked by the user or triggered by context. Each skill is a Markdown instruction file that the AI loads and follows.

### Note Processing Skill

**Trigger:** "Process notes for [Entity]"

**Protocol:**
1. Read raw notes from `entities/[name]/notes/YYYY-MM-DD_[topic].md`
2. Extract action items -> add to ROADMAP.md with `[ ]` status
3. Extract decisions -> add to DECISIONS.md with full context
4. Update PROFILE.md if new entity information discovered
5. Update strategy/planning docs if strategic content found
6. Append activity entry to CHANGELOG.md
7. Mark notes as processed with timestamp
8. Report summary of extractions

### External Tool Sync Skill

**Trigger:** "Sync [tool name]" or proactive schedule

**Protocol:**
1. Pull current state from external tool via API/MCP
2. Compare to local ROADMAP.md items
3. Reconcile using defined conflict resolution rules (external tool wins for operational status, local files win for strategic planning)
4. Push new local items to external tool
5. Log sync event in CHANGELOG.md
6. Update sync status tracking document

### Validation Skill

**Trigger:** "Validate [Entity]" or periodic schedule

**Checks:**
- Entity folder has all required documents (PROFILE, ROADMAP, DECISIONS, CHANGELOG)
- No orphaned tasks (in roadmap but not in external tool, or vice versa)
- All notes have been processed (check for processing marker)
- No duplicate information between profile and roadmap
- Template compliance (all required sections present)
- Frontmatter integrity (required fields populated, valid values)
- Recurring tasks not overdue
- Phase transition criteria met for current state

---

## Cross-Entity Querying

YAML frontmatter on every document enables structured queries without reading full content:

**By tag:** "Show me all entities tagged with local-seo"
- AI greps frontmatter across all PROFILE.md files for `tags: [... local-seo ...]`

**By phase:** "Which entities are in execution phase?"
- AI greps frontmatter for `phase: execution`

**By relationship:** "What entities are connected to MarketStorm?"
- AI greps frontmatter for `related_to: [... MarketStorm ...]`

**By staleness:** "Which entities haven't been updated in 30+ days?"
- AI compares `last_updated` frontmatter to current date

**By completion:** "What's the overall task completion rate?"
- AI reads `tasks` frontmatter from all ROADMAPs and computes aggregate statistics

---

## Recurring Task System

`ops/RECURRING.md` defines scheduled tasks that the AI checks proactively:

```markdown
## Monthly
- [ ] KPI dashboard update for each active entity (1st of month)
- [ ] Status rollup to DASHBOARD.md (1st of month)
- [ ] Stale entity check (entities with no activity in 30+ days)

## Quarterly
- [ ] Strategy review for all entities in Execution or Optimization phase
- [ ] Knowledge base review (update playbooks, archive outdated content)
- [ ] Team retrospective and process improvement

## Per-Entity Triggers
- [ ] If days_since_last_note > 14: surface "No recent notes for [Entity]"
- [ ] If phase == "Assessment" and days_in_phase > 21: surface "Assessment running long for [Entity]"
- [ ] If unprocessed_notes > 0 at session start: surface "Unprocessed notes found"
```

At session start, the AI checks these definitions against current state and surfaces anything due or overdue.

---

## Automated Status Rollup

DASHBOARD.md is auto-generated by scanning all entity ROADMAP.md files. The AI computes:

- Entity count by phase
- Task completion percentages per entity
- Entities with blockers or overdue items
- Recently completed milestones
- Upcoming deadlines

This eliminates manual dashboard maintenance and ensures the master view is always current.

---

## Integration Architecture

External tool integrations follow a plugin pattern:

```
ops/integrations/
|-- trello/
|   |-- SYNC_PLAYBOOK.md     # How to sync (protocol)
|   |-- SYNC_STATUS.md       # Current sync state
|   └-- CONFLICT_RULES.md    # What wins when there's a conflict
|-- crm/
|   |-- SYNC_PLAYBOOK.md
|   └-- FIELD_MAPPING.md     # How CRM fields map to local documents
|-- calendar/
|   |-- SYNC_PLAYBOOK.md
|   └-- EVENT_MAPPING.md
```

Each integration defines:
- **Sync protocol:** Pull/push procedures
- **Conflict resolution:** Which source of truth wins for which data types
- **Field mapping:** How external data maps to local document structure
- **Status tracking:** Current sync state and last sync timestamp

This pattern is extensible to any external system: CRM, calendar, email, analytics, financial, or communication tools.

---

## Validation and Linting

A validation script checks structural integrity across the entire repository:

```bash
# Run all validation checks
make validate

# Check a specific entity
make validate entity="Sherman Restoration Services"
```

**Checks performed:**
- Every entity folder has PROFILE.md, ROADMAP.md, DECISIONS.md, CHANGELOG.md
- All PROFILE.md files have valid YAML frontmatter with required fields
- No tasks exist in ROADMAP.md without corresponding external tool cards (if sync is configured)
- All notes have processing markers (or are flagged as unprocessed)
- No information duplicated between PROFILE.md and ROADMAP.md
- All required template sections are present in each document
- Frontmatter `last_updated` dates are within expected staleness thresholds
- Phase values match the defined state machine transitions
- Recurring tasks are not overdue

Results are written to `.validation/report.md` and surfaced at session start if issues are found.

---

## Multi-User Awareness

Session startup includes user identification:

```
AI: "Who am I speaking with?"
User: "Seth"
AI: [Loads Seth's role context: Systems/Automation/CRM/SEO/AI/Dev]
    [Filters relevant tasks and surfaces technical details]
    [Uses appropriate communication style]
```

Different team members see different views:
- **Technical leads** get architecture details, implementation specifics, and system status
- **Strategy leads** get client relationship context, campaign performance, and strategic recommendations
- **Creative leads** get content briefs, brand guidelines, and asset status

User context is stored in `ops/team_profiles/` and referenced during session initialization.

---

## Confidence and Completeness Indicators

When the AI generates documents (proposals, strategies, reports), it includes confidence markers:

```markdown
## Proposal for Sherman Restoration Services

> **Completeness: 92%** -- Missing: competitor pricing data, client approval on CTA copy
> **Confidence: High** -- All scope items verified against CLIENT_PROFILE.md and meeting notes
> **Data Sources:** CLIENT_PROFILE.md (Feb 9), meeting notes (Feb 4), website research (Feb 9)
```

This helps humans focus review effort on areas where the AI had incomplete information or made inferences.

---

## Knowledge Graph Connections

Documents explicitly link to the methodology they follow:

```markdown
## SEO Buildout Plan

**Methodology:** Follows [LOCAL_SEO_PLAYBOOK.md](../../knowledge/playbooks/LOCAL_SEO_PLAYBOOK.md), sections 2, 4, 7
**Adapted for:** 4-location service area, 43 service categories
```

This creates traceable connections between strategic playbooks and actual implementation, enabling:
- Methodology audits ("are we following our own playbooks?")
- Playbook improvements ("section 4 needed adaptation for multi-location -- update it")
- Onboarding acceleration ("here's the playbook we used, here's how we adapted it")

---

## Summary: The Components

At its core, this is a **conversational knowledge management system** where:

1. **Markdown files are the database** -- structured enough for machines, readable by humans
2. **The instruction file is the application logic** -- it defines behaviors, protocols, and workflows
3. **Templates are the schema** -- they define what information exists for each entity type
4. **The AI is the interface** -- no forms, no dashboards, just natural language
5. **Git is the infrastructure** -- versioning, history, backup, collaboration
6. **Skills are the extensions** -- pluggable triggered workflows for specific operations
7. **Subagents are the specialists** -- delegatable expertise at different quality/cost tiers
8. **External tool sync is the integration layer** -- connecting to where the broader team works
9. **Frontmatter is the query layer** -- structured metadata enabling cross-entity search
10. **Validation is the safety net** -- automated checks ensuring structural integrity
11. **Recurring tasks are the heartbeat** -- proactive surfacing of due and overdue work
12. **Decision logs are the institutional memory** -- capturing not just what was decided, but why

The same architecture -- instruction file + templates + entity folders + shared knowledge + external sync -- can manage any domain where entities need to be tracked, information needs to be remembered, and work needs to be coordinated over time. The pattern is domain-agnostic; only the content of the instructions and templates changes.
