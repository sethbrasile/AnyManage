# Agent PM Roadmap

**Core Value:** Non-technical PMs can operate a powerful AI project management system without understanding git, command line, or code.

**Phases:** 8
**Depth:** Standard
**Coverage:** 42/42 v1 requirements mapped

---

## Overview

This roadmap transforms requirements into an 8-phase delivery plan that progressively builds a fully-functional AI project management template. Each phase delivers a coherent, demonstrable capability. The build order follows natural dependencies: foundation before features, core value before enhancements, user abstraction before onboarding, and integrations as optional enhancements at the end.

---

## Phase 1: Core File Structure

**Goal:** Users can create and organize entities using a consistent, semantic file structure.

**Dependencies:** None (foundation phase)

**Plans:** 2 plans

Plans:
- [x] 01-01-PLAN.md - Foundation setup (folder structure, CONFIG.md, master ROADMAP.md)
- [x] 01-02-PLAN.md - Document templates (ENTITY_PROFILE_TEMPLATE.md, ENTITY_ROADMAP_TEMPLATE.md)

**Requirements:**
| ID | Requirement |
|----|-------------|
| CORE-01 | Structured folder hierarchy with semantic naming |
| CORE-02 | Markdown-based files for all data storage |
| CORE-03 | Template system with reusable document templates |
| CORE-04 | Entity profile + roadmap as core two documents per entity |
| CORE-05 | Checkbox task tracking with `[ ]` / `[-]` / `[x]` status |
| CORE-06 | Master roadmap dashboard showing all entities |
| ENTY-01 | Configurable entity type |
| ENTY-02 | Standard subfolder structure per entity |

**Success Criteria:**
1. User can manually create an entity folder that follows the standard structure
2. Entity profile and roadmap templates exist with clear placeholders
3. Master ROADMAP.md shows all active entities with checkbox status tracking
4. Folder names are self-documenting (entities/, templates/, ops/, .planning/)
5. All data is human-readable markdown - no databases, no proprietary formats

**Deliverables:**
- `/entities/` directory with standard subfolder structure
- `/templates/ENTITY_PROFILE_TEMPLATE.md`
- `/templates/ENTITY_ROADMAP_TEMPLATE.md`
- `/ROADMAP.md` (master dashboard)
- `CONFIG.md` (entity type configuration)

---

## Phase 2: Agent Instruction Layer

**Goal:** Any supported AI agent can operate the system following consistent instructions.

**Dependencies:** Phase 1 (needs file structure to reference)

**Plans:** 3 plans

Plans:
- [x] 02-01-PLAN.md - Create INSTRUCTIONS.md canonical agent instructions
- [x] 02-02-PLAN.md - Create STATE.md and docs/protocols/ documentation
- [x] 02-03-PLAN.md - Create agent-specific wrappers (.claude/, .opencode/, .agent/, .cursorrules)

**Requirements:**
| ID | Requirement |
|----|-------------|
| CORE-07 | Agent-agnostic instruction pattern (INSTRUCTIONS.md as source of truth) |
| CORE-08 | Thin agent-specific wrappers (.claude/, .opencode/, .cursorrules) |
| INTF-01 | Natural language + structured command hybrid interface |
| INTF-02 | Session protocol for auto-context loading on startup |
| INTF-06 | Context restoration across sessions |

**Success Criteria:**
1. Agent reads INSTRUCTIONS.md on startup and understands its role
2. Session protocol loads ROADMAP.md and identifies active work automatically
3. User can give natural language commands ("Process notes for Acme Corp")
4. Same instructions work across Claude Code, opencode, and codex
5. Agent maintains context across session restarts

**Deliverables:**
- `/INSTRUCTIONS.md` (canonical agent instructions)
- `/.claude/` directory with Claude Code wrapper
- `/.opencode/` directory with opencode wrapper
- `/.agent/` directory with codex/generic wrapper
- `.cursorrules` file for Cursor compatibility
- Session initialization protocol in instructions

---

## Phase 3: Entity Management Workflows

**Goal:** Users can onboard and manage entities through natural language commands.

**Dependencies:** Phase 2 (needs agent instructions)

**Plans:** 3 plans

Plans:
- [x] 03-01-PLAN.md - Entity onboarding protocol (create entities with natural language)
- [x] 03-02-PLAN.md - Profile building and note processing protocols (incremental updates, note extraction)
- [x] 03-03-PLAN.md - Git abstraction and action logging (invisible saves, traceability)

**Requirements:**
| ID | Requirement |
|----|-------------|
| ENTY-03 | Incremental profile building |
| ENTY-04 | Note processing workflow |
| ENTY-05 | Entity onboarding workflow |
| ENTY-06 | Subagent/specialist logging for traceability |
| INTF-05 | Invisible git management (agent commits silently, user never sees git) |

**Success Criteria:**
1. User can onboard new entity with "Add new client Acme Corp" - folder structure created automatically
2. User can add facts with "Add [fact] to Acme's profile" - profile updated incrementally
3. User can process notes with "Process notes for Acme" - tasks extracted, documents updated
4. Git commits happen silently - user never sees git commands or errors
5. All agent actions are logged for traceability

**Deliverables:**
- Entity onboarding protocol in INSTRUCTIONS.md
- Note processing protocol in INSTRUCTIONS.md
- Incremental profile update protocol
- Git abstraction layer in agent instructions
- `/subagent_logs/` convention documented

---

## Phase 4: Skill System Foundation

**Goal:** Users can invoke reusable workflows that encode best practices.

**Dependencies:** Phase 3 (needs entity management as base)

**Plans:** 3 plans

Plans:
- [x] 04-01-PLAN.md - Skill infrastructure (directories, discovery protocol, INSTRUCTIONS.md updates)
- [x] 04-02-PLAN.md - PM base skills (process-notes, get-status, weekly-review)
- [x] 04-03-PLAN.md - Skill authoring guide (documentation for creating custom skills)

**Requirements:**
| ID | Requirement |
|----|-------------|
| INTF-03 | Skill system for reusable workflow definitions |
| KNOW-01 | PM base skills layer |
| KNOW-05 | Skill discovery workflow |
| KNOW-06 | Skill authoring guide |

**Success Criteria:**
1. PM base skills exist: process-notes, get-status, weekly-review
2. User can discover available skills with "What skills do you have?"
3. User can invoke skills naturally ("Run weekly review for all clients")
4. Skills work across all supported agents (same SKILL.md format)
5. Documentation exists for creating custom skills

**Deliverables:**
- `/.claude/skills/` directory structure
- `/.opencode/skills/` directory structure
- `/.github/skills/` directory structure (cross-platform)
- Core skills: process-notes, get-status, weekly-review
- `/docs/SKILL_AUTHORING_GUIDE.md`
- Skill discovery protocol in instructions

---

## Phase 5: Specialist Pattern

**Goal:** Users can delegate domain-specific work to expert AI personas.

**Dependencies:** Phase 4 (specialists use skills)

**Plans:** 3 plans

Plans:
- [x] 05-01-PLAN.md - Specialist infrastructure and assessment specialist
- [x] 05-02-PLAN.md - Strategy specialist and INSTRUCTIONS.md integration
- [x] 05-03-PLAN.md - Specialist coordination protocol and entity onboarding update

**Requirements:**
| ID | Requirement |
|----|-------------|
| INTF-04 | Specialist pattern for domain expert personas |
| KNOW-02 | Domain knowledge layer |
| KNOW-03 | User/agency knowledge layer |
| KNOW-04 | Entity-specific knowledge layer |

**Success Criteria:**
1. Specialist subagents defined (assessment-specialist, strategy-specialist)
2. User can delegate work ("Run assessment for Acme Corp")
3. Specialists produce structured deliverables (AUDIT_FINDINGS.md, STRATEGY.md)
4. Domain knowledge is layered: base PM -> domain -> agency -> entity
5. Specialists log their work for transparency

**Deliverables:**
- `/.claude/agents/` directory with specialist definitions
- `/docs/domain/` for domain knowledge
- `/ops/` for agency/user knowledge
- Specialist coordination protocol in instructions
- Example specialists: assessment-specialist.md, strategy-specialist.md

---

## Phase 6: Voice Training System

**Goal:** Users can train the system to write in their personal voice and tone.

**Dependencies:** Phase 5 (voice applies to specialist outputs)

**Plans:** 3 plans

Plans:
- [x] 06-01-PLAN.md - Voice training infrastructure (storage directories, templates, INSTRUCTIONS.md)
- [x] 06-02-PLAN.md - Voice extraction protocol (capability assessment, sample collection, extraction)
- [x] 06-03-PLAN.md - Voice application and correction learning (modifiers, auto-activation, learning from edits)

**Requirements:**
| ID | Requirement |
|----|-------------|
| VOIC-01 | Voice training exercise |
| VOIC-02 | Example types: emails, chat messages, docs/guides |
| VOIC-03 | LLM-assisted tone/voice extraction |
| VOIC-04 | Voice profile storage |
| VOIC-05 | Selectable voice per output type |
| VOIC-06 | Voice-aware writing |

**Success Criteria:**
1. User can complete voice training with "Train my voice"
2. System extracts tone from user-provided writing samples
3. Voice profile stored persistently in user's configuration
4. User can select different voices for different outputs (formal docs vs casual chat)
5. System writes deliverables in user's voice with minimal editing needed

**Deliverables:**
- Voice training protocol in instructions
- `/templates/VOICE_TRAINING_EXERCISE.md`
- Voice profile storage convention
- Voice-aware writing protocol for specialists
- Support for multiple voice profiles

---

## Phase 7: Documentation and Onboarding

**Goal:** Non-technical users can set up and operate the system via tutorial.

**Dependencies:** Phases 1-6 (documents working system)

**Plans:** 4 plans

Plans:
- [ ] 07-01-PLAN.md - README.md entry point and foundational guides (getting-started, customization, skill-discovery)
- [ ] 07-02-PLAN.md - Reference example (marketing agency with 3 progressive clients)
- [ ] 07-03-PLAN.md - Agent-specific tutorials and troubleshooting guide + skill
- [ ] 07-04-PLAN.md - Video scripts for YouTube series (5 videos)

**Requirements:**
| ID | Requirement |
|----|-------------|
| DOCS-01 | Beginner tutorial |
| DOCS-02 | Reference example (cleaned marketing agency with fake data) |
| DOCS-03 | Customization guide |
| DOCS-04 | Video-ready structure |
| DOCS-05 | Single entry point |
| DOCS-06 | Troubleshooting guide |
| DOCS-07 | Skill discovery documentation |
| INTG-01 | Standalone operation |

**Success Criteria:**
1. README.md has single clear first step - user knows exactly what to do
2. Beginner tutorial covers every keystroke for non-technical users
3. Reference example demonstrates working system with realistic fake data
4. Structure supports YouTube walkthrough (clear visual progression)
5. System works completely without any external integrations
6. Troubleshooting guide covers common issues and solutions

**Deliverables:**
- `/README.md` (single entry point)
- `/docs/guides/` with setup tutorials, customization, troubleshooting
- `/example/` directory with marketing agency reference (3 clients at different stages)
- `/VIDEO_SCRIPTS/` with 5 video scripts for YouTube series
- Troubleshooting skill for agent-assisted diagnosis

---

## Phase 8: Integration Patterns

**Goal:** Users can optionally connect external tools while maintaining standalone value.

**Dependencies:** Phase 7 (integrations are enhancements to working system)

**Requirements:**
| ID | Requirement |
|----|-------------|
| INTG-02 | Integration pattern documentation |
| INTG-03 | Trello integration as example |
| INTG-04 | Dual source of truth pattern |
| INTG-05 | Integration skill framework |

**Success Criteria:**
1. Integration pattern documented - users can add their own integrations
2. Trello integration works as reference example
3. Dual source of truth is clear: external = status, local = context
4. System gracefully handles integration unavailability
5. Integration skills follow consistent framework

**Deliverables:**
- `/docs/INTEGRATION_GUIDE.md`
- `/ops/TASK_SYNC_PLAYBOOK.md`
- Trello integration skill
- Integration skill template
- MCP configuration examples

---

## Progress

| Phase | Name | Status | Requirements |
|-------|------|--------|--------------|
| 1 | Core File Structure | Complete | CORE-01, CORE-02, CORE-03, CORE-04, CORE-05, CORE-06, ENTY-01, ENTY-02 |
| 2 | Agent Instruction Layer | Complete | CORE-07, CORE-08, INTF-01, INTF-02, INTF-06 |
| 3 | Entity Management Workflows | Complete | ENTY-03, ENTY-04, ENTY-05, ENTY-06, INTF-05 |
| 4 | Skill System Foundation | Complete | INTF-03, KNOW-01, KNOW-05, KNOW-06 |
| 5 | Specialist Pattern | Complete | INTF-04, KNOW-02, KNOW-03, KNOW-04 |
| 6 | Voice Training System | Complete | VOIC-01, VOIC-02, VOIC-03, VOIC-04, VOIC-05, VOIC-06 |
| 7 | Documentation and Onboarding | Pending | DOCS-01, DOCS-02, DOCS-03, DOCS-04, DOCS-05, DOCS-06, DOCS-07, INTG-01 |
| 8 | Integration Patterns | Pending | INTG-02, INTG-03, INTG-04, INTG-05 |

---

## Dependency Graph

```
Phase 1: Core File Structure
    |
    v
Phase 2: Agent Instruction Layer
    |
    v
Phase 3: Entity Management Workflows
    |
    v
Phase 4: Skill System Foundation
    |
    v
Phase 5: Specialist Pattern
    |
    v
Phase 6: Voice Training System
    |
    v
Phase 7: Documentation and Onboarding
    |
    v
Phase 8: Integration Patterns
```

Phases are sequential. Each phase builds on capabilities from previous phases.

---

## YouTube Tutorial Alignment

Each phase produces a demonstrable capability suitable for video segments:

| Phase | Video Segment | What Viewers See |
|-------|---------------|------------------|
| 1 | "Setting Up Your Workspace" | Folder structure, templates, master roadmap |
| 2 | "Teaching AI Your System" | Agent instructions, session startup, cross-agent |
| 3 | "Managing Clients with AI" | Onboarding, profile building, note processing |
| 4 | "Reusable Workflows" | Skills in action, weekly reviews |
| 5 | "AI Specialists" | Assessment reports, strategy development |
| 6 | "Writing in Your Voice" | Voice training, personalized outputs |
| 7 | "Getting Started Guide" | Full tutorial walkthrough, reference example |
| 8 | "Connecting Your Tools" | Trello sync, integration patterns |

---

*Roadmap created: 2026-01-24*
*Last updated: 2026-01-25 - Phase 6 complete (3 plans executed, voice training + application + correction learning)*
