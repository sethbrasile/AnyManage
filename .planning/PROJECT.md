# Agent PM

## What This Is

A generalized template that enables non-technical project managers to use local AI coding agents (Claude Code, opencode, codex, etc.) as their project management assistant for any industry. Users clone the template, configure it for their domain (entity type, agents, templates), and operate via natural language with simple commands. The AI handles all git operations invisibly.

## Core Value

Non-technical PMs can operate a powerful AI project management system without understanding git, command line, or code — they just talk to it and it handles the rest.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Scaffold structure that works across multiple AI coding agents (Claude Code, opencode, codex)
- [ ] Configurable entity concept (user defines: clients, projects, products, patients, etc.)
- [ ] Agent-agnostic instruction file pattern (main instructions + agent-specific pointers)
- [ ] Template structure for domain-specific document types
- [ ] Subagent definition pattern for specialized tasks
- [ ] Skill definition pattern for reusable workflows
- [ ] Protocol definitions for common operations (note processing, entity onboarding)
- [ ] Natural language + structured command hybrid interface
- [ ] Invisible git management (agent commits silently, user never sees git)
- [ ] Standalone operation without external integrations
- [ ] Reference example (marketing agency use case from ../ppmc)
- [ ] Optional integration module pattern (Trello, Gmail, etc. as add-ons)
- [ ] Setup wizard or guided configuration for first-time users
- [ ] Clear documentation for customization points

### Out of Scope

- Building integrations for every possible external system — provide pattern + Trello example only
- Mobile app or GUI — this operates via AI agent in terminal
- Multi-user access control — single operator model (team uses external interface like Trello)
- Real-time collaboration features — async via external integrations
- Hosting/deployment — local-first, repo-based system

## Context

**Prior work:** Seth built a working implementation for his marketing agency (../ppmc) that demonstrates the full pattern. This template generalizes those patterns for any industry.

**Technical environment:**
- Requires git (but user-invisible)
- Works with AI coding agents: Claude Code, opencode, codex (primary targets)
- Optional: grepai + ollama for reduced token usage (research needed on actual impact)
- Optional integrations via MCP: Trello, Gmail, etc.

**Target users:** Non-technical project managers who are comfortable with email and spreadsheets but not command line or git. They need simple operational patterns they can memorize.

**Key insight from existing implementation:** The dual source of truth pattern works well — external tool (Trello) is operational truth for status, local repo is strategic context for planning.

## Constraints

- **Audience**: Must be operable by someone who has never used a terminal before following video tutorial
- **AI Agent Compatibility**: Must work across Claude Code, opencode, and codex at minimum
- **Git Invisibility**: User must never be asked about or interact with git directly
- **Standalone First**: Core functionality must work without any external integrations

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Entity type is configurable | Different industries manage different things (clients, projects, patients) | — Pending |
| Git is agent-managed and invisible | Target audience doesn't know git | — Pending |
| External integrations are optional modules | Lowers barrier to entry for first-time users | — Pending |
| Template includes reference example (marketing) | Users learn by studying working example | — Pending |
| Hybrid natural language + commands | Natural for context-heavy input, commands for repeatable actions | — Pending |

---
*Last updated: 2026-01-24 after initialization*
