# AnyManage Improvement Analysis

Comparison of the vision described in `SYSTEM_DESCRIPTION.md` against the current state of the AnyManage codebase, with layman-friendly improvement recommendations.

**Date:** 2026-02-09

---

## Executive Summary

The `SYSTEM_DESCRIPTION.md` describes a mature "conversational knowledge management system" with rich features like YAML-powered cross-entity querying, decision logs, changelogs, entity lifecycle tracking, recurring task automation, knowledge bases, validation, multi-user awareness, and a full integration plugin architecture. AnyManage in its current state (v1.0) has solid foundations -- entity management, note processing, profile building, git abstraction, voice training, two specialist agents, and a Trello integration framework -- but is missing roughly 60% of the features described in the vision document.

The good news: the architecture is sound and extensible. The current system was built with the right separation of concerns (templates as schema, protocols as behavior, agents as specialists). The gaps are mostly additive -- new templates, new protocols, new automation -- not architectural rewrites.

The core challenge throughout: the vision document describes many features in technical terms (YAML frontmatter, Makefile validation, shell scripts, cross-file grep queries). For a layman audience, every one of these features needs to be surfaced through natural language conversation, never through CLI commands or file editing.

---

## Category 1: Missing Document Types and Templates

These are structural gaps -- the vision describes documents that don't exist yet.

### 1.1 No DECISIONS.md Per Entity

**What the vision describes:** Every entity gets a `DECISIONS.md` log capturing what was decided, why, what alternatives were considered, and who decided. This creates institutional memory so months later anyone can ask "why did we choose X?" and get a real answer.

**Current state:** Completely absent. No template, no protocol, no mention in entity onboarding.

**Impact:** Without decision logs, the system loses the "why" behind choices. Meeting notes get processed into tasks and profile facts, but the reasoning behind strategic decisions evaporates.

**Layman-friendly implementation:**
- Add `ENTITY_DECISIONS_TEMPLATE.md` to templates/
- Update entity onboarding to create `DECISIONS.md` in each entity folder
- Update note processing to detect decisions and route them to DECISIONS.md (e.g., "We decided to go with vendor X because...")
- User never touches the file -- AI populates it during note processing and users can ask "Why did we decide X for Acme?"

---

### 1.2 No CHANGELOG.md Per Entity

**What the vision describes:** An auto-maintained timeline of all significant activity per entity, generated from note processing, roadmap updates, specialist work, and sync events. Provides human-readable history without needing git log.

**Current state:** Completely absent.

**Impact:** There's no way to quickly see "what happened with this entity and when?" The profile shows current state, the roadmap shows tasks, but neither shows a timeline of activity. Users have to piece together history from multiple files.

**Layman-friendly implementation:**
- Add `ENTITY_CHANGELOG_TEMPLATE.md` to templates/
- Auto-populate during every significant operation (note processing, profile update, specialist deliverable, roadmap change)
- User says "What's happened with Acme lately?" and AI reads the changelog
- No user interaction needed to maintain it -- it's a side effect of normal operations

---

### 1.3 No YAML Frontmatter on Entity Documents

**What the vision describes:** Every PROFILE.md has structured YAML frontmatter:
```yaml
---
entity_type: client
entity_name: Sherman Restoration
phase: planning_and_scoping
status: active
tags: [local-seo, restoration, small-business]
related_to: []
last_updated: 2026-02-09
confidence: high
---
```

Every ROADMAP.md has computed metadata:
```yaml
---
entity: Sherman Restoration
current_phase: Planning & Scoping
tasks: { todo: 12, in_progress: 3, done: 5 }
next_review: 2026-03-09
---
```

**Current state:** Entity documents have zero frontmatter. The profile template is pure Markdown with no structured metadata.

**Impact:** This is arguably the single biggest gap. Without frontmatter, the system **cannot** do cross-entity queries. "Show me all entities in execution phase" requires the AI to read every single profile and parse free text. With frontmatter, it can grep metadata instantly.

**Layman-friendly implementation:**
- Add frontmatter to both templates (ENTITY_PROFILE_TEMPLATE.md and ENTITY_ROADMAP_TEMPLATE.md)
- AI auto-populates frontmatter during entity creation and updates it during every profile/roadmap change
- Users never see or edit frontmatter -- they say "tag Acme with local-seo" and the AI updates the frontmatter
- Add a protocol for frontmatter management (auto-update `last_updated`, recompute task counts, track phase)
- This enables powerful queries like "Which clients haven't been updated in 30 days?" or "Show me all active projects"

---

## Category 2: Missing Operational Systems

These are workflow and automation gaps -- things the system should do proactively.

### 2.1 No Entity Lifecycle/State Machine

**What the vision describes:** Every entity progresses through defined phases:
```
Prospect -> Onboarding -> Assessment -> Planning & Scoping -> Execution -> Optimization -> Maintenance
```
With explicit transition criteria (e.g., "Assessment -> Planning: Audit complete, gaps identified").

**Current state:** Entity roadmaps have a "Current Phase" section, but there's no defined lifecycle, no transition criteria, and no system-level tracking of what phase each entity is in.

**Impact:** Without a state machine, there's no way to answer "Which entities are in what phase?" and no proactive nudging when entities are stuck in a phase too long.

**Layman-friendly implementation:**
- Define default lifecycle phases in CONFIG.md (customizable per domain)
- Track current phase in frontmatter (see 1.3 above)
- AI suggests phase transitions: "Acme's assessment is complete. Ready to move to Planning & Scoping?"
- User can customize phases: "Add a 'Discovery' phase before Assessment"
- Add a skill or protocol for phase transition that updates all relevant files

---

### 2.2 No Recurring Task System

**What the vision describes:** `ops/RECURRING.md` defines scheduled tasks:
- Monthly: KPI dashboard update, status rollup, stale entity check
- Quarterly: Strategy review, knowledge base review
- Per-entity triggers: "If no notes in 14 days, surface warning"

At session start, the AI checks these and surfaces anything due or overdue.

**Current state:** Completely absent. No recurring tasks, no proactive surfacing, no staleness detection.

**Impact:** Users must remember to do periodic reviews themselves. The system is purely reactive -- it only does what you ask. The vision describes a system that proactively reminds you of overdue reviews, stale entities, and unprocessed notes.

**Layman-friendly implementation:**
- Create `ops/RECURRING.md` with sensible defaults
- At session start, check recurring definitions against entity state
- Surface reminders naturally: "Heads up -- it's been 3 weeks since you touched Beta Industries. Want to check in?"
- Users configure by saying "Remind me to review active clients monthly" and the AI updates RECURRING.md
- No cron jobs, no scripts -- the AI checks at session start

---

### 2.3 No Automated Dashboard Rollup

**What the vision describes:** DASHBOARD.md is auto-generated by scanning all entity ROADMAP.md files. Computes entity count by phase, task completion percentages, entities with blockers, upcoming deadlines.

**Current state:** ROADMAP.md serves as the master dashboard but is manually maintained. Adding/updating entries requires the AI to edit it explicitly during each operation.

**Impact:** The dashboard can drift from reality. If the AI misses an update, the master view is stale. Also, there are no computed metrics (completion percentages, phase distribution).

**Layman-friendly implementation:**
- When user says "Show status" or "Dashboard," AI should dynamically scan all entity roadmaps and compute current state rather than just reading the static ROADMAP.md
- Periodically regenerate ROADMAP.md from entity state (or do it on demand)
- Include computed metrics: "You have 5 active clients: 1 in Onboarding, 2 in Execution, 2 in Optimization. Overall 67% of tasks complete."
- Add a weekly-review skill that automatically generates this rollup

---

### 2.4 No Validation or Structural Integrity Checks

**What the vision describes:** A validation system that checks:
- Every entity has required documents
- All frontmatter has valid values
- No orphaned tasks between local and external tools
- All notes have been processed
- No duplicated information
- Template compliance
- Phase transition criteria

**Current state:** No validation at all. No lint script, no validation rules, no integrity checks.

**Impact:** Over time, entities can drift from template structure. New entities might be missing subfolders. Notes might go unprocessed indefinitely. There's no safety net.

**Layman-friendly implementation:**
- Run validation silently at session start
- Only surface issues when found: "I noticed 2 unprocessed notes for Acme and Beta's profile is missing key contacts."
- Add a "Check my setup" command that runs full validation
- Never expose technical details -- just "Here are 3 things that need attention"
- Store validation rules in a simple Markdown file that the AI reads (not a shell script)

---

## Category 3: Missing Knowledge Infrastructure

### 3.1 No Knowledge Base

**What the vision describes:** A `knowledge/` directory with:
- `playbooks/` -- Step-by-step methodology guides
- `frameworks/` -- Reusable analytical frameworks
- `best_practices/` -- Domain best practices
- `platform_howtos/` -- Procedural knowledge
- `lessons_learned/` -- Post-mortems and learnings

**Current state:** `docs/domain/` exists but is empty (just a .gitkeep). No playbooks, no frameworks, no best practices, no lessons learned.

**Impact:** The system can't learn from its own work. When the AI does an assessment for one entity, the insights don't automatically become reusable knowledge for similar entities. Each engagement starts from scratch.

**Layman-friendly implementation:**
- Create `knowledge/` directory with the subdirectories listed above
- When specialists complete work, prompt: "This assessment uncovered useful patterns for [industry type]. Save to knowledge base?"
- User can say "What do we know about SEO assessments?" and the AI searches the knowledge base
- Lessons learned captured after major milestones: "What worked? What didn't?"
- Knowledge auto-referenced by specialists during future work

---

### 3.2 No Knowledge Graph Connections

**What the vision describes:** Documents explicitly link to the methodology they follow:
```markdown
**Methodology:** Follows LOCAL_SEO_PLAYBOOK.md, sections 2, 4, 7
**Adapted for:** 4-location service area
```

This enables methodology audits, playbook improvements, and onboarding acceleration.

**Current state:** No methodology linking. Deliverables are standalone documents.

**Impact:** No traceability between "how we said we'd do things" and "how we actually did them."

**Layman-friendly implementation:**
- When specialists generate deliverables, auto-include methodology references
- User can ask "Are we following our own playbooks?"
- When a playbook is updated, identify entities where the old version was used
- This builds naturally on top of the knowledge base (3.1)

---

### 3.3 No Team/Operations Infrastructure

**What the vision describes:** `ops/` contains:
- `TEAM.md` -- Team roster and roles
- `PRICING.md` -- Service catalog with pricing
- `PROCESSES.md` -- SOPs and workflows
- `ONBOARDING.md` -- Team onboarding checklist
- `RECURRING.md` -- Recurring task schedules
- `team_profiles/` -- Individual team member bios

**Current state:** `ops/` has only `TASK_SYNC_PLAYBOOK.md`, `logs/`, and `voice/`. No team management, no pricing, no processes.

**Impact:** The system is single-user only. There's no concept of team roles, service offerings, or standard processes beyond what's in the templates.

**Layman-friendly implementation:**
- Add template files for team management docs
- User says "Add team member Sarah, she handles design" -- AI creates/updates TEAM.md
- User says "Our SEO package costs $2,000/month" -- AI updates PRICING.md
- These become inputs for specialist agents (e.g., strategy specialist references PRICING.md when developing proposals)

---

## Category 4: Missing Intelligence Features

### 4.1 No Cross-Entity Querying

**What the vision describes:** Rich queries across all entities:
- By tag: "Show me all entities tagged with local-seo"
- By phase: "Which entities are in execution phase?"
- By relationship: "What entities are connected to MarketStorm?"
- By staleness: "Which entities haven't been updated in 30+ days?"
- By completion: "What's the overall task completion rate?"

**Current state:** None of these queries work because there's no structured metadata (frontmatter). The AI would have to read every file to answer any cross-entity question.

**Layman-friendly implementation:**
- Depends on implementing frontmatter (1.3) first
- Once frontmatter exists, the AI can grep metadata fields instantly
- User just asks naturally: "Which clients need attention?" or "Who's tagged with social-media?"
- The AI surfaces answers without the user knowing about frontmatter

---

### 4.2 No Multi-User Awareness

**What the vision describes:** Session startup includes user identification. Different team members see different views based on their role. User context stored in `ops/team_profiles/`.

**Current state:** Single-user only. No user identification, no role-based context.

**Impact:** If multiple people use the system, there's no personalization. A technical lead and a strategy lead see the same thing.

**Layman-friendly implementation:**
- At first use, ask "What's your name and role?"
- Store in `ops/team_profiles/[name].md`
- At session start, load user context for communication style and relevant focus areas
- Could be as simple as: "Welcome back, Seth. You're focused on the technical side. 3 entities have pending technical tasks."
- Don't over-engineer -- simple name + role + preferences is enough for v2

---

### 4.3 No Confidence and Completeness Indicators

**What the vision describes:** AI-generated documents include:
```markdown
> **Completeness: 92%** -- Missing: competitor pricing data
> **Confidence: High** -- All scope items verified
> **Data Sources:** PROFILE.md (Feb 9), meeting notes (Feb 4)
```

**Current state:** Specialist deliverables have no confidence tracking. No indication of what data was available vs. missing.

**Impact:** Users can't quickly assess where to focus their review effort. A proposal based on incomplete data looks the same as one with full context.

**Layman-friendly implementation:**
- Update specialist agents to include a "Data Quality" section in deliverables
- Track which profile sections have real data vs. placeholders
- Surface naturally: "This assessment is based on limited information -- Acme's profile is only 40% complete. Want to fill in more details first?"
- Helps users prioritize what information to gather

---

### 4.4 No Proactive Cross-Entity Intelligence

**What the vision describes:** The AI sees all entities simultaneously, identifies patterns, applies learnings from one engagement to another, and maintains consistency.

**Current state:** Each entity is an island. The AI processes one entity at a time and doesn't proactively connect dots between entities.

**Impact:** Missed opportunities for pattern recognition. If Entity A had a great SEO result from Strategy X, Entity B in the same industry could benefit, but the system doesn't surface this.

**Layman-friendly implementation:**
- During specialist work, search knowledge base for similar entity contexts
- During weekly reviews, highlight patterns: "3 of your 5 clients have the same issue with social media engagement"
- When onboarding a new entity, reference similar existing entities: "This looks similar to Acme Corp -- want me to apply what worked there?"
- Builds on knowledge base (3.1) and frontmatter tagging (1.3)

---

## Category 5: Missing or Incomplete Skills

### 5.1 Weekly Review Skill Needs Enhancement

**Current state:** A weekly-review skill exists but is basic.

**What it should do (per vision):**
- Auto-scan all entity roadmaps and compute metrics
- Identify stale entities (no activity in X days)
- Surface unprocessed notes across all entities
- Check recurring task schedule for due items
- Generate a structured summary with action items
- Detect blocked tasks and surface them

**Layman-friendly improvement:**
- Make it truly automatic: "Here's your weekly summary" with zero user input
- Include actionable recommendations, not just data
- Track week-over-week changes: "4 new tasks completed this week, 2 new blockers"

---

### 5.2 No Validation Skill

**What the vision describes:** "Validate [Entity]" checks all required documents exist, no orphaned tasks, all notes processed, template compliance, frontmatter integrity.

**Current state:** No validation skill at all.

**Layman-friendly implementation:**
- User says "Check [Entity]" or "Is everything in order?"
- AI runs checks and reports in plain English: "Everything looks good" or "Found 3 issues: ..."
- Can run across all entities: "Check all clients"

---

### 5.3 Missing Specialist Agents

**What the vision describes:** Four specialist types: Assessment, Strategy, Execution, Analytics.

**Current state:** Only Assessment and Strategy exist.

**Missing specialists:**
- **Analytics specialist:** KPI frameworks, dashboards, performance analysis, monthly reporting
- **Communication specialist:** Draft emails, reports, proposals, and other entity-facing content
- **Research specialist:** Background research, competitive analysis, data gathering

**Layman-friendly implementation:**
- User says "How is Acme performing?" -- triggers analytics specialist
- User says "Draft an email to Acme about our progress" -- triggers communication specialist using voice profile
- User says "Research Acme's competitors" -- triggers research specialist

---

## Category 6: UX and Accessibility Improvements

These are about making the system more usable for non-technical users.

### 6.1 Setup Still Requires Git Clone

**Current state:** README says `git clone https://github.com/... && cd anymanage && claude`. While simpler than many dev tools, `git clone` is still a command-line operation that intimidates non-technical users.

**Improvement ideas:**
- Provide a "Download ZIP" option with clear instructions (GitHub has this built in)
- Create a simple installer script or even a web-based setup wizard that generates the initial repo
- Better yet: a GitHub template repository that users can "Use this template" with one click from the browser, then clone with GitHub Desktop (a GUI)
- Include screenshots in the getting-started guide showing exactly what each step looks like

---

### 6.2 No Status Visualization

**Current state:** Everything is text-based Markdown. Status is conveyed as checkbox lists and tables.

**Improvement ideas:**
- Generate ASCII/text-based progress bars for entity completion: `[========--] 80%`
- Use emoji sparingly for quick visual scanning (if user opts in): checkmarks, warning signs, etc.
- Consider a companion web viewer (a simple static site generator that renders the Markdown into a dashboard) -- this would be a longer-term enhancement

---

### 6.3 No Report Export

**Current state:** All output stays in Markdown files. No way to generate a polished report to share with stakeholders.

**Improvement ideas:**
- Add a "Generate report for [Entity]" skill that compiles profile + roadmap + recent changelog into a single formatted document
- Support Markdown-to-PDF export (using a tool like pandoc, but the AI handles the command invisibly)
- Email-ready summaries: "Draft a status update email for Acme"

---

### 6.4 No Guided Discovery

**Current state:** Users can say "What skills do you have?" but there's no contextual discovery. The system doesn't proactively suggest what to do next.

**Improvement ideas:**
- After onboarding an entity: "Now you can add details, process notes, or run an assessment. What would you like?"
- When entity has been idle: "It's been 2 weeks since you worked on Acme. Want to check their status?"
- After assessment: "Assessment complete. Ready to develop a strategy based on these findings?"
- The vision describes this as "progressive depth" -- start simple, grow as needed

---

## Priority Ranking

If implementing these improvements, I'd suggest this order based on impact and dependencies:

### Tier 1: Foundation (enables everything else)
1. **YAML Frontmatter** (1.3) -- Required for cross-entity queries, state machine, validation
2. **DECISIONS.md template** (1.1) -- Low effort, high value for institutional memory
3. **CHANGELOG.md template** (1.2) -- Low effort, auto-populated
4. **Entity lifecycle/state machine** (2.1) -- Structures all future work

### Tier 2: Proactive Intelligence
5. **Recurring task system** (2.2) -- Makes the system proactive instead of reactive
6. **Automated dashboard rollup** (2.3) -- Dynamic status instead of stale file
7. **Validation skill** (5.2) -- Safety net for data integrity
8. **Enhanced weekly review** (5.1) -- Biggest "wow" factor for regular users

### Tier 3: Knowledge & Depth
9. **Knowledge base** (3.1) -- Enables learning across entities
10. **Cross-entity querying** (4.1) -- Powerful once frontmatter exists
11. **Team/ops infrastructure** (3.3) -- Multi-user readiness
12. **Confidence indicators** (4.3) -- Quality awareness

### Tier 4: Growth & Polish
13. **Missing specialist agents** (5.3) -- Analytics, communication, research
14. **Multi-user awareness** (4.2) -- Role-based personalization
15. **Knowledge graph connections** (3.2) -- Methodology traceability
16. **Cross-entity intelligence** (4.4) -- Pattern recognition

### Tier 5: UX Enhancement
17. **Easier setup** (6.1) -- Lower barrier to entry
18. **Status visualization** (6.2) -- Quick visual scanning
19. **Report export** (6.3) -- Stakeholder communication
20. **Guided discovery** (6.4) -- Progressive onboarding

---

## Layman Design Principle (Cross-Cutting)

Every feature above must follow one rule: **the user never sees the implementation.**

- YAML frontmatter? User says "tag Acme with local-seo." AI manages the YAML.
- Recurring tasks? AI reminds the user at session start. User never edits RECURRING.md.
- Validation? AI runs checks silently and says "Found 2 things to fix."
- Cross-entity queries? User asks "Which clients need attention?" AI does the grep.
- Knowledge base? AI says "This looks like what worked for Beta. Apply it?" User says yes.
- State machine? AI says "Assessment is done. Ready to move to Planning?" User says yes.

The user's mental model should always be: "I'm talking to a smart assistant that handles everything." Not: "I'm interacting with a file system that happens to have an AI layer."

---

*Analysis prepared: 2026-02-09*
