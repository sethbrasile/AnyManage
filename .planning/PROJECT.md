# Agent PM

## What This Is

A generalized template that enables non-technical project managers to use local AI coding agents (Claude Code, opencode, codex, etc.) as their project management assistant for any industry. Users clone the template, configure it for their domain (entity type, agents, templates), and operate via natural language with simple commands. The AI handles all git operations invisibly.

## Core Value

Non-technical PMs can operate a powerful AI project management system without understanding git, command line, or code — they just talk to it and it handles the rest.

**Broader positioning:** Not just for project managers. This system is suitable for ANYONE who needs to manage anything or even just take notes on a regular basis and remember things. The "entity" concept is fully configurable — manage clients, projects, products, notes, ideas, research topics, or anything else you track over time.

## Current State (v1.0 Shipped)

**Shipped:** 2026-01-25
**Stats:** 8 phases, 24 plans, 42 requirements, 179 markdown files, 40,583 lines

**What v1.0 Delivers:**
- Semantic file structure with configurable entity types
- Agent-agnostic instruction system (Claude Code, opencode, codex)
- Natural language entity management (onboarding, profiles, notes)
- Reusable skill system with three base skills
- Specialist pattern with 4-layer knowledge loading
- Voice training for personalized AI writing
- Complete documentation with tutorials and reference example
- Integration framework with Trello example

**Tech Stack:**
- Pure markdown (no databases, no proprietary formats)
- Git for version control (invisible to users)
- Works with any AI coding agent that reads INSTRUCTIONS.md

## Requirements

### Validated

- CORE-01 through CORE-08 — v1.0 (file structure, markdown storage, templates, agent-agnostic instructions)
- INTF-01 through INTF-06 — v1.0 (natural language, session protocol, skills, specialists, invisible git)
- ENTY-01 through ENTY-06 — v1.0 (entity types, folders, profiles, notes, onboarding, logging)
- KNOW-01 through KNOW-06 — v1.0 (knowledge layers, skill discovery, authoring guide)
- VOIC-01 through VOIC-06 — v1.0 (voice training, extraction, application, corrections)
- DOCS-01 through DOCS-07 — v1.0 (tutorials, example, customization, troubleshooting)
- INTG-01 through INTG-05 — v1.0 (standalone, patterns, Trello, dual source of truth)

### Active

(None currently — awaiting v1.1 milestone definition)

### Out of Scope

- Building integrations for every possible external system — provide pattern + Trello example only
- Mobile app or GUI — this operates via AI agent in terminal
- Multi-user access control — single operator model (team uses external interface like Trello)
- Real-time collaboration features — async via external integrations
- Hosting/deployment — local-first, repo-based system
- Database dependency — markdown = zero dependencies
- AI model training/fine-tuning — prompt engineering sufficient

## Context

**Prior work:** Seth built a working implementation for his marketing agency (../ppmc) that demonstrates the full pattern. This template generalizes those patterns for any industry.

**Technical environment:**
- Requires git (but user-invisible)
- Works with AI coding agents: Claude Code, opencode, codex (primary targets)
- Optional: grepai + ollama for reduced token usage
- Optional integrations via MCP: Trello, Gmail, etc.

**Target users:** Non-technical project managers who are comfortable with email and spreadsheets but not command line or git. They need simple operational patterns they can memorize.

**Key insight from v1.0:** The dual source of truth pattern works well — external tool (Trello) is operational truth for status, local repo is strategic context for planning.

## Constraints

- **Audience**: Must be operable by someone who has never used a terminal before following video tutorial
- **AI Agent Compatibility**: Must work across Claude Code, opencode, and codex at minimum
- **Git Invisibility**: User must never be asked about or interact with git directly
- **Standalone First**: Core functionality must work without any external integrations

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Entity type is configurable | Different industries manage different things (clients, projects, patients) | Validated v1.0 |
| Git is agent-managed and invisible | Target audience doesn't know git | Validated v1.0 |
| External integrations are optional modules | Lowers barrier to entry for first-time users | Validated v1.0 |
| Template includes reference example (marketing) | Users learn by studying working example | Validated v1.0 |
| Hybrid natural language + commands | Natural for context-heavy input, commands for repeatable actions | Validated v1.0 |
| INSTRUCTIONS.md as source of truth | Agent-agnostic; thin wrappers point to single canonical file | Validated v1.0 |
| Four-layer knowledge hierarchy | Progressive context loading for specialists | Validated v1.0 |
| Dual source of truth for integrations | External = status, local = context | Validated v1.0 |

---
*Last updated: 2026-01-25 after v1.0 milestone*
