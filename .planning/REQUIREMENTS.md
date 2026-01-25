# Requirements: Agent PM

**Defined:** 2026-01-24
**Core Value:** Non-technical PMs can operate a powerful AI project management system without understanding git, command line, or code.

## v1 Requirements

Requirements for initial release. Each maps to roadmap phases.

### Core System

- [x] **CORE-01**: Structured folder hierarchy with semantic naming (entities/, templates/, ops/, .planning/)
- [x] **CORE-02**: Markdown-based files for all data storage (human-readable, AI-parseable)
- [x] **CORE-03**: Template system with reusable document templates
- [x] **CORE-04**: Entity profile + roadmap as core two documents per entity
- [x] **CORE-05**: Checkbox task tracking with `[ ]` / `[-]` / `[x]` status
- [x] **CORE-06**: Master roadmap dashboard showing all entities
- [ ] **CORE-07**: Agent-agnostic instruction pattern (INSTRUCTIONS.md as source of truth)
- [ ] **CORE-08**: Thin agent-specific wrappers (.claude/, .opencode/, .cursorrules)

### Agent Interface

- [ ] **INTF-01**: Natural language + structured command hybrid interface
- [ ] **INTF-02**: Session protocol for auto-context loading on startup
- [ ] **INTF-03**: Skill system for reusable workflow definitions
- [ ] **INTF-04**: Specialist pattern for domain expert personas
- [x] **INTF-05**: Invisible git management (agent commits silently, user never sees git)
- [ ] **INTF-06**: Context restoration across sessions

### Entity Management

- [x] **ENTY-01**: Configurable entity type (user defines: clients, projects, products, etc.)
- [x] **ENTY-02**: Standard subfolder structure per entity (notes/, planning/, tracking/, etc.)
- [x] **ENTY-03**: Incremental profile building ("Add [fact] to [entity] profile")
- [x] **ENTY-04**: Note processing workflow (unstructured notes -> structured tasks/updates)
- [x] **ENTY-05**: Entity onboarding workflow (create folder, templates, update master roadmap)
- [x] **ENTY-06**: Subagent/specialist logging for traceability

### Layered Knowledge System

- [ ] **KNOW-01**: PM base skills layer (project management, note processing, task tracking)
- [ ] **KNOW-02**: Domain knowledge layer (industry-specific skills and playbooks)
- [ ] **KNOW-03**: User/agency knowledge layer (company-specific knowledge and preferences)
- [ ] **KNOW-04**: Entity-specific knowledge layer (client/project-specific context)
- [ ] **KNOW-05**: Skill discovery workflow (find existing skills for domain, then create custom)
- [ ] **KNOW-06**: Skill authoring guide (how to create custom skills)

### Voice Training System

- [ ] **VOIC-01**: Voice training exercise (guided input collection for tone extraction)
- [ ] **VOIC-02**: Example types: emails, chat messages, docs/guides
- [ ] **VOIC-03**: LLM-assisted tone/voice extraction from examples
- [ ] **VOIC-04**: Voice profile storage (persistent tone data)
- [ ] **VOIC-05**: Selectable voice per output type (docs vs emails vs chat)
- [ ] **VOIC-06**: Voice-aware writing (system writes in user's voice with minimal editing)

### Documentation & UX

- [ ] **DOCS-01**: Beginner tutorial (step-by-step for non-technical users)
- [ ] **DOCS-02**: Reference example (cleaned marketing agency with fake data)
- [ ] **DOCS-03**: Customization guide (how to adapt for your industry)
- [ ] **DOCS-04**: Video-ready structure (designed for YouTube walkthrough)
- [ ] **DOCS-05**: Single entry point (README with one clear first step)
- [ ] **DOCS-06**: Troubleshooting guide (common issues and solutions)
- [ ] **DOCS-07**: Skill discovery documentation (how to find/install domain skills)

### Integrations

- [ ] **INTG-01**: Standalone operation (full value without any integrations)
- [ ] **INTG-02**: Integration pattern documentation (how to add integrations)
- [ ] **INTG-03**: Trello integration as example (task management sync)
- [ ] **INTG-04**: Dual source of truth pattern (external = status, local = context)
- [ ] **INTG-05**: Integration skill framework (standard way to build integrations)

## v2 Requirements

Deferred to future release. Tracked but not in current roadmap.

### Advanced Integrations

- **INTG-06**: Gmail integration for email drafting/sending
- **INTG-07**: Calendar integration for scheduling
- **INTG-08**: CRM integration (HubSpot, Salesforce)
- **INTG-09**: Analytics platform integration

### Advanced Features

- **FEAT-01**: Semantic search across all entities (grepai + Ollama)
- **FEAT-02**: Cross-entity dependency tracking
- **FEAT-03**: Migration tooling (import from Asana, Jira, etc.)
- **FEAT-04**: HTML dashboard (GitHub Pages or internal hosting)
- **FEAT-05**: Mobile-friendly access pattern

### Domain Expansions

- **DOMN-01**: Software development example template
- **DOMN-02**: Real estate example template
- **DOMN-03**: Healthcare/medical example template
- **DOMN-04**: Legal/consulting example template

## Out of Scope

Explicitly excluded. Documented to prevent scope creep.

| Feature | Reason |
|---------|--------|
| Database dependency | Complexity, failure modes, vendor lock-in. Markdown = zero dependencies |
| GUI application | Maintenance burden, platform-specific. Agent + editor is sufficient |
| Real-time collaboration | CRDT complexity. Users work async, git handles versioning |
| Built-in time tracking/billing | Scope creep. Users have billing tools already |
| Notifications/alerting system | Daemon/permissions complexity. Users check roadmap when ready |
| Mobile app | Fragmentation. Obsidian mobile or text editors work fine |
| AI model training/fine-tuning | ML infrastructure burden. Prompt engineering sufficient |
| Built-in file storage/CDN | Storage costs, compliance issues. Assets in folders |
| Custom query language | Learning curve. Natural language via AI is better |

## Traceability

Which phases cover which requirements.

| Requirement | Phase | Status |
|-------------|-------|--------|
| CORE-01 | Phase 1 | Complete |
| CORE-02 | Phase 1 | Complete |
| CORE-03 | Phase 1 | Complete |
| CORE-04 | Phase 1 | Complete |
| CORE-05 | Phase 1 | Complete |
| CORE-06 | Phase 1 | Complete |
| CORE-07 | Phase 2 | Pending |
| CORE-08 | Phase 2 | Pending |
| INTF-01 | Phase 2 | Pending |
| INTF-02 | Phase 2 | Pending |
| INTF-03 | Phase 4 | Pending |
| INTF-04 | Phase 5 | Pending |
| INTF-05 | Phase 3 | Complete |
| INTF-06 | Phase 2 | Pending |
| ENTY-01 | Phase 1 | Complete |
| ENTY-02 | Phase 1 | Complete |
| ENTY-03 | Phase 3 | Complete |
| ENTY-04 | Phase 3 | Complete |
| ENTY-05 | Phase 3 | Complete |
| ENTY-06 | Phase 3 | Complete |
| KNOW-01 | Phase 4 | Pending |
| KNOW-02 | Phase 5 | Pending |
| KNOW-03 | Phase 5 | Pending |
| KNOW-04 | Phase 5 | Pending |
| KNOW-05 | Phase 4 | Pending |
| KNOW-06 | Phase 4 | Pending |
| VOIC-01 | Phase 6 | Pending |
| VOIC-02 | Phase 6 | Pending |
| VOIC-03 | Phase 6 | Pending |
| VOIC-04 | Phase 6 | Pending |
| VOIC-05 | Phase 6 | Pending |
| VOIC-06 | Phase 6 | Pending |
| DOCS-01 | Phase 7 | Pending |
| DOCS-02 | Phase 7 | Pending |
| DOCS-03 | Phase 7 | Pending |
| DOCS-04 | Phase 7 | Pending |
| DOCS-05 | Phase 7 | Pending |
| DOCS-06 | Phase 7 | Pending |
| DOCS-07 | Phase 7 | Pending |
| INTG-01 | Phase 7 | Pending |
| INTG-02 | Phase 8 | Pending |
| INTG-03 | Phase 8 | Pending |
| INTG-04 | Phase 8 | Pending |
| INTG-05 | Phase 8 | Pending |

**Coverage:**
- v1 requirements: 42 total
- Mapped to phases: 42
- Unmapped: 0

---
*Requirements defined: 2026-01-24*
*Last updated: 2026-01-24 - Phase 3 requirements marked Complete (ENTY-03, ENTY-04, ENTY-05, ENTY-06, INTF-05)*
