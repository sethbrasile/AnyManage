# Features Research: AI-Assisted Project Management Systems

**Research Date:** 2026-01-24
**Reference Implementation:** `/Users/seth/Documents/GitHub/ppmc` (Prickly Pear Marketing Co)
**Target Users:** Non-technical project managers (email/spreadsheet proficiency, no command line)

---

## Reference Implementation Analysis (../ppmc)

### Core Architecture Features

**Markdown-Based Repository Structure**
- All data stored in plain text Markdown files (.md)
- Hierarchical folder organization by entity (clients, agency_ops, templates, shared_resources)
- Git-compatible (version control ready, though not currently initialized)
- Human-readable, AI-parseable format
- Works offline, platform-agnostic

**Entity-Based Organization**
- Primary entity type: Clients (in `/clients/[Client Name]/`)
- Standard subfolder structure per entity:
  - `planning/` - Strategic planning documents
  - `strategy/` - Marketing strategies, positioning
  - `execution/` - Campaign execution materials
  - `tracking/` - Performance monitoring
  - `current_state/` - Audits, assessments, gap analysis
  - `assets/` - Files, images, documents
  - `subagent_logs/` - AI agent activity logs
  - `notes/` - Raw meeting notes, brain dumps
  - `automations/` - Workflow documentation (Mermaid diagrams)

**Template System**
- Reusable document templates in `/templates/`:
  - CLIENT_PROFILE_TEMPLATE.md
  - PROJECT_ROADMAP_TEMPLATE.md
  - MARKETING_PLAN_TEMPLATE.md
  - CAMPAIGN_BRIEF_TEMPLATE.md
  - KPI_DASHBOARD_TEMPLATE.md
  - MONTHLY_REPORT_TEMPLATE.md
  - COMPETITIVE_ANALYSIS_TEMPLATE.md
  - AUDIENCE_PERSONA_TEMPLATE.md
  - PARTNER_PROFILE_TEMPLATE.md
  - MARKETING_AUDIT_CHECKLIST.md
- Templates copied to entity folders and customized
- Ensures consistency across all entities

**Shared Resources Knowledge Base**
- Centralized playbooks and best practices:
  - MARKETING_FRAMEWORKS.md
  - CHANNEL_BEST_PRACTICES.md
  - CONTENT_GUIDELINES.md
  - LOCAL_SEO_PLAYBOOK.md
  - FULL_MARKETING_PLAN_PLAYBOOK.md
- Domain-specific knowledge accessible to all entities
- Can be generalized for other industries

### AI Agent Integration Features

**Multi-Agent Architecture**
- Main project manager agent (coordinates all work)
- Specialized sub-agents with domain expertise:
  - Assessment Agent (audits, competitive analysis)
  - Strategy Agent (positioning, planning)
  - Campaign Planning Agent
  - Analytics Agent (KPIs, reporting)
- Sub-agents defined in `.claude/agents/` and `.opencode/skills/`
- Model selection per agent (Opus for strategy, Sonnet for execution)

**Agent Instruction Files**
- `CLAUDE.md` - Primary agent instructions for Claude Code
- `OPENCODE.md` - Instructions for OpenCode
- `AGENTS.md` - Multi-agent coordination logic
- Agent personas, responsibilities, workflows clearly defined
- Session protocol (what to read on startup, what to check)

**Skills System (Reusable Agent Behaviors)**
- Custom skills in `.claude/skills/` and `.opencode/skills/`:
  - `trello-manager` - Sync with Trello board (operational truth)
  - `process-client-notes` - Transform raw notes into action items
- Skills have standardized SKILL.md format
- Trigger phrases for skill activation
- Cross-agent compatibility

**Agent Context Management**
- Session Protocol: Agent reads ROADMAP.md → identifies active work → reads entity roadmap → checks current_state
- Always delegates to specialist sub-agents when available
- Logs work in `subagent_logs/` for traceability
- Clear handoff protocols between agents

### Project Management Features

**Task Tracking System**
- Simple checkbox syntax in PROJECT_ROADMAP.md:
  - `[ ]` = Todo
  - `[-]` = In Progress (with start date)
  - `[x]` = Completed (with completion date)
- Date format: MM/DD/YY or YYYY-MM-DD
- Tasks organized by priority (High/Medium/Low)
- Tasks grouped by phase or timeframe (Next 30 Days, 30-90 Days, 90+ Days)
- Recently Completed section for visibility

**Master Roadmap Dashboard**
- Root-level ROADMAP.md shows all active entities
- High/Medium/Low priority segmentation
- Current phase tracking per entity
- Quarterly strategic work section
- Operations tasks separate from entity work

**Note Processing Workflow**
- Raw notes dumped to `/notes/YYYY-MM-DD_[topic].md`
- AI processes notes to extract:
  - Action items → PROJECT_ROADMAP.md
  - Client insights → CLIENT_PROFILE.md
  - Strategy updates → strategy/ folder
- Notes marked "Processed by Claude on [Date]" when complete
- No formatting required from user (brain dump friendly)

**Profile Management**
- Entity profiles (CLIENT_PROFILE.md) for context
- Partner/team profiles (agency_ops/team_profiles/)
- Incremental fact addition ("Add [fact] to [Name]'s profile")
- Profile curation guide with multiple input methods (direct edit, ChatGPT interview, brain dump)
- Additive-only updates (never delete, only clarify)
- Update logs track all changes

### Automation & Integration Features

**External Tool Integration**
- Trello MCP Server for Kanban sync
- Trello = operational truth (status)
- Repo = strategic truth (planning)
- Pull before plan mandate
- Bi-directional sync (pull status, push new work)
- Card naming conventions ([Client Initials]: Task Name)

**Automation Scripts**
- Makefile for common operations:
  - `make client name="Client Name"` - Onboard new entity
  - `make note client="Name" topic="Topic"` - Create timestamped note
  - `make list` - List all entities
- Shell scripts in `/scripts/`:
  - `new_client.sh` - Creates folder structure, copies templates, updates master roadmap
  - `new_note.sh` - Creates dated note file
- Cross-platform compatibility (bash, perl for sed differences)

**Workflow Documentation**
- Mermaid diagrams for complex automations
- Visual flowcharts in automation docs
- Example: Wedding booking flow with conditional logic, parallel flows, timing

### Knowledge Management Features

**Templated Documents**
- KPI Dashboard with executive summary, metrics tables, trend indicators
- Campaign Briefs with objectives, audience, timeline, budget
- Monthly reports with performance highlights
- Marketing plans with positioning, messaging, channel strategy

**Reference Architecture**
- Links between documents (CLIENT_PROFILE.md referenced from PROJECT_ROADMAP.md)
- Avoid duplication (reference, don't repeat)
- Consistent cross-linking
- Relative paths for portability

**Obsidian Compatibility**
- .obsidian/ folder for vault configuration
- Works as Obsidian knowledge base
- Graph view, backlinks supported
- Non-technical users can use Obsidian GUI instead of CLI

### User Experience Features

**Natural Language Interface**
- User gives plain English commands:
  - "What is the current status of [Client]?"
  - "Process notes for [Client]"
  - "Draft a campaign brief for [Client]"
  - "Add [fact] to [Name]'s profile"
- AI interprets intent and executes

**Multiple AI Agent Options**
- Works with Claude Code (expensive but powerful)
- Works with OpenCode (cheaper, Gemini Flash compatible)
- Agent-agnostic design via instruction files
- Permission system in `.claude/settings.local.json`

**Non-Technical Onboarding**
- README.md written for marketers, not developers
- "Usage Guide (For Marketing Team)" section
- Explains concepts in business terms
- Makefile abstracts away command line
- Brain dump → structured output workflow

**Change Management**
- Document change requests with timestamps
- Update roadmaps with new priorities
- Communicate affected dates
- Archive completed work to "Recently Completed"

---

## Table Stakes (Must Have)

### 1. Structured Folder Hierarchy
**Why Essential:** Non-technical users need predictable organization. Without clear structure, information becomes unfindable. Standard folders per entity ensure consistency and AI navigability.

### 2. Markdown-Based Files
**Why Essential:** Human-readable, AI-parseable, version-controllable, platform-agnostic. Works with any text editor. No proprietary format lock-in. Survives technology changes.

### 3. Template System
**Why Essential:** Ensures consistency across entities. Users shouldn't have to "design" document structure each time. Templates encode best practices and required fields.

### 4. Entity Profile + Roadmap
**Why Essential:** Core two documents for context and task tracking. Profile = who/what, Roadmap = work to do. Minimum viable PM system.

### 5. Simple Task Tracking (Checkboxes)
**Why Essential:** Users understand `[ ]` / `[-]` / `[x]` instantly. No learning curve. Plain text = grep-able, AI-readable, future-proof.

### 6. Natural Language Agent Interface
**Why Essential:** Target users don't use command line. Must be able to say "Process notes for ClientX" instead of running scripts.

### 7. Agent Instruction Files (CLAUDE.md / OPENCODE.md)
**Why Essential:** AI needs to know its role, responsibilities, folder structure, protocols. Without this, every session starts from zero context.

### 8. Note Processing Workflow
**Why Essential:** Users think in brain dumps and meeting notes. Must transform unstructured → structured without manual labor.

### 9. Master Roadmap Dashboard
**Why Essential:** Users need single-page view of all active work. Without this, entities become siloed and invisible.

### 10. Date Stamping & Status Tracking
**Why Essential:** PM requires timeline visibility. When did work start? When completed? Status changes must be timestamped for accountability.

---

## Differentiators (Competitive Advantage)

### 1. Multi-Agent Specialist Architecture
**Why Valuable:** Generic AI assistants lack domain depth. Specialist agents (Assessment, Strategy, Analytics) provide expert-level output. Mirrors real consulting team structure. Scales expertise without hiring.

### 2. Skills System (Reusable Agent Behaviors)
**Why Valuable:** Codifies complex workflows into callable skills. "Process-client-notes" skill is reusable across sessions/users. Skills = institutional memory of "how we do things."

### 3. Bi-Directional External Tool Sync
**Why Valuable:** Most AI PM tools are islands. Trello sync lets teams use familiar Kanban while AI maintains strategic context. Operational vs. strategic truth separation is sophisticated.

### 4. Domain-Specific Playbooks
**Why Valuable:** Shared knowledge base (MARKETING_FRAMEWORKS.md, LOCAL_SEO_PLAYBOOK.md) turns AI into domain expert. Not just task manager—strategic advisor. Generalizable to other industries.

### 5. Incremental Profile Building
**Why Valuable:** "Add [fact] to [Name]'s profile" allows lazy, additive knowledge capture. No need to fill out entire form. Knowledge accumulates over time. Low friction.

### 6. Automation Workflow Documentation (Mermaid)
**Why Valuable:** Most PM tools don't document complex automation logic. Mermaid flowcharts make automation transparent, debuggable, transferable. Supports sophisticated process design.

### 7. Multi-Modal Input Methods
**Why Valuable:** Profile curation guide offers 3 paths: direct edit, ChatGPT interview, voice memo brain dump. Meets users where they are. Reduces activation energy.

### 8. Subagent Logging
**Why Valuable:** Transparency into what AI did, when, why. Traceability for audits. Enables debugging and improvement. Most AI tools are black boxes.

### 9. Session Protocol (Context Restoration)
**Why Valuable:** AI knows exactly what to read on startup. No "what was I working on?" friction. Instant context restoration across sessions.

### 10. Agent-Agnostic Design
**Why Valuable:** Not locked into Claude Code or OpenCode. Same repo works with multiple AI agents. Future-proof. Cost optimization (use cheaper agents when appropriate).

---

## Anti-Features (Deliberately Avoid)

### 1. Database Dependency
**Why Avoid:** Introduces complexity, failure modes, vendor lock-in. Non-technical users can't debug database issues. Markdown files = zero dependencies, always readable.

### 2. GUI Application
**Why Avoid:** Requires maintenance, platform-specific builds, update distribution. Markdown + AI agent = works with any text editor, terminal, or Obsidian. No app to install/break.

### 3. Real-Time Collaboration Conflicts
**Why Avoid:** CRDTs, operational transforms add massive complexity. Target users work asynchronously. Git merge conflicts on Markdown are tractable; live collab sync is not.

### 4. Custom Query Language
**Why Avoid:** Learning curve. Natural language queries via AI eliminate need for DSL. "Show me all tasks for ClientX" > `SELECT * FROM tasks WHERE client='ClientX'`.

### 5. Built-In Time Tracking / Billing
**Why Avoid:** Scope creep. Users already have billing tools. PM template should integrate (via notes processing) not replace. Stay focused on context + task management.

### 6. Notifications / Alerting System
**Why Avoid:** Requires daemon, permissions, notification APIs. Adds complexity. Users check roadmap when they want updates—no need for interruptions.

### 7. Mobile App
**Why Avoid:** Fragmentation, maintenance burden. Mobile users can access Markdown via Obsidian mobile, Git clients, or text editors. Don't reinvent the wheel.

### 8. AI Model Training / Fine-Tuning
**Why Avoid:** Requires ML infrastructure, data pipelines, maintenance. Prompt engineering via instruction files (CLAUDE.md) achieves 90% of benefits with 1% of complexity.

### 9. Built-In File Storage / CDN
**Why Avoid:** Storage costs, scaling issues, compliance. Assets live in `/assets/` folder. Users sync via Git, Dropbox, or whatever they already use.

### 10. Calendar Integration
**Why Avoid:** OAuth flows, API maintenance, calendar provider fragmentation. Users can paste calendar info into notes; AI processes it. Integration tax too high for benefit.

---

## Complexity Notes

### Low Complexity (1-2 days implementation)
- **Basic folder structure** - mkdir, copy templates
- **Simple task tracking** - checkbox regex parsing
- **Agent instruction files** - write CLAUDE.md with protocols
- **Note creation scripts** - bash script with date stamping
- **Master roadmap** - single markdown file with links
- **Template library** - copy reference templates, generalize placeholders

### Medium Complexity (3-7 days implementation)
- **Note processing workflow** - AI parsing notes → extract tasks → update multiple docs
- **Profile incremental updates** - "Add fact to profile" command with update logging
- **Skills system** - SKILL.md format, skill discovery/invocation
- **Multi-agent coordination** - delegation logic, handoff protocols
- **Automation documentation** - Mermaid diagram generation
- **Cross-document reference integrity** - ensure links don't break

### High Complexity (1-2 weeks implementation)
- **External tool sync (Trello, etc)** - API integration, conflict resolution, bi-directional sync
- **Subagent logging & traceability** - structured logging, log parsing, activity timeline
- **Session protocol automation** - AI self-initializes context on startup
- **Domain playbook integration** - AI references playbooks contextually during work
- **Multi-agent architecture** - specialist agents with different models, coordination layer
- **Migration tooling** - Import from other PM systems (Asana, Jira, etc)

### Very High Complexity (2+ weeks implementation)
- **Semantic search across all entities** - embedding generation, vector DB (if needed)
- **Automated workflow execution** - Mermaid flowcharts → actual automation runners
- **AI-generated templates** - Dynamic template creation based on domain analysis
- **Cross-entity dependency tracking** - Task A in ClientX blocks Task B in ClientY
- **Version control integration** - Git commits, branches, PRs via AI
- **Custom MCP servers** - Build domain-specific Model Context Protocol servers

---

## Dependencies

### Feature Dependency Chain

**Tier 1 (Foundation - No Dependencies)**
- Folder structure
- Markdown files
- Templates
- Basic agent instruction file

**Tier 2 (Requires Tier 1)**
- Task tracking (requires folder structure, markdown)
- Entity profiles (requires templates, folder structure)
- Master roadmap (requires entity folders, roadmaps)
- Note creation scripts (requires folder structure)

**Tier 3 (Requires Tier 1-2)**
- Note processing (requires task tracking, profiles, agent instructions)
- Profile incremental updates (requires profiles, agent instructions)
- Session protocol (requires master roadmap, entity structure, agent instructions)
- Skills system (requires agent instructions)

**Tier 4 (Requires Tier 1-3)**
- Multi-agent coordination (requires skills system, agent instructions, session protocol)
- Subagent logging (requires multi-agent, folder structure)
- Domain playbooks (requires skills system, note processing, profiles)

**Tier 5 (Requires Tier 1-4)**
- External tool sync (requires task tracking, multi-agent, skills)
- Workflow automation execution (requires Mermaid docs, skills system, multi-agent)
- Migration tooling (requires all foundational features)

### Critical Path for MVP

1. **Folder structure + Templates** → Users can create entities manually
2. **Agent instruction file** → AI knows what to do
3. **Task tracking in roadmaps** → Basic PM functionality
4. **Note processing** → Unstructured → structured workflow
5. **Master roadmap** → Visibility across entities

Everything else is enhancement, not essential for launch.

### Optional Dependencies (Enhancement Only)

- **Obsidian compatibility** - Enhances UI but not required
- **Git integration** - Adds version control but markdown works without it
- **Trello sync** - External tool bridge, not core functionality
- **Make/script automation** - Convenience layer, AI can do same work
- **Mermaid diagrams** - Nice-to-have visualization, not core PM

---

## Generalization Strategy (Marketing → Generic PM)

### Domain-Specific Elements to Abstract

**Marketing-Specific**
- CLIENT_PROFILE.md → ENTITY_PROFILE.md
- campaign, strategy, competitive analysis templates → configurable template sets
- MARKETING_FRAMEWORKS.md → DOMAIN_PLAYBOOKS.md
- Assessment/Strategy/Analytics agents → configurable specialist roles

**Generalizable Patterns**
- Entity-based organization (clients, projects, products, initiatives)
- Profile + Roadmap core structure
- Note processing workflow
- Task tracking syntax
- Multi-agent coordination
- Skills system
- External tool sync framework

### Configuration Requirements

**Template for template generation:**
- "What entity type are you managing?" (Clients, Projects, Products, Campaigns, etc.)
- "What specialist roles do you need?" (Strategy, Technical, Financial, Operations, etc.)
- "What document types are common?" → Generate templates
- "What external tools do you use?" → Configure sync

**Domain playbook scaffolding:**
- "What frameworks does your industry use?"
- "What are your standard operating procedures?"
- "What compliance/regulatory requirements exist?"
- "What metrics matter in your domain?"

### MVP Generalization Approach

1. **Extract marketing-specific terminology** into config file
2. **Parameterize templates** with entity type placeholders
3. **Make agent roles configurable** (role name, responsibilities, model)
4. **Provide template template** (meta-template for creating domain templates)
5. **Document customization process** in README
6. **Include reference implementations** (marketing, software development, etc.)

---

## Risk Assessment

### Low Risk
- Folder structure (standard practice)
- Markdown files (battle-tested format)
- Task checkboxes (universally understood)
- Agent instruction files (text files, easy to edit)

### Medium Risk
- **Multi-agent coordination** - Can get complex, hard to debug if agents conflict
- **Note processing accuracy** - AI might mis-parse notes, create wrong tasks
- **External tool sync** - API changes break integration, conflict resolution bugs
- **Profile incremental updates** - Potential for corrupting existing data

### High Risk
- **Agent context management** - AI might miss critical context, make wrong decisions
- **Cross-document integrity** - Links break, references go stale
- **Skill system complexity** - Skills might not compose well, hard to maintain
- **Workflow automation execution** - Mermaid → actual execution is risky (destructive actions)

### Mitigation Strategies
- **Version control mandatory** - Every change tracked, rollback possible
- **Dry-run mode for AI** - Preview changes before applying
- **Explicit approval gates** - AI asks before destructive operations
- **Subagent logging** - Audit trail for all AI actions
- **Template validation** - Ensure required fields present before use
- **Regular backups** - Automated snapshots of entire repo

---

## Competitive Landscape (Inferred)

### Traditional PM Tools (Asana, Jira, Monday.com)
- **Their Strengths:** Real-time collab, rich UI, integrations, enterprise features
- **Their Weaknesses:** Expensive, complex, not AI-native, vendor lock-in, requires training
- **Our Advantage:** Simpler, AI-native, markdown-based (portable), cheaper, no learning curve

### AI PM Assistants (Motion, Reclaim, Notion AI)
- **Their Strengths:** Calendar integration, smart scheduling, UI polish
- **Their Weaknesses:** Generic (not domain-aware), no multi-agent architecture, limited customization
- **Our Advantage:** Domain playbooks, specialist agents, fully customizable, open format

### Code-Based PM (GitHub Projects, Linear)
- **Their Strengths:** Developer-friendly, git integration, issue tracking
- **Their Weaknesses:** Technical users only, no non-dev domain knowledge, limited templates
- **Our Advantage:** Non-technical friendly, natural language interface, domain playbooks, richer templates

### Note-Taking Tools (Obsidian, Notion, Roam)
- **Their Strengths:** Flexible structure, linking, knowledge management
- **Their Weaknesses:** Not PM-focused, no task automation, no AI agent integration
- **Our Advantage:** PM-specific workflows, AI automation, task tracking, specialist agents

---

## Success Metrics (How to Measure Feature Success)

### User Adoption Metrics
- Time from repo clone to first entity created (Target: <10 min)
- Percentage of users who successfully process their first note (Target: >80%)
- Number of entities managed per user (indicates scaling)
- Active usage days per week (indicates stickiness)

### AI Effectiveness Metrics
- Note processing accuracy (% of correctly extracted tasks)
- Agent decision correctness (user overrides per session)
- Session protocol success rate (does AI load right context?)
- Skill invocation success rate (does skill execute as intended?)

### System Health Metrics
- Cross-document link integrity (% of broken links)
- Template compliance (% of entities using standard structure)
- Subagent log completeness (is all AI work logged?)
- External tool sync conflicts (how often does Trello sync fail?)

### Business Outcome Metrics (For Domain Users)
- **Marketing example:** Client projects completed on time (%)
- **Marketing example:** Campaign launch velocity (days from brief to launch)
- **Generic:** Entity satisfaction scores (if tracked)
- **Generic:** Time saved vs. manual PM (hours per week)

---

## Future Research Questions

1. **What licensing model works for this?** Open-source core + paid playbooks? SaaS hosting? Enterprise support?
2. **How to handle multi-user repos?** Git-based collaboration? Separate branches per user?
3. **Should we build a web UI anyway?** For less technical users? Or is Obsidian enough?
4. **What about compliance/security?** HIPAA, SOC2 for enterprise? Encryption at rest?
5. **How to monetize domain playbooks?** Marketplace for industry-specific templates/skills?
6. **Can workflow automation be safe?** Sandboxing? Approval queues? Dry-run always?
7. **What about scale?** How many entities before performance degrades? Need database?
8. **How to handle migrations?** Import from Asana, Jira, etc.? Export to them?
9. **What about mobile experience?** Is Obsidian mobile enough? Or build PWA?
10. **How to version control agent instructions?** Git tag for instruction updates? Changelog?

---

## Appendix: File Structure Map (Reference Implementation)

```
ppmc/
├── CLAUDE.md                    # Agent instructions for Claude Code
├── OPENCODE.md                  # Agent instructions for OpenCode
├── AGENTS.md                    # Multi-agent coordination
├── README.md                    # User-facing documentation
├── ROADMAP.md                   # Master dashboard
├── Makefile                     # Automation commands
├── .claude/
│   ├── agents/                  # Specialist agent definitions
│   │   ├── marketing-assessment-specialist.md
│   │   ├── marketing-strategy-specialist.md
│   │   ├── marketing-campaign-planning-specialist.md
│   │   └── marketing-analyst.md
│   ├── skills/                  # Reusable skills
│   │   ├── trello-manager/
│   │   └── process-client-notes/
│   └── settings.local.json      # Permissions config
├── .opencode/
│   ├── skills/                  # OpenCode skills (mirrors Claude)
│   └── package.json             # Plugin dependencies
├── clients/                     # Entity folders
│   └── [Client Name]/
│       ├── CLIENT_PROFILE.md
│       ├── PROJECT_ROADMAP.md
│       ├── planning/
│       ├── strategy/
│       ├── execution/
│       ├── tracking/
│       ├── current_state/
│       ├── assets/
│       ├── automations/
│       ├── subagent_logs/
│       └── notes/
├── templates/                   # Reusable document templates
│   ├── CLIENT_PROFILE_TEMPLATE.md
│   ├── PROJECT_ROADMAP_TEMPLATE.md
│   ├── MARKETING_PLAN_TEMPLATE.md
│   ├── CAMPAIGN_BRIEF_TEMPLATE.md
│   ├── KPI_DASHBOARD_TEMPLATE.md
│   ├── MONTHLY_REPORT_TEMPLATE.md
│   ├── COMPETITIVE_ANALYSIS_TEMPLATE.md
│   ├── AUDIENCE_PERSONA_TEMPLATE.md
│   └── PARTNER_PROFILE_TEMPLATE.md
├── shared_resources/            # Domain knowledge base
│   ├── MARKETING_FRAMEWORKS.md
│   ├── CHANNEL_BEST_PRACTICES.md
│   ├── CONTENT_GUIDELINES.md
│   ├── LOCAL_SEO_PLAYBOOK.md
│   └── FULL_MARKETING_PLAN_PLAYBOOK.md
├── agency_ops/                  # Internal operations
│   ├── CLIENT_ONBOARDING.md
│   ├── TRELLO_SYNC_PLAYBOOK.md
│   ├── TEAM.md
│   ├── PROFILE_CURATION_GUIDE.md
│   └── team_profiles/
└── scripts/                     # Automation scripts
    ├── new_client.sh
    └── new_note.sh
```

---

**End of Research Document**
