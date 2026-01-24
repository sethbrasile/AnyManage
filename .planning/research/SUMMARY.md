# Project Research Summary

**Project:** AI-Assisted Project Management Template System
**Domain:** Knowledge management / Project management template for non-technical users
**Researched:** 2026-01-24
**Confidence:** HIGH

## Executive Summary

This project creates an **agent-agnostic PM template** that enables non-technical users (marketing managers, consultants, small business owners) to manage clients, projects, or products using AI coding assistants as their interface. The system is file-based (markdown + git), works across multiple AI agents (Claude Code, Cursor, Aider, OpenCode), and requires zero technical knowledge from end users.

The recommended approach is a **semantic file-based architecture** where structured markdown files act as the data layer, AI agents act as the processing layer, and natural language serves as the user interface. Core technologies are intentionally minimal: markdown for documents, JSON for configuration, Git for version control (invisible to users), and simple string replacement for templating. This "no-framework" approach ensures portability, longevity, and accessibility.

The **critical risk** is making assumptions about user technical literacy. The reference implementation (ppmc - marketing agency system) demonstrates that experienced developers often expose technical concepts (git, terminal, configuration files) that intimidate non-technical users. Success depends on aggressive abstraction: users speak in plain English about their business domain, the AI handles all technical operations invisibly. Secondary risks include agent compatibility drift and integration complexity creep.

## Key Findings

### Recommended Stack

The research strongly recommends a **minimalist, universal stack** that prioritizes portability and simplicity over sophistication. The reference implementation proves this approach works in production.

**Core technologies:**
- **Markdown with YAML frontmatter**: Universal instruction and data format — all AI agents parse it natively, human-readable, version-controllable, no proprietary lock-in
- **JSON for configuration**: Machine-readable settings and metadata — simpler and more universal than YAML for programmatic access
- **Semantic directory structure**: Predictable folder naming (.planning/, entities/, templates/, protocols/) — AI agents navigate by convention, users find information intuitively
- **Git (invisible)**: Automatic version control and audit trail — all agents support it, provides backup/recovery, but completely hidden from end users
- **Simple string replacement**: Template engine using {{variable}} or [PLACEHOLDER] patterns — zero dependencies, works everywhere, easy for AI to execute

**Avoid:**
- Databases (complexity, dependencies, non-human-readable)
- Custom query languages (learning curve, use natural language instead)
- Framework dependencies (maintenance burden, upgrade fragility)
- Real-time collaboration features (massive complexity for async workflows)

**Confidence:** HIGH (90%+) — All modern AI agents handle markdown/git natively. Reference implementation validates this approach.

### Expected Features

Research identified clear tiers based on the working ppmc implementation and user workflow analysis.

**Must have (table stakes):**
- **Structured folder hierarchy per entity** — users need predictable organization or information becomes unfindable
- **Markdown-based files** — human-readable, AI-parseable, platform-agnostic, survives technology changes
- **Template system** — ensures consistency, encodes best practices, eliminates "blank page" friction
- **Entity profile + roadmap documents** — core two docs for context (who/what) and task tracking (status/next)
- **Simple task tracking** — checkbox syntax ([ ], [-], [x]) is instantly understood, grep-able, future-proof
- **Natural language agent interface** — target users don't use command line, must say "Process notes for ClientX" not run scripts
- **Agent instruction files** — AI needs role definition, folder structure knowledge, and protocols to operate autonomously
- **Note processing workflow** — transform brain dumps and meeting notes into structured tasks without manual formatting
- **Master roadmap dashboard** — single-page view of all active work prevents entity siloing
- **Date stamping & status tracking** — timeline visibility and accountability require when work started/completed

**Should have (competitive differentiators):**
- **Multi-agent specialist architecture** — domain-specific subagents (assessment, strategy, execution) provide expert-level output
- **Skills system** — reusable workflows codified as callable skills (e.g., "process-client-notes") create institutional memory
- **Bi-directional external tool sync** — Trello/Asana integration with clear operational vs. strategic truth separation
- **Domain-specific playbooks** — shared knowledge base (frameworks, best practices) turns AI into domain expert, not just task manager
- **Incremental profile building** — "Add [fact] to [Name]'s profile" allows lazy, additive knowledge capture
- **Automation workflow documentation** — Mermaid flowcharts make complex automations transparent and transferable
- **Multi-modal input methods** — direct edit, ChatGPT interview, voice memo brain dump reduces activation energy
- **Subagent logging** — transparency into AI actions enables debugging and audit trails
- **Session protocol** — AI knows what to read on startup for instant context restoration across sessions
- **Agent-agnostic design** — works with Claude Code, OpenCode, Cursor, Aider for cost optimization and future-proofing

**Defer (v2+):**
- Database backend (markdown scales surprisingly far)
- Mobile apps (Obsidian mobile + git clients suffice)
- Real-time collaboration (async workflows are the norm)
- Built-in billing/time tracking (integrate with existing tools)
- Custom query language (natural language queries via AI eliminate need)
- Notification system (adds complexity, users check roadmap when ready)
- AI model fine-tuning (prompt engineering via instruction files achieves 90% of benefits with 1% complexity)

### Architecture Approach

The ppmc reference implementation demonstrates a **three-layer semantic architecture**: files as data, agents as processors, natural language as interface. This inverts traditional application architecture where code handles logic and databases store data. Here, documents ARE the data model, AI reads/writes them following protocols defined in instruction files.

**Major components:**

1. **Core Instruction System** (INSTRUCTIONS.md, AGENTS.md, CLAUDE.md) — Defines agent persona, responsibilities, session protocols, and workflows. Single source of truth for agent behavior with thin agent-specific wrappers for compatibility.

2. **Entity Management System** (entities/ directory) — Isolates all work for a specific client/project/product in dedicated workspace with standardized structure: ENTITY_PROFILE.md (static reference), ENTITY_ROADMAP.md (dynamic task tracking), plus domain-specific subfolders (planning/, strategy/, execution/, notes/, etc.).

3. **Agent/Subagent System** (.claude/agents/, .opencode/skills/) — Specialized AI personas for domain expertise (assessment specialist, strategy specialist, execution specialist). Each defined as markdown file with YAML frontmatter specifying model preference (opus for strategy, sonnet for analysis, gemini-flash for integrations).

4. **Skill/Protocol System** (skills/ directories) — Reusable workflows for common tasks (process-client-notes, trello-sync). Skills are procedures, subagents are personas. Cross-agent compatible via SKILL.md format.

5. **Template System** (templates/ directory) — Ensures consistency via ENTITY_PROFILE_TEMPLATE.md, ENTITY_ROADMAP_TEMPLATE.md, plus domain-specific deliverable templates. Automation scripts copy → rename → replace placeholders.

6. **Integration Layer** (skills + MCP servers) — Optional external tool connections (Trello, CRM, calendar) via skills that define sync protocols. Standalone-first architecture: integrations enhance but never replace core functionality.

**Key architectural patterns:**
- **Reference, don't duplicate**: Profile = static facts, Roadmap = dynamic status. Roadmap links to profile.
- **Locality of information**: All entity data in entity folder, folder names indicate content type
- **Graceful degradation**: Works without scripts (manual fallback), without integrations (standalone), across agents
- **Session initialization protocol**: Every session loads instructions → reads ROADMAP.md → identifies active work → loads context
- **Clear authority hierarchy**: External tools are operational truth (status), local repo is strategic truth (scope/context)

### Critical Pitfalls

Research identified 36 pitfalls mapped across implementation phases. The top critical risks:

1. **Assuming Git Literacy** — Exposing users to git terminology, commands, or error messages. Prevention: Agent handles ALL git operations silently, never mentions commits/branches, translates git errors to plain English ("Your work is saved automatically").

2. **Terminal Intimidation** — Assuming users understand paths, navigation, or command syntax. Prevention: Tutorial shows exact keystrokes with screenshots, agent uses plain language ("your project folder" not "current directory"), all paths handled internally.

3. **Configuration Complexity** — Requiring users to edit JSON/YAML files or understand configuration schemas. Prevention: Interactive setup wizard with conversational prompts, agent reads/writes all configs, natural language configuration ("Set my entity type to clients").

4. **Agent-Specific Instruction Divergence** — Creating separate CLAUDE.md and OPENCODE.md that drift over time. Prevention: Single INSTRUCTIONS.md source of truth with thin agent-specific wrappers that point to main instructions.

5. **Ambiguous Command Parsing** — Users don't know what phrases trigger actions vs. general conversation. Prevention: Document command patterns with variations, forgiving parsing (multiple phrasings work), agent confirms intent before destructive operations.

6. **Invisible System State** — Users don't know what the system is doing or where information lives. Prevention: Agent explains actions ("Creating your client folder..."), clear completion confirmations, status commands in plain language, visual progress indicators.

7. **No Obvious Entry Point** — Users don't know where to start or what to do first. Prevention: Single entry point ("Type 'help' to start"), first-run wizard guides setup, README has exactly one instruction, agent greets new users and offers guided tour.

8. **Integration as Requirement** — System requires external integrations to be useful. Prevention: Standalone first (full value without integrations), integrations as optional enhancements, graceful degradation when unavailable.

9. **Sync Conflicts and Dual Truth** — Data in repo conflicts with data in external system. Prevention: Explicit truth hierarchy documented (e.g., "Trello is truth for status, repo for context"), sync direction clearly defined, agent warns before overwriting.

10. **No Upgrade Path** — Users can't update template without losing customizations. Prevention: Separate user data from template structure, migration scripts for breaking changes, version compatibility checking.

## Implications for Roadmap

Based on combined research, the system should be built in **8 phases** that follow dependency order and progressive user experience refinement.

### Phase 1: Universal Foundation
**Rationale:** Establish the file-based data model and cross-agent instruction system before building features. Agent compatibility must be validated early or later phases will require rework.

**Delivers:**
- Root documentation (README.md, INSTRUCTIONS.md, AGENTS.md)
- Template system (ENTITY_PROFILE_TEMPLATE.md, ENTITY_ROADMAP_TEMPLATE.md)
- Master ROADMAP.md with checkbox task tracking
- Working entity folder structure

**Addresses features:** Structured folder hierarchy, markdown-based files, template system, entity profile + roadmap (all table stakes)

**Avoids pitfalls:** [8] Agent-specific instruction divergence, [11] Directory structure conflicts, [26] Customization vs. core confusion

**Research flag:** SKIP — Well-documented patterns, reference implementation validates approach

### Phase 2: Automation Layer
**Rationale:** Reduce manual setup friction before user testing. Scripts enable fast iteration and onboarding validation.

**Delivers:**
- scripts/new_entity.sh (creates folder structure, copies templates, updates ROADMAP.md)
- scripts/new_note.sh (timestamped note file creation)
- Makefile with user-friendly commands (make entity name="Name", make note, make list)

**Addresses features:** Master roadmap dashboard (table stakes)

**Avoids pitfalls:** [5] Overwhelming initial complexity, [21] No obvious entry point

**Research flag:** SKIP — Standard shell scripting patterns

### Phase 3: Agent Configuration
**Rationale:** AI agents must operate autonomously before implementing domain features. Session protocol critical for context continuity.

**Delivers:**
- Refined INSTRUCTIONS.md with session initialization, entity onboarding, task tracking, note processing protocols
- Cross-agent compatibility layer (.claude/, .opencode/, .agent/ directories with pointers to INSTRUCTIONS.md)
- Agent instruction validation

**Addresses features:** Agent instruction files, natural language interface foundation, session protocol (table stakes + differentiators)

**Avoids pitfalls:** [9] Tool availability assumptions, [10] Hardcoded agent names, [17] Context loss across sessions

**Research flag:** SKIP — Reference implementation provides templates

### Phase 4: Note Processing Skill
**Rationale:** Unstructured → structured transformation is the core value proposition. Must work reliably before adding complexity.

**Delivers:**
- process-notes skill in .claude/skills/ and .opencode/skills/
- SKILL.md format: extract tasks → update profile → update strategy → mark processed
- Model recommendation (gemini-flash for cost-effectiveness)

**Addresses features:** Note processing workflow (table stakes), skills system (differentiator)

**Avoids pitfalls:** [14] Ambiguous command parsing, [15] Scope creep in requests

**Research flag:** NEEDED — Note parsing accuracy is critical, needs validation with diverse input formats

### Phase 5: User-Facing Abstraction
**Rationale:** Non-technical user experience depends on complete abstraction of technical concepts. Cannot validate with real users until this is complete.

**Delivers:**
- Git invisibility layer (silent commits, plain English error messages)
- Terminal abstraction (no paths exposed to users)
- Configuration wizard (conversational setup, no manual JSON editing)
- System state visibility (agent explains actions, progress indicators)
- Error recovery system (friendly errors, agent-offered fixes, undo commands)
- Jargon elimination pass (business terminology only)

**Addresses features:** Natural language agent interface (table stakes)

**Avoids pitfalls:** [1] Git literacy assumptions, [2] Terminal intimidation, [3] Configuration complexity, [4] Invisible system state, [6] Jargon infiltration, [7] Error recovery gaps

**Research flag:** SKIP — UX patterns well-established, reference implementation shows what to avoid

### Phase 6: Onboarding & Documentation
**Rationale:** Can't validate with real users until onboarding is excellent. This phase is critical validation gate.

**Delivers:**
- Tutorial for absolute beginners (every keystroke, video walkthrough, prerequisites checklist)
- First-run wizard with guided tour
- Template vs. example clarity (clear naming, fictional examples)
- Migration path documentation (import from spreadsheets, Trello exports)
- Troubleshooting guide and FAQ
- Customization guide (what to edit vs. what to leave alone)

**Addresses features:** Multi-modal input methods (differentiator)

**Avoids pitfalls:** [20] Tutorial assumes too much, [22] Template/example confusion, [23] Missing migration path, [24] Outdated documentation, [25] No troubleshooting guide

**Research flag:** NEEDED — User testing with non-technical users required to validate onboarding flow

### Phase 7: Subagent System
**Rationale:** Domain expertise is a key differentiator but requires solid foundation. Subagents build on working skills system.

**Delivers:**
- assessment-specialist.md (audits, gap analysis, deliverables: AUDIT_FINDINGS.md, GAPS_ANALYSIS.md)
- strategy-specialist.md (synthesize findings, deliverables: STRATEGY.md, POSITIONING.md)
- execution-specialist.md (optional, campaign planning)
- Subagent logging to subagent_logs/ for transparency
- Model selection per subagent (opus for strategy, sonnet for assessment)

**Addresses features:** Multi-agent specialist architecture (differentiator), subagent logging (differentiator)

**Avoids pitfalls:** [12] Skill/subagent pattern incompatibility, [19] No progressive learning

**Research flag:** SKIP for general subagent pattern; NEEDED for domain-specific specialist personas (varies by template customization)

### Phase 8: Optional Integrations
**Rationale:** Integrations are enhancements, not requirements. Come last to avoid scope creep and ensure standalone value.

**Delivers:**
- Task management integration framework (Trello/Asana/Linear skill template)
- TASK_SYNC_PLAYBOOK.md with truth hierarchy and conflict resolution
- Integration configuration guide (API keys, permissions, OAuth flows)
- CRM integration pattern (optional)
- Calendar integration pattern (optional)
- Graceful degradation when integrations unavailable

**Addresses features:** Bi-directional external tool sync (differentiator)

**Avoids pitfalls:** [27] Integration as requirement, [28] Sync conflicts and dual truth, [29] API complexity leakage, [30] Integration maintenance burden, [31] Platform lock-in creep

**Research flag:** NEEDED — Each specific integration (Trello, Asana, HubSpot, etc.) requires API research and sync protocol design

### Phase Ordering Rationale

- **Foundation before features**: Phases 1-3 establish data model and agent compatibility before implementing user-facing functionality
- **Core value first**: Phase 4 (note processing) delivers the primary value proposition before enhancement features
- **UX polish gates validation**: Phase 5 (abstraction) must complete before Phase 6 (onboarding) because you can't validate UX with technical debt exposed
- **Advanced features last**: Subagents (Phase 7) and integrations (Phase 8) are differentiators but not MVP-critical
- **Dependency chain respected**: Skills system (Phase 4) before subagent system (Phase 7); automation scripts (Phase 2) before onboarding that references them (Phase 6)

### Research Flags

**Phases likely needing deeper research during planning:**
- **Phase 4 (Note Processing):** Note parsing accuracy critical — needs validation with diverse input formats (emails, meeting transcripts, voice memos, brain dumps). Consider creating test corpus.
- **Phase 6 (Onboarding):** User testing required — needs actual non-technical users to validate tutorial, wizard, and error messages. Plan usability sessions.
- **Phase 7 (Subagents - domain-specific):** Specialist personas vary by domain — marketing agency needs assessment/strategy/execution, product management needs research/spec/roadmap, real estate needs market analysis/valuation. Research domain-specific frameworks.
- **Phase 8 (Integrations):** Each external tool requires API research — Trello, Asana, Linear, HubSpot, Salesforce all have different APIs, rate limits, auth flows. Research per integration individually.

**Phases with standard patterns (skip research-phase):**
- **Phase 1 (Foundation):** Markdown + git patterns well-documented, reference implementation validates
- **Phase 2 (Automation):** Standard shell scripting, no novel approaches
- **Phase 3 (Agent Config):** Reference implementation provides templates
- **Phase 5 (Abstraction):** UX patterns established, mainly execution
- **Phase 7 (Subagents - pattern):** General subagent pattern proven in ppmc, only domain customization needs research

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | **HIGH** | Markdown/git/JSON are universal across all modern AI agents. Reference implementation (ppmc) validates this approach in production. Only medium confidence on grepai/Ollama semantic search (requires additional setup). |
| Features | **HIGH** | Table stakes features validated in working ppmc system. Differentiators directly observed in reference implementation. Anti-features based on complexity analysis and scope discipline. |
| Architecture | **HIGH** | Three-layer architecture (files/agents/natural language) proven in ppmc. Component boundaries clear. Data flow patterns documented. Generalization path validated. |
| Pitfalls | **MEDIUM-HIGH** | 36 pitfalls identified from reference implementation analysis and common AI agent UX failures. Phase mapping solid. Some pitfalls require user testing to validate prevention strategies (especially UX abstraction). |

**Overall confidence:** **HIGH**

The research benefits from an existing, working reference implementation (ppmc) that demonstrates the architecture in production use. This is not theoretical — we're extracting patterns from a real system. Confidence is reduced slightly on:
- Multi-agent compatibility (tested with Claude Code and OpenCode in ppmc, but Cursor/Aider/windsurf need validation)
- Non-technical user onboarding (ppmc built by/for technical user, abstraction layer untested with actual target users)
- Domain generalization (ppmc is marketing-specific, other domains need validation)

### Gaps to Address

**During planning/execution:**

1. **Multi-agent testing protocol**: Need systematic testing across Claude Code, Cursor, Aider, OpenCode, windsurf with same instruction files. Create agent compatibility test suite.

2. **Non-technical user validation**: ppmc was built by technical user. Need usability testing with actual target users (marketing managers, consultants, small business owners with email/spreadsheet skills only). Plan 5-10 user onboarding sessions.

3. **Domain customization templates**: Marketing domain proven, but need to validate generalization. Create example customizations for 2-3 other domains (product management, real estate, consulting) to test template flexibility.

4. **Performance at scale**: ppmc manages ~10 clients. Needs testing with 50+ entities to validate performance assumptions and identify archiving/pagination needs.

5. **Note processing accuracy**: Critical feature. Create test corpus with diverse input formats (formal emails, casual Slack messages, meeting transcripts, voice memo transcriptions, brain dumps). Measure extraction accuracy across models (opus, sonnet, gemini-flash).

6. **Integration sync protocols**: Trello sync proven in ppmc. Each additional integration (Asana, Linear, HubSpot, etc.) requires API research, rate limit handling, and conflict resolution design. Don't assume Trello patterns generalize.

7. **Security review**: ppmc stores client confidential info. Need to validate .gitignore patterns, document security best practices, consider encryption at rest for sensitive folders.

8. **Upgrade migration**: ppmc is v1. Need to design and test upgrade path before shipping template to users (can they upgrade from v1 → v2 without losing customizations?).

## Sources

### Primary (HIGH confidence)

- **Reference Implementation**: /Users/seth/Documents/GitHub/ppmc — Working marketing agency management system, production-validated architecture, demonstrates all core patterns
- **Current Project Context**: /Users/seth/Documents/GitHub/agent-pm/PROJECT.md — Requirements specification, user personas, constraints
- **Claude Code Documentation**: Official agent instruction patterns, MCP (Model Context Protocol) integration, permission system, skills SDK
- **Markdown Standards**: CommonMark specification, YAML frontmatter conventions (Jekyll/Hugo/Obsidian compatibility)
- **Git Best Practices**: Conventional commits, semantic versioning, .gitignore patterns for secrets

### Secondary (MEDIUM confidence)

- **AI Agent Patterns (2025)**: Emerging conventions for .claude/, .cursorrules, .aider.conf.yml, .agent/ directories — community patterns, not formal standards
- **Multi-Agent Compatibility**: Analysis of Claude Code, Cursor, Aider, OpenCode instruction file patterns — inferred from documentation and community examples
- **Template/Placeholder Conventions**: Mustache {{variable}}, [PLACEHOLDER], ALL_CAPS patterns — widely adopted but not standardized
- **Non-Technical User UX**: Common failure patterns from AI coding assistant adoption by non-developers — based on user feedback patterns, not formal studies

### Tertiary (LOW confidence)

- **grepai/Ollama Integration**: Semantic search for token reduction — promising but requires validation with actual implementation
- **MCP Server Portability**: Currently Claude Code specific, unclear adoption path for other agents — needs monitoring as ecosystem evolves
- **Cross-Agent Skill Standardization**: No universal skill definition format yet — each agent has different extensibility model, may require per-agent implementations

---
*Research completed: 2026-01-24*
*Ready for roadmap: yes*
