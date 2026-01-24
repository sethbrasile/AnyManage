# Architecture Research: AI-Assisted Project Management Template System

## Executive Summary

The reference implementation (/Users/seth/Documents/GitHub/ppmc) is a marketing agency management system that uses local AI coding agents (Claude Code, OpenCode, codex) as project management assistants. The architecture is file-based, human-readable, and designed for non-technical users who interact through natural language with AI agents.

**Core Architectural Principle**: The system is a "semantic operating system" where structured markdown files act as the data layer, AI agents act as the processing layer, and natural language acts as the user interface.

---

## Reference Implementation Structure

### Top-Level Directory Architecture

```
ppmc/
├── .agent/                    # Agent-specific rules (for agents like codex)
│   ├── rules/                 # Session initialization rules
│   └── workflows/             # Automated workflows
├── .claude/                   # Claude Code configuration
│   ├── agents/                # Subagent definitions (assessment, strategy, etc.)
│   ├── skills/                # Claude-specific skills
│   └── settings.local.json    # Permissions and configuration
├── .opencode/                 # OpenCode configuration
│   ├── skills/                # OpenCode-specific skills
│   └── package.json           # Dependencies
├── agency_ops/                # Internal agency SOPs and processes
│   ├── team_profiles/         # Partner/team member profiles
│   ├── CLIENT_ONBOARDING.md   # Onboarding checklist
│   ├── TRELLO_SYNC_PLAYBOOK.md
│   └── PROCESSES.md
├── clients/                   # Entity-specific workspaces (clients in this case)
│   └── [Client Name]/
│       ├── planning/          # Strategic planning documents
│       ├── strategy/          # Marketing strategy docs
│       ├── execution/         # Campaign execution materials
│       ├── tracking/          # Performance data
│       ├── current_state/     # Audit findings, gaps analysis
│       ├── assets/            # Client-specific files
│       ├── subagent_logs/     # Activity logs from specialist agents
│       ├── notes/             # Raw brain dumps and meeting notes
│       ├── CLIENT_PROFILE.md  # Static client information
│       └── PROJECT_ROADMAP.md # Active task tracking
├── shared_resources/          # Knowledge base and playbooks
│   ├── MARKETING_FRAMEWORKS.md
│   ├── LOCAL_SEO_PLAYBOOK.md
│   └── FULL_MARKETING_PLAN_PLAYBOOK.md
├── templates/                 # Reusable document templates
│   ├── CLIENT_PROFILE_TEMPLATE.md
│   ├── PROJECT_ROADMAP_TEMPLATE.md
│   └── [Various marketing templates]
├── scripts/                   # Automation helpers
│   ├── new_client.sh          # Client onboarding automation
│   └── new_note.sh            # Note file creation
├── ROADMAP.md                 # Master overview of all active work
├── CLAUDE.md                  # Primary agent instruction manual
├── AGENTS.md                  # Agent integration pointer
├── OPENCODE.md                # Cross-agent compatibility instructions
├── README.md                  # User-facing documentation
└── Makefile                   # Command shortcuts
```

### Client (Entity) Structure Deep Dive

Each client folder follows a standardized structure:

```
[Client Name]/
├── CLIENT_PROFILE.md          # Static reference data (company info, goals, contacts)
├── PROJECT_ROADMAP.md         # Dynamic task tracking with checkbox statuses
├── planning/                  # Future-focused strategic documents
├── strategy/                  # Positioning, messaging, channel strategy
├── execution/                 # Campaign briefs, content calendars
├── tracking/                  # KPI dashboards, monthly reports
├── current_state/             # Audit findings, gaps analysis, strengths
├── assets/                    # Files, images, content deliverables
├── subagent_logs/             # Timestamped work logs from specialist agents
└── notes/                     # Unprocessed raw information
```

---

## Component Boundaries

### 1. Core Instruction System

**Location**: Root-level markdown files (CLAUDE.md, AGENTS.md, OPENCODE.md)

**Purpose**: Define agent persona, protocols, workflows, and responsibilities

**Key Files**:
- **CLAUDE.md**: Comprehensive instruction manual for the AI agent
  - Session protocol (what to read on startup)
  - Core responsibilities (project coordination, assessment, planning, etc.)
  - Special protocols (note processing, client onboarding, partner profile updates)
  - Subagent coordination rules
  - Working style and standards

- **AGENTS.md**: Entry point that redirects to CLAUDE.md
  - Ensures all AI agents (Claude Code, OpenCode, etc.) follow the same core instructions
  - Cross-agent compatibility layer

- **OPENCODE.md**: OpenCode-specific initialization (currently mirrors AGENTS.md)

**Architecture Pattern**: Single source of truth principle - CLAUDE.md is the canonical instruction set

---

### 2. Entity Management System

**Location**: `clients/` directory (generalizable to products, projects, etc.)

**Purpose**: Isolate all work related to a specific entity in a dedicated workspace

**Core Documents**:

1. **CLIENT_PROFILE.md** (Static Reference Data)
   - Company information
   - Product/service overview
   - Business goals
   - Current marketing state
   - Contact information
   - Engagement terms
   - Competitive landscape summary

2. **PROJECT_ROADMAP.md** (Dynamic Task Tracking)
   - Current phase indicator
   - Prioritized task lists (High/Medium/Low priority)
   - Task status tracking using markdown checkboxes:
     - `[ ]` = To Do
     - `[-]` = In Progress (with start date)
     - `[x]` = Completed (with completion date)
   - Reference to CLIENT_PROFILE.md to avoid duplication
   - Recently Completed section for historical tracking

**Design Philosophy**:
- Profile = "What" (unchanging facts)
- Roadmap = "When/Status" (changing state)
- Reference not duplicate (roadmap links to profile)

---

### 3. Agent/Subagent System

**Location**: `.claude/agents/`, `.opencode/skills/`

**Purpose**: Specialized AI personas for different types of work

**Claude Code Subagents** (`.claude/agents/`):

1. **marketing-assessment-specialist.md**
   - Role: Conduct audits, gap analysis, competitive research
   - Model: sonnet (cost-effective for structured analysis)
   - Deliverables: AUDIT_FINDINGS.md, GAPS_ANALYSIS.md, OPPORTUNITIES.md
   - Workflow: Systematic assessment using RACE framework

2. **marketing-strategy-specialist.md**
   - Role: Synthesize findings into actionable strategy
   - Model: opus (high-quality strategic thinking)
   - Deliverables: MARKETING_STRATEGY.md, POSITIONING.md, BRAND_MESSAGING.md
   - Workflow: Review audit → develop strategy → define KPIs

3. **marketing-campaign-planning-specialist.md** (inferred from pattern)
   - Role: Campaign planning and execution coordination

4. **marketing-analyst.md** (inferred from pattern)
   - Role: Performance analysis and reporting

**Subagent Architecture**:
- Each agent is a markdown file with YAML frontmatter defining:
  - `name`: Agent identifier
  - `description`: Capabilities summary
  - `tools`: Available tool set
  - `model`: Preferred AI model (opus vs sonnet)
- Body contains:
  - Role definition
  - Core responsibilities
  - Workflow steps
  - Deliverables checklist
  - Validation questions

**Coordination Pattern**: Main agent reads ROADMAP.md → identifies client work → activates specialist subagent → specialist logs work in `subagent_logs/`

---

### 4. Skill/Protocol System

**Location**: `.claude/skills/`, `.opencode/skills/`

**Purpose**: Reusable, cross-agent procedures for common tasks

**Skills are workflows, not personas** (unlike subagents)

**Implemented Skills**:

1. **trello-manager**
   - File: `.opencode/skills/trello-manager/SKILL.md`
   - Model: gemini-3-flash (cost-effective for API operations)
   - Functions:
     - `pull_status(client)`: Sync Trello → local roadmap
     - `push_new_work(client)`: Create Trello cards from roadmap
     - `update_card_context(card_id)`: Update card details
   - Mappings:
     - Trello "Done" → `[x]`
     - Trello "In Progress" → `[-]`
     - Trello "Ready/To Do" → `[ ]`
   - Safety rules: Never delete local roadmap items; Trello is truth for status

2. **process-client-notes**
   - File: `.opencode/skills/process-client-notes/SKILL.md`
   - Model: gemini-3-flash-preview
   - Workflow:
     1. Ingest raw notes from `notes/` folder
     2. Extract actionable tasks → add to PROJECT_ROADMAP.md
     3. Update CLIENT_PROFILE.md with new information
     4. Refine strategy documents if goals discussed
     5. Mark note as processed with timestamp
   - Handles both client-specific and agency-level notes

**Skill Architecture**:
- SKILL.md format:
  - YAML frontmatter: name, description, model
  - Body: Overview, when to use, protocol steps, mappings, best practices
- Cross-agent compatible (both Claude and OpenCode can use them)
- Model selection per skill allows cost optimization

**Skill vs Subagent**:
- **Subagent** = Specialized persona for domain expertise (e.g., "strategy specialist")
- **Skill** = Procedural workflow for specific tasks (e.g., "sync with Trello")

---

### 5. Template System

**Location**: `templates/`

**Purpose**: Ensure consistency across all entity workspaces

**Template Types**:

1. **Entity Templates**:
   - CLIENT_PROFILE_TEMPLATE.md
   - PROJECT_ROADMAP_TEMPLATE.md

2. **Deliverable Templates**:
   - MARKETING_AUDIT_CHECKLIST.md
   - KPI_DASHBOARD_TEMPLATE.md
   - MONTHLY_REPORT_TEMPLATE.md
   - CAMPAIGN_BRIEF_TEMPLATE.md
   - COMPETITIVE_ANALYSIS_TEMPLATE.md
   - AUDIENCE_PERSONA_TEMPLATE.md

3. **Team Templates**:
   - PARTNER_PROFILE_TEMPLATE.md

**Usage Pattern**:
1. Template file contains placeholders: `[Client Name]`, `[Industry]`, etc.
2. Automation scripts (e.g., `new_client.sh`) copy template → rename → replace placeholders
3. AI agents can also copy templates manually when scripts aren't available

**Design Philosophy**: Templates enforce structural consistency while allowing content flexibility

---

### 6. Integration Layer

**Location**: Multiple integration points

**Purpose**: Connect to external systems without requiring them

**Integration Types**:

1. **Task Management (Trello)**:
   - **Interface**: MCP (Model Context Protocol) server
   - **Configuration**: `.claude/settings.local.json` contains Trello MCP permissions
   - **Sync Protocol**: Defined in `agency_ops/TRELLO_SYNC_PLAYBOOK.md`
   - **Skill**: `trello-manager` implements sync logic
   - **Philosophy**: Trello = operational truth (status), Repo = strategic truth (plans)
   - **Conflict Resolution**: Trello wins for status, repo wins for existence

2. **Web Research**:
   - **Permissions**: WebFetch and WebSearch enabled in settings.local.json
   - **Use Cases**: Competitive research, documentation lookup

3. **Automation Scripts**:
   - **Interface**: Makefile + bash scripts
   - **Commands**:
     - `make client name="Client Name"` → Runs `scripts/new_client.sh`
     - `make note client="Name" topic="Topic"` → Runs `scripts/new_note.sh`
     - `make list` → Lists all active clients
   - **Fallback**: AI agents can perform these tasks manually if scripts unavailable

**Integration Philosophy**:
- **Standalone-first**: System works without any integrations
- **Additive integrations**: External systems enhance but don't replace core functionality
- **Sync, don't migrate**: Keep data in both places with clear truth sources
- **AI-mediated**: Agent handles all integration logic via skills

---

## Data Flow

### 1. Session Initialization Flow

```
User starts AI agent
    ↓
Agent reads initialization rules (.agent/rules/agent-context.md)
    ↓
Agent reads CLAUDE.md (loads persona & protocols)
    ↓
Agent reads ROADMAP.md (identifies active work)
    ↓
Agent identifies in-progress clients ([-] status)
    ↓
Agent reads relevant CLIENT_PROFILE.md and PROJECT_ROADMAP.md
    ↓
Agent reports readiness + current context
```

**Key Pattern**: Every session starts with context loading, ensuring continuity

---

### 2. Client Onboarding Flow

```
User: "Onboard new client [Name]"
    ↓
Agent chooses method:
├─ PREFERRED: Run `make client name="[Name]"`
│   ├─ scripts/new_client.sh executes
│   ├─ Creates folder structure (planning/, strategy/, notes/, etc.)
│   ├─ Copies templates → renames with client name
│   ├─ Replaces placeholders ([Client Name] → actual name)
│   └─ Updates ROADMAP.md (adds client as [-] In Progress)
│
└─ FALLBACK: Manual creation
    ├─ Create /clients/[Name]/ + subfolders
    ├─ Copy CLIENT_PROFILE_TEMPLATE.md → CLIENT_PROFILE.md
    ├─ Copy PROJECT_ROADMAP_TEMPLATE.md → PROJECT_ROADMAP.md
    ├─ Update templates with client info
    └─ Update ROADMAP.md
    ↓
Agent asks: "Ready to start assessment?"
    ↓
User confirms
    ↓
Agent activates assessment-specialist subagent
    ↓
Subagent conducts audit → creates AUDIT_FINDINGS.md
```

**Key Pattern**: Automation-first with manual fallback; immediate activation of relevant subagent

---

### 3. Note Processing Flow

```
User dumps raw notes (email, meeting transcript, brain dump)
    ↓
User: "Process notes for [Client]"
    ↓
Agent activates process-client-notes skill
    ↓
Skill workflow:
├─ 1. Read notes file (clients/[Client]/notes/YYYY-MM-DD_topic.md)
├─ 2. Extract actionable tasks
├─ 3. Add tasks to PROJECT_ROADMAP.md as [ ] (To Do)
├─ 4. Identify new client information
├─ 5. Update CLIENT_PROFILE.md with new facts
├─ 6. Identify strategic insights
├─ 7. Update strategy/ documents if relevant
└─ 8. Mark note as processed (append timestamp)
    ↓
Agent reports: "Added 5 tasks, updated client profile with [details]"
    ↓
Optional: User asks to sync with Trello
    ↓
Agent activates trello-manager skill
    ↓
Skill creates Trello cards from new roadmap tasks
```

**Key Pattern**: Transform unstructured → structured → actionable → synchronized

---

### 4. Task Status Update Flow (with Trello integration)

```
External change in Trello (user moves card to "Done")
    ↓
[Some time passes]
    ↓
Agent starts new session OR user requests update
    ↓
Agent (or user) triggers: trello-manager.pull_status([Client])
    ↓
Skill workflow:
├─ 1. Fetch all Trello cards for client (by label/prefix)
├─ 2. Read current PROJECT_ROADMAP.md
├─ 3. Match cards to roadmap items (fuzzy matching)
├─ 4. Update roadmap statuses:
│   ├─ Trello "Done" → [x]
│   ├─ Trello "In Progress" → [-]
│   └─ Trello "Ready" → [ ]
├─ 5. Add completion dates where applicable
└─ 6. NEVER delete roadmap items (even if missing from Trello)
    ↓
Agent reports: "Synced 12 items, marked 3 as completed"
```

**Key Pattern**: External system (Trello) is operational truth for status; local roadmap is strategic truth for scope

---

### 5. Subagent Workflow (Strategy Development Example)

```
User: "Develop marketing strategy for [Client]"
    ↓
Main agent reads ROADMAP.md → identifies client → reads context
    ↓
Main agent determines: Strategy work needed
    ↓
Main agent activates marketing-strategy-specialist subagent
    ↓
Subagent workflow (defined in .claude/agents/marketing-strategy-specialist.md):
├─ 1. Review clients/[Client]/current_state/AUDIT_FINDINGS.md
├─ 2. Review clients/[Client]/current_state/GAPS_ANALYSIS.md
├─ 3. Review COMPETITIVE_ANALYSIS.md
├─ 4. Develop MARKETING_STRATEGY.md (6-12 month roadmap)
├─ 5. Create POSITIONING.md (UVP + differentiation)
├─ 6. Document BRAND_MESSAGING.md (messaging architecture)
├─ 7. Develop CHANNEL_STRATEGY.md (recommended channels)
├─ 8. Create CUSTOMER_JOURNEY_MAP.md
├─ 9. Define KPI framework
└─ 10. Log work in subagent_logs/strategy_agent_log.md
    ↓
Subagent validates work against checklist
    ↓
Subagent reports completion to main agent
    ↓
Main agent updates PROJECT_ROADMAP.md (marks strategy tasks as [x])
    ↓
Main agent reports to user: "Strategy complete. Deliverables in strategy/ folder."
```

**Key Pattern**: Delegation with accountability - subagent has clear inputs, outputs, and logging requirements

---

### 6. Information Reference Flow

```
Agent needs client information
    ↓
Decision tree:
├─ Static info (company name, industry, goals)?
│   └─ Read CLIENT_PROFILE.md
│
├─ Task status (what's in progress)?
│   └─ Read PROJECT_ROADMAP.md
│
├─ Current state assessment?
│   └─ Read current_state/AUDIT_FINDINGS.md
│
├─ Strategic direction?
│   └─ Read strategy/MARKETING_STRATEGY.md
│
├─ Performance data?
│   └─ Read tracking/KPI_DASHBOARD.md
│
└─ Historical context?
    └─ Read notes/ (timestamped files)
```

**Key Pattern**: Information locality - all client data in client folder; folder structure indicates information type

---

## Build Order: Suggested Component Sequence

### Phase 1: Foundation (Core System)

**Goal**: Establish the file-based data model and core agent instructions

1. **Root Documentation**:
   - [ ] Create README.md (user-facing "what is this system")
   - [ ] Create AGENTS.md (agent entry point)
   - [ ] Create INSTRUCTIONS.md (primary agent instruction manual)
     - Equivalent to CLAUDE.md but generalized
     - Define session protocol
     - Define core responsibilities
     - Define working style

2. **Template System**:
   - [ ] Create templates/ directory
   - [ ] Build ENTITY_PROFILE_TEMPLATE.md
     - Generalize from CLIENT_PROFILE_TEMPLATE.md
     - Include placeholders for: Company info, goals, contacts, context
   - [ ] Build ENTITY_ROADMAP_TEMPLATE.md
     - Generalize from PROJECT_ROADMAP_TEMPLATE.md
     - Include: Current phase, prioritized tasks, checkbox system

3. **Master Roadmap**:
   - [ ] Create ROADMAP.md (master overview)
     - List all active entities with status
     - Progress tracking convention ([ ] / [-] / [x])
     - Section for recently completed work

**Validation**: Can you manually create an entity folder and use it to track work?

---

### Phase 2: Automation Layer

**Goal**: Scripts to reduce manual setup friction

4. **Onboarding Script**:
   - [ ] Create scripts/ directory
   - [ ] Build scripts/new_entity.sh
     - Accept entity name as argument
     - Create standardized folder structure
     - Copy templates → rename → replace placeholders
     - Update ROADMAP.md automatically
   - [ ] Build Makefile (user-friendly commands)
     - `make entity name="Entity Name"`
     - `make note entity="Name" topic="Topic"`
     - `make list`

5. **Note Management**:
   - [ ] Build scripts/new_note.sh
     - Create timestamped note files
     - Follow naming convention: YYYY-MM-DD_topic.md

**Validation**: Can you onboard a new entity with a single command?

---

### Phase 3: Agent Configuration

**Goal**: AI agents can operate the system autonomously

6. **Agent Instructions**:
   - [ ] Refine INSTRUCTIONS.md with protocols:
     - Session initialization (what to read on startup)
     - Entity onboarding workflow
     - Task tracking conventions
     - Note processing protocol
     - Subagent coordination rules

7. **Cross-Agent Compatibility**:
   - [ ] Create .claude/ directory
   - [ ] Create .opencode/ directory
   - [ ] Create .agent/ directory (for codex or other agents)
   - [ ] Add initialization rules to each:
     - .agent/rules/agent-context.md (codex)
     - .claude/agents/README.md (Claude Code)
     - .opencode/README.md (OpenCode)
   - [ ] Each points to INSTRUCTIONS.md as source of truth

**Validation**: Can you start Claude Code, OpenCode, or codex and have them automatically load context?

---

### Phase 4: Skill System

**Goal**: Reusable workflows for common tasks

8. **Note Processing Skill**:
   - [ ] Create .claude/skills/process-notes/
   - [ ] Create .opencode/skills/process-notes/
   - [ ] Build SKILL.md:
     - When to use (user asks to process notes)
     - Protocol: Extract tasks → update profile → update strategy → mark processed
     - Model recommendation (gemini-flash for cost-effectiveness)

9. **Status Reporting Skill**:
   - [ ] Build get-status skill
     - Read ROADMAP.md
     - Identify active entities
     - Read each PROJECT_ROADMAP.md
     - Summarize current state and blockers

**Validation**: Can agent process raw notes into structured tasks?

---

### Phase 5: Subagent System

**Goal**: Specialized AI personas for domain-specific work

10. **Assessment Subagent**:
    - [ ] Create .claude/agents/assessment-specialist.md
    - [ ] Define:
      - Role: Conduct audits and gap analysis
      - Responsibilities: Current state documentation
      - Workflow: Systematic assessment checklist
      - Deliverables: AUDIT_FINDINGS.md, GAPS_ANALYSIS.md
      - Model: sonnet (cost-effective)

11. **Strategy Subagent**:
    - [ ] Create .claude/agents/strategy-specialist.md
    - [ ] Define:
      - Role: Synthesize findings into strategy
      - Responsibilities: Strategic planning documents
      - Workflow: Review audit → develop strategy → define KPIs
      - Deliverables: STRATEGY.md, POSITIONING.md
      - Model: opus (high-quality thinking)

12. **Execution Subagent** (optional but recommended):
    - [ ] Create execution-specialist.md
    - [ ] Define: Campaign planning and coordination

**Validation**: Can main agent delegate specialized work to subagents?

---

### Phase 6: Integration Layer (Optional)

**Goal**: Connect to external systems without requiring them

13. **Task Management Integration** (if desired):
    - [ ] Create integration playbook (e.g., TRELLO_SYNC_PLAYBOOK.md)
    - [ ] Build integration skill (e.g., task-manager skill)
    - [ ] Define sync protocol:
      - External system = operational truth (status)
      - Local system = strategic truth (scope)
      - Conflict resolution rules
    - [ ] Configure permissions (.claude/settings.local.json)

14. **Other Integrations**:
    - [ ] CRM sync (optional)
    - [ ] Calendar integration (optional)
    - [ ] Analytics platforms (optional)

**Validation**: Can system sync with external task management without losing local data?

---

### Phase 7: Knowledge Base

**Goal**: Shared resources and best practices

15. **Playbooks and Frameworks**:
    - [ ] Create shared_resources/ directory
    - [ ] Build domain-specific playbooks
      - Example: FULL_PLAN_PLAYBOOK.md (comprehensive planning guide)
      - Example: DOMAIN_SPECIFIC_PLAYBOOK.md (industry best practices)
    - [ ] Create FRAMEWORKS.md (methodologies and approaches)

16. **Internal Operations**:
    - [ ] Create ops/ directory (equivalent to agency_ops/)
    - [ ] Document:
      - ONBOARDING.md (process for new entities)
      - TEAM.md (team roster)
      - PROCESSES.md (standard operating procedures)

**Validation**: Do agents have reference material for domain-specific work?

---

### Phase 8: Polish and Documentation

**Goal**: Make system accessible to non-technical users

17. **User Documentation**:
    - [ ] Expand README.md with:
      - Usage guide for non-technical users
      - Common commands and workflows
      - How to work with AI agent
      - File structure explanation
    - [ ] Create GETTING_STARTED.md
    - [ ] Create FAQ.md

18. **Configuration Files**:
    - [ ] Add .gitignore (exclude sensitive data, temp files)
    - [ ] Add example configuration files
    - [ ] Document permission settings for each agent type

**Validation**: Can a non-technical user onboard themselves and start working?

---

## Critical Success Factors

### 1. Consistency Over Complexity
- Simple, repeatable folder structures
- Standardized naming conventions (dates as YYYY-MM-DD)
- Checkbox system for status ([ ] / [-] / [x])
- Templates enforce uniformity

### 2. Reference, Don't Duplicate
- CLIENT_PROFILE.md = static facts
- PROJECT_ROADMAP.md = dynamic status
- Roadmap references profile, doesn't copy it
- Single source of truth for each piece of information

### 3. Locality of Information
- All entity data in entity folder
- Folder names indicate content type (planning/, execution/, notes/)
- Agent can find information by structure alone

### 4. Agent Initialization Protocol
- Every session starts by reading core instructions
- Agent loads ROADMAP.md to understand current state
- Ensures continuity across sessions and context resets

### 5. Graceful Degradation
- System works without scripts (manual fallback)
- System works without integrations (standalone-first)
- System works with any AI agent (cross-agent compatible)

### 6. Cost Optimization Through Model Selection
- Subagents specify preferred models
- Assessment work → sonnet (cheaper)
- Strategic work → opus (higher quality)
- Integration work → gemini-flash (cheapest)
- Let users override based on budget

### 7. Accountability Through Logging
- Subagents log work in subagent_logs/
- Notes marked as processed with timestamp
- Roadmap shows start/completion dates
- Audit trail for all changes

---

## Integration Points

### Where Optional Integrations Plug In

#### 1. Task Management Systems (Trello, Asana, Linear, etc.)

**Hook Point**: Skill system (e.g., `.claude/skills/task-sync/`)

**Interface**:
- MCP (Model Context Protocol) for Claude Code
- API clients for other agents
- Skill defines sync protocol and mappings

**Data Flow**:
```
External System (Status Authority)
    ↔ Sync Skill (Bidirectional)
    ↔ PROJECT_ROADMAP.md (Scope Authority)
```

**Configuration**:
- Credentials: `.claude/settings.local.json` or environment variables
- Sync playbook: `ops/TASK_SYNC_PLAYBOOK.md`

**Example Mappings**:
- External "Done" → Local `[x]`
- External "In Progress" → Local `[-]`
- External "To Do" → Local `[ ]`

**Conflict Resolution**:
- External system wins for status
- Local system wins for existence (don't delete local items)

---

#### 2. CRM Systems (HubSpot, Salesforce, etc.)

**Hook Point**: Skill system + ENTITY_PROFILE.md

**Interface**:
- API integration via skill
- Pull contact information, deal stage, notes
- Push activity logs, meeting notes

**Data Flow**:
```
CRM (Contact/Deal Authority)
    → Pull Skill
    → ENTITY_PROFILE.md (enrichment)

Notes Processing
    → Push Skill
    → CRM Activity Log
```

**Use Cases**:
- Auto-populate ENTITY_PROFILE.md from CRM
- Sync meeting notes from CRM to notes/ folder
- Push status updates from roadmap to CRM

---

#### 3. Calendar Systems (Google Calendar, Outlook, etc.)

**Hook Point**: Skill system + ENTITY_ROADMAP.md

**Interface**:
- Calendar API via skill
- Extract meeting notes, action items
- Create calendar events from roadmap deadlines

**Data Flow**:
```
Calendar Event (with notes)
    → Extract Skill
    → notes/YYYY-MM-DD_meeting.md

ROADMAP.md (with due dates)
    → Create Event Skill
    → Calendar Event
```

**Use Cases**:
- Auto-create note files from meeting invites
- Surface upcoming deadlines from roadmap in calendar
- Sync task due dates

---

#### 4. Communication Platforms (Slack, Teams, etc.)

**Hook Point**: Notification system (new skill type)

**Interface**:
- Webhook or API via skill
- Send status updates, reports, alerts

**Data Flow**:
```
ROADMAP.md (status change)
    → Notification Skill
    → Slack/Teams Message

Daily summary
    → Report Skill
    → Slack/Teams Channel
```

**Use Cases**:
- Daily standup reports
- Status change notifications
- Weekly summaries of completed work

---

#### 5. Analytics/BI Tools

**Hook Point**: tracking/ folder + export skills

**Interface**:
- Export structured data from tracking/ folder
- Push to analytics platform or data warehouse

**Data Flow**:
```
tracking/KPI_DASHBOARD.md
    → Export Skill (CSV/JSON)
    → Analytics Platform

Analytics Platform (computed metrics)
    → Import Skill
    → tracking/MONTHLY_REPORT.md
```

**Use Cases**:
- Export performance data for visualization
- Import computed metrics for reporting

---

#### 6. File Storage (Google Drive, Dropbox, etc.)

**Hook Point**: assets/ folder + sync skills

**Interface**:
- Cloud storage API via skill
- Two-way sync of files in assets/

**Data Flow**:
```
assets/ folder
    ↔ Storage Sync Skill
    ↔ Cloud Storage Folder
```

**Use Cases**:
- Backup assets to cloud
- Collaborate on files with external stakeholders
- Version control for deliverables

---

### Integration Design Principles

1. **Standalone First**: System must work without any integration
2. **Additive Only**: Integrations enhance, never replace core functionality
3. **Skill-Based**: All integrations via skills (consistent interface)
4. **Clear Authority**: Define which system is truth for each data type
5. **Sync, Don't Migrate**: Keep data in both places with clear rules
6. **Graceful Failure**: If integration unavailable, system continues to work
7. **Configuration in Settings**: Credentials and permissions in .claude/settings.local.json
8. **Documentation in Playbooks**: Sync protocols in ops/ folder

---

## Generalization Strategy

### From PPMC (Marketing Agency) → Generic Template

#### Entity Abstraction

**Replace**:
- `clients/` → `entities/` (or domain-specific: `products/`, `projects/`)
- CLIENT_PROFILE.md → ENTITY_PROFILE.md
- PROJECT_ROADMAP.md → ENTITY_ROADMAP.md

**Keep**:
- Folder structure (planning/, execution/, notes/, current_state/)
- File naming conventions
- Checkbox system for status

#### Domain-Specific Customization

**Configurable via**:
- Template files (customize fields in ENTITY_PROFILE_TEMPLATE.md)
- Subagent definitions (replace marketing-strategy-specialist with domain expert)
- Shared resources (replace marketing playbooks with domain playbooks)

**Example: Product Management Template**:
- entities/ → products/
- Subagents: product-research-specialist, technical-spec-specialist
- Shared resources: PRODUCT_DEVELOPMENT_PLAYBOOK.md, AGILE_FRAMEWORKS.md

**Example: Real Estate Template**:
- entities/ → properties/
- Subagents: market-analysis-specialist, valuation-specialist
- Shared resources: COMP_ANALYSIS_PLAYBOOK.md, PRICING_STRATEGIES.md

#### Configuration File Approach

Create `CONFIG.md` at root:

```markdown
# Template Configuration

## Entity Concept
- **Entity Type**: [clients | products | projects | properties]
- **Entity Folder Name**: entities/
- **Primary Entity Document**: ENTITY_PROFILE.md
- **Tracking Document**: ENTITY_ROADMAP.md

## Domain Specialization
- **Industry**: [Marketing | Product | Real Estate | ...]
- **Subagent Types**: [assessment, strategy, execution, analytics]
- **Primary Workflows**: [onboarding, planning, execution, reporting]

## Integration Preferences
- **Task Management**: [Trello | Asana | Linear | None]
- **CRM**: [HubSpot | Salesforce | None]
- **Calendar**: [Google | Outlook | None]
```

Agent reads CONFIG.md on initialization → adapts behavior accordingly

---

## Conclusion

The PPMC architecture is a **semantic file-based system** where:
- **Data layer** = Markdown files in standardized structure
- **Processing layer** = AI agents following instruction protocols
- **Interface layer** = Natural language commands

**Key Architectural Innovations**:
1. **Files as database**: Structured markdown replaces traditional databases
2. **Agents as processors**: AI agents replace custom application logic
3. **Natural language as UI**: No custom interface needed
4. **Distributed authority**: Multiple files as authoritative sources (profile vs roadmap)
5. **Skill-based extensibility**: Skills enable integrations without core changes

**Generalization Path**:
- Abstract "client" → "entity"
- Make subagents configurable by domain
- Provide templates for different industries
- Maintain core structure and protocols

**Success Metrics**:
- Non-technical user can onboard new entity in < 5 minutes
- System works across Claude Code, OpenCode, and other AI agents
- No external dependencies required (integrations optional)
- Context survives across sessions and agent types
- Information easily discoverable through folder structure
