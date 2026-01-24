# Phase 2: Agent Instruction Layer - Context

**Gathered:** 2026-01-24
**Status:** Ready for planning

<domain>
## Phase Boundary

Create INSTRUCTIONS.md and agent-specific wrappers so that Claude Code, opencode, and codex can all operate the system consistently. Includes session protocol for auto-context loading and natural language + command hybrid interface.

</domain>

<decisions>
## Implementation Decisions

### Session Startup Behavior
- Load STATE.md only on startup — minimal context, agent asks for more as needed
- Silent start — no greeting, no status summary, just ready to work
- Detect pause marker: if PAUSE.md exists, offer to resume; otherwise silent start
- Agent-optimized behavior: adapt startup to agent capabilities (Claude Code can do more upfront than opencode/codex)
  - **Note:** Document the difference between "Agent-Optimized" and "Identical Behavior" approaches in docs/ so this decision can be revisited after practical testing

### Command Patterns
- Hybrid: natural language + shortcuts — "Process notes for Acme" works, power users can use `/process-notes Acme`
- Slash commands format: `/command-name` (familiar from Slack/Discord)
- Confirm destructive only — delete, overwrite, bulk changes ask first; reads/adds just run
- Batch supported — "Process notes for Acme, Beta, and Gamma" works as one request

### Cross-agent Consistency
- Agent identity is user-managed — instructions don't announce or hide which agent is running
- Agent-agnostic design — system works with just INSTRUCTIONS.md; wrappers are optional enhancements
- Graceful degradation with smart fallbacks:
  - When agent can't complete a task due to capability limitations, offer alternative ways to achieve the outcome
  - Example: If agent can't update Trello, offer a manual checklist for user
  - Use judgment to decide when fallback is important given context

### Claude's Discretion
- INSTRUCTIONS.md architecture (single source vs layered with wrapper additions)

### Error & Recovery Behavior
- Offer to create missing files: "STATE.md not found. Want me to initialize it?"
- Attempt reconciliation with user input: when context files conflict/stale, show situation, propose fix, ask user to confirm
- Checkpoint on failure: save progress, mark failure point, let user resume or fix
- Plain English only for error messages — no tech jargon for non-technical users

### Efficiency (Token Optimization)
- Core behavior + pointers (~50 lines) in INSTRUCTIONS.md/CLAUDE.md; details in docs/
- Categorized docs structure: `docs/protocols/` for operational docs, `docs/guides/` for user guides
- Balanced verbosity: concise for status/updates, fuller for teaching moments

#### grepai Integration
- **Goal:** Semantic code search using local embeddings for token-efficient context retrieval
- **Installation approach:** Agent-guided with automation
  1. Agent checks for Ollama installation
  2. If missing, agent explains prerequisites (for IT dept if needed) or asks permission to run install script
  3. Once opted-in, agent ensures: Ollama installed, `nomic-embed-text` model pulled, grepai installed
  4. Agent runs `grepai watch` when file operations occur, stops it on session close
- **Configuration:** Supports Ollama (local, recommended) or OpenAI API (cloud alternative)
- **Install reference:** https://yoanbernabeu.github.io/grepai/installation/
- **Config reference:** https://yoanbernabeu.github.io/grepai/configuration/
- **Key commands:**
  - `curl -sSL https://raw.githubusercontent.com/yoanbernabeu/grepai/main/install.sh | sh` (grepai install)
  - `brew install ollama` (macOS) or `curl -fsSL https://ollama.com/install.sh | sh` (Linux)
  - `ollama pull nomic-embed-text`
  - `grepai watch` / stop on session close

### Claude's Discretion
- Whether to implement an efficiency mode toggle (high/normal/verbose)

</decisions>

<specifics>
## Specific Ideas

- "As automated as possible" for grepai setup — script that agent asks permission to run
- Agent should list prerequisites if it can't install (for IT dept scenarios)
- The docs/ split: protocols for agent consumption, guides for user reading
- Document the "Agent-Optimized vs Identical Behavior" tradeoff for future revisiting

</specifics>

<deferred>
## Deferred Ideas

- **Cross-agent delta tool** — A utility that compares agent configurations (CLAUDE.md vs AGENTS.md, skills directories, etc.) to highlight functional differences between agents. Catches accidental divergence where one agent becomes more capable due to incremental tweaks. Could be a skill or standalone tool.

</deferred>

---

*Phase: 02-agent-instruction-layer*
*Context gathered: 2026-01-24*
