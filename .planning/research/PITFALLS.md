# Pitfalls Research

Research findings on common mistakes in AI-assisted project management template systems for non-technical users.

## Non-Technical User Pitfalls

### 1. Assuming Git Literacy
- **Pitfall**: Exposing users to git terminology, commands, or error messages
- **Warning Signs**: Documentation mentions "commits," "branches," "pull/push"; errors show git output; instructions include git commands
- **Prevention Strategy**:
  - Agent handles ALL git operations silently
  - Never ask users about commit messages, branching, or merge conflicts
  - Error messages translate git problems into user-friendly language
  - Tutorial never mentions git exists

### 2. Terminal Intimidation
- **Pitfall**: Assuming users understand terminal basics (paths, navigation, commands)
- **Warning Signs**: Documentation uses technical terms like "cwd," "absolute path," "execute"; instructions assume knowledge of directory structure
- **Prevention Strategy**:
  - Tutorial shows exact keystrokes/screenshots for every operation
  - Agent prompts use plain language ("your project folder" not "current directory")
  - All paths handled internally - user never constructs a path
  - Commands use memorable natural language patterns

### 3. Configuration Complexity
- **Pitfall**: Requiring users to edit JSON/YAML or understand configuration schemas
- **Warning Signs**: Setup instructions say "edit config.json"; users must know key-value syntax; no validation on config errors
- **Prevention Strategy**:
  - Interactive setup wizard with conversational prompts
  - Agent reads/writes all config files
  - Natural language configuration: "Set my entity type to clients"
  - Validation with friendly error messages

### 4. Invisible System State
- **Pitfall**: Users don't know what the system is doing or where information lives
- **Warning Signs**: Files appear/disappear mysteriously; no feedback on operations; unclear what's happening during long operations
- **Prevention Strategy**:
  - Agent explains what it's doing: "Creating your client folder..."
  - Clear completion confirmations with next steps
  - Status commands that show system state in plain language
  - Visual progress indicators for multi-step operations

### 5. Overwhelming Initial Complexity
- **Pitfall**: Showing users the full system structure upfront
- **Warning Signs**: Documentation explains every folder; setup creates 20+ empty files; users see template variables before having entities
- **Prevention Strategy**:
  - Progressive disclosure: show only what's needed now
  - Just-in-time folder creation (create client folder when they add first client)
  - Start with single successful workflow before showing advanced features
  - Hide system folders (.planning, .claude, etc.) from user awareness

### 6. Jargon Infiltration
- **Pitfall**: Using AI/tech terms that make sense to developers but not PMs
- **Warning Signs**: Documentation says "subagent," "skill," "prompt engineering," "context window"
- **Prevention Strategy**:
  - Use domain language: "specialist" not "subagent," "workflow" not "skill"
  - Avoid meta-references to AI ("I will process..." → just do it)
  - Tutorial uses business terminology from target industry
  - No mentions of tokens, models, or AI architecture

### 7. Error Recovery Gaps
- **Pitfall**: Cryptic errors with no clear recovery path for non-technical users
- **Warning Signs**: Errors show stack traces; suggestions require technical knowledge; no "undo" mechanism
- **Prevention Strategy**:
  - Every error has a plain English explanation and fix
  - Agent offers to fix problems automatically
  - Undo command for common operations
  - Escape hatches: "Start over with this client" command

## Multi-Agent Compatibility Pitfalls

### 8. Agent-Specific Instruction Files
- **Pitfall**: Creating separate instruction files that diverge over time
- **Warning Signs**: CLAUDE.md and OPENCODE.md have different protocols; one agent works, another doesn't; duplication of 90% of content
- **Prevention Strategy**:
  - Single source of truth (e.g., INSTRUCTIONS.md) with all agent-agnostic logic
  - Agent-specific files are thin wrappers that load main instructions
  - Explicit pointers: "Read INSTRUCTIONS.md for protocols"
  - Version checking to ensure consistency

### 9. Tool Availability Assumptions
- **Pitfall**: Instructions assume specific tools that don't exist in all agents
- **Warning Signs**: Code breaks when agent lacks Task tool; MCP integrations hardcoded; assumes specific skill syntax
- **Prevention Strategy**:
  - Graceful degradation: check tool availability, adapt approach
  - Core functionality works without any MCP integrations
  - Document which features require which tools
  - Agent-specific skill patterns in separate optional modules

### 10. Hardcoded Agent Names
- **Pitfall**: Instructions reference "Claude" by name or use agent-specific features
- **Warning Signs**: "As Claude, you should..."; references to Claude-specific Skills or Projects
- **Prevention Strategy**:
  - Generic role descriptions: "You are a project management assistant..."
  - Avoid brand names in core instructions
  - Feature detection over agent detection
  - Test with multiple agents during development

### 11. Directory Structure Conflicts
- **Pitfall**: Different agents expect config in different locations
- **Warning Signs**: .claude/ works but .opencode/ fails; conflicts between .cursorrules and INSTRUCTIONS.md
- **Prevention Strategy**:
  - Agent-specific folders (.claude/, .opencode/, .windsurf/) for their configs only
  - Shared operational files in neutral locations (.planning/, templates/)
  - Clear documentation of folder purpose
  - Agents read from shared locations for source of truth

### 12. Skill/Subagent Pattern Incompatibility
- **Pitfall**: Subagent pattern works in Claude but not in other agents
- **Warning Signs**: Subagent calls fail; no equivalent to Claude's Task tool; circular dependencies
- **Prevention Strategy**:
  - Document specialist personas in markdown, not as executables
  - Main agent "becomes" the specialist by reading their instructions
  - Fallback: single agent follows specialist protocols inline
  - Optional: enhanced mode when Task tool available

### 13. Model Capability Assumptions
- **Pitfall**: Instructions assume specific context window or capability level
- **Warning Signs**: Works with Opus, fails with Haiku; requires 200K tokens; assumes vision capabilities
- **Prevention Strategy**:
  - Design for smallest common denominator (Sonnet-level capability)
  - Break large contexts into smaller chunks
  - Don't assume multimodal capabilities in core workflows
  - Document minimum model requirements

## Natural Language Interface Pitfalls

### 14. Ambiguous Command Parsing
- **Pitfall**: Users don't know what phrases trigger actions vs. general conversation
- **Warning Signs**: "Show me client X" works but "Can you show client X?" doesn't; inconsistent behavior; users frustrated by needing exact phrasing
- **Prevention Strategy**:
  - Hybrid interface: structured commands for repeatable actions, NL for context
  - Document command patterns with variations: "/client add [name]" OR "Add client [name]" OR "Create new client [name]"
  - Agent confirms intent before destructive operations
  - Forgiving parsing: multiple phrasings work

### 15. Scope Creep in Requests
- **Pitfall**: Users make vague requests that could mean many things
- **Warning Signs**: "Update the project" - which project? what update?; agent guesses wrong; work must be redone
- **Prevention Strategy**:
  - Agent asks clarifying questions before acting
  - Scope confirmation: "I'll update Project X's timeline to Y. Proceed?"
  - Required parameters: teach users to include entity name in requests
  - Context awareness: remember recently discussed entities

### 16. Invisible Mental Models
- **Pitfall**: Agent works differently than user expects, causing confusion
- **Warning Signs**: User expects client folders to sync to external systems automatically; surprises about what persists vs. what's ephemeral
- **Prevention Strategy**:
  - Tutorial explicitly teaches the mental model
  - Agent explains its interpretation: "I'll save this to the client folder, which is permanent storage"
  - Clear boundaries: "This lives in [location] and is used for [purpose]"
  - Consistent behavior patterns across operations

### 17. Context Loss Across Sessions
- **Pitfall**: Agent forgets previous session context, user must re-explain
- **Warning Signs**: User says "continue working on that proposal" but agent doesn't know which one; no session continuity
- **Prevention Strategy**:
  - Session handoff protocol: agent writes context file on exit
  - Resume command: "Resume work" loads last session context
  - Persistent state in files, not just conversation memory
  - Explicit context loading: "I see we were working on X last time..."

### 18. Overly Verbose Responses
- **Pitfall**: Agent writes long explanations when user wants quick action
- **Warning Signs**: Users skip reading responses; important info buried in text; frustration with "chattiness"
- **Prevention Strategy**:
  - Action-first communication: do the task, then confirm briefly
  - Structured output: use bullets, headers for scannability
  - Verbosity settings: "brief mode" vs. "explain mode"
  - Summary-first: lead with what happened, details on request

### 19. No Progressive Learning
- **Pitfall**: Agent treats expert users the same as beginners
- **Warning Signs**: Repeats same explanations every time; no adaptation to user skill level
- **Prevention Strategy**:
  - Track user expertise level in profile
  - Reduce explanations as user demonstrates competence
  - "Skip tips" mode for experienced users
  - Adaptive prompting based on user history

## Documentation/Onboarding Pitfalls

### 20. Tutorial Assumes Too Much
- **Pitfall**: "Quick start" requires background knowledge
- **Warning Signs**: Steps like "clone the repo" without explaining git/GitHub; assumes terminal is already open; skips prerequisites
- **Prevention Strategy**:
  - Absolute beginner tutorial: every single click/keystroke
  - Video walkthrough showing actual screen
  - Prerequisites checklist with install links
  - Test tutorial with actual non-technical user

### 21. No Obvious Entry Point
- **Pitfall**: Users don't know where to start or what to do first
- **Warning Signs**: Multiple README files; no clear first command; overwhelming options
- **Prevention Strategy**:
  - Single entry point: "Type 'help' to start"
  - First-run wizard that guides setup
  - README has exactly one instruction: how to start
  - Agent greets new users and offers guided tour

### 22. Template/Example Confusion
- **Pitfall**: Users don't know if they should edit examples or create new files
- **Warning Signs**: Users edit template files directly; examples pollute their actual data; unclear what's boilerplate vs. real
- **Prevention Strategy**:
  - Clear naming: TEMPLATE.md vs. EXAMPLE.md vs. actual files
  - Templates stay in templates/ folder, never in working directories
  - Agent creates files from templates, user never touches templates
  - Example content clearly marked as fictional

### 23. Missing Migration Path
- **Pitfall**: Users can't import existing data from current systems
- **Warning Signs**: Users must manually re-enter everything; no import from Excel/Trello/etc.; abandonment during setup
- **Prevention Strategy**:
  - Import wizards for common sources (CSV, Trello export, etc.)
  - Incremental adoption: can use alongside existing system initially
  - Manual entry assisted: agent interviews user to gather info
  - Clear payoff: show value before asking for big data entry effort

### 24. Outdated Documentation
- **Pitfall**: Docs don't match current system behavior
- **Warning Signs**: Screenshots show old UI; commands don't work as documented; confusion and bug reports
- **Prevention Strategy**:
  - Version docs with releases
  - Automated testing of documented commands
  - Examples pulled from actual system, not manually written
  - Regular doc review as part of release process

### 25. No Troubleshooting Guide
- **Pitfall**: Users stuck with no idea how to get help
- **Warning Signs**: Same questions repeatedly; users giving up; no FAQ
- **Prevention Strategy**:
  - Troubleshooting section in docs addressing common issues
  - Agent detects common problems and offers specific help
  - FAQ generated from actual user questions
  - Community/support channel clearly documented

### 26. Customization vs. Core Confusion
- **Pitfall**: Users don't know what they should customize vs. what they must leave alone
- **Warning Signs**: Users break system by editing wrong files; fear of customization; everything treated as sacred
- **Prevention Strategy**:
  - Clear customization guide: "Edit these, don't edit these"
  - Customization points marked explicitly in files
  - Safe defaults: works well without customization
  - Validation: agent detects when core files corrupted

## Integration Pitfalls

### 27. Integration as Requirement
- **Pitfall**: System requires external integrations to be useful
- **Warning Signs**: "Connect to Trello first" in setup; core features broken without API keys; complexity barrier
- **Prevention Strategy**:
  - Standalone first: full value without any integrations
  - Integrations as optional enhancements
  - Clear value prop for each integration
  - Graceful degradation when integration unavailable

### 28. Sync Conflicts and Dual Truth
- **Pitfall**: Data in repo conflicts with data in external system
- **Warning Signs**: Confusion about which is current; overwriting changes; data loss
- **Prevention Strategy**:
  - Explicit truth hierarchy: "Trello is source of truth for status, repo for context"
  - Sync direction clearly documented
  - Agent warns before overwriting
  - Timestamp-based conflict resolution

### 29. API Complexity Leakage
- **Pitfall**: Users exposed to API tokens, OAuth flows, rate limits
- **Warning Signs**: Setup requires API key creation; errors about rate limits; OAuth redirect confusion
- **Prevention Strategy**:
  - Agent handles all API interactions
  - Simple credential entry: "Paste your API key here"
  - Rate limit handling invisible: agent queues/retries
  - Clear troubleshooting for auth failures

### 30. Integration Maintenance Burden
- **Pitfall**: External API changes break integrations constantly
- **Warning Signs**: Frequent "integration broken" reports; version pinning issues; abandoned integrations
- **Prevention Strategy**:
  - Minimal integrations: only high-value, stable APIs
  - Version external dependencies explicitly
  - Graceful degradation when API changes
  - Community-contributed integrations separate from core

### 31. Platform Lock-in Creep
- **Pitfall**: System becomes dependent on specific external tools
- **Warning Signs**: "Requires Trello" becomes hard requirement; references to specific tools in core logic
- **Prevention Strategy**:
  - Abstraction layer: integration interface, not direct API calls
  - Multiple integration options for same function
  - Core features never depend on integrations
  - Document alternatives clearly

### 32. Notification Overload
- **Pitfall**: Integration creates too many notifications/updates
- **Warning Signs**: Users ignore all notifications; spam perception; disabling integrations
- **Prevention Strategy**:
  - Smart batching: daily digest vs. every change
  - User-configurable notification preferences
  - Only notify on actionable items
  - Clear notification purpose and value

## Cross-Cutting Concerns

### 33. No Upgrade Path
- **Pitfall**: Users can't update template without losing customizations
- **Warning Signs**: "Don't upgrade, you'll lose everything"; manual merge required; abandoned old versions
- **Prevention Strategy**:
  - Separation: user data separate from template structure
  - Migration scripts for breaking changes
  - Version compatibility checking
  - Clear upgrade instructions with rollback option

### 34. Performance Degradation
- **Pitfall**: System slows down as data grows
- **Warning Signs**: Operations take minutes; token limits hit; agent times out
- **Prevention Strategy**:
  - Archiving strategy: move old data to archive folders
  - Pagination/chunking for large datasets
  - Index/summary files instead of reading everything
  - Performance testing with realistic data volumes

### 35. Backup/Recovery Blindspot
- **Pitfall**: Users lose data with no recovery option
- **Warning Signs**: No backup mechanism; git history only backup; deletion is permanent
- **Prevention Strategy**:
  - Git provides automatic versioning (invisible to user)
  - Soft delete: move to .trash/ instead of removing
  - Agent can restore recent deletions
  - Optional external backup integration

### 36. Security Naivety
- **Pitfall**: Sensitive data stored insecurely
- **Warning Signs**: API keys in committed files; client confidential info in public repos; no encryption
- **Prevention Strategy**:
  - .gitignore for secrets by default
  - Agent warns if committing likely secrets
  - Documentation on security best practices
  - Local-first architecture reduces attack surface

## Phase Mapping

### Phase 1: Core Structure & Agent Compatibility
**Addresses:**
- [8] Agent-Specific Instruction Files
- [9] Tool Availability Assumptions
- [10] Hardcoded Agent Names
- [11] Directory Structure Conflicts
- [26] Customization vs. Core Confusion
- [33] No Upgrade Path

**Why:** Foundation must work across agents before building features.

### Phase 2: Natural Language Interface
**Addresses:**
- [14] Ambiguous Command Parsing
- [15] Scope Creep in Requests
- [16] Invisible Mental Models
- [17] Context Loss Across Sessions
- [18] Overly Verbose Responses

**Why:** Core interaction model must be solid before adding domain features.

### Phase 3: User-Facing Abstraction Layer
**Addresses:**
- [1] Assuming Git Literacy
- [2] Terminal Intimidation
- [3] Configuration Complexity
- [4] Invisible System State
- [5] Overwhelming Initial Complexity
- [6] Jargon Infiltration
- [7] Error Recovery Gaps

**Why:** Non-technical user experience depends on proper abstraction.

### Phase 4: Onboarding & Documentation
**Addresses:**
- [20] Tutorial Assumes Too Much
- [21] No Obvious Entry Point
- [22] Template/Example Confusion
- [23] Missing Migration Path
- [24] Outdated Documentation
- [25] No Troubleshooting Guide

**Why:** Can't validate with real users until onboarding is excellent.

### Phase 5: Domain Customization
**Addresses:**
- [12] Skill/Subagent Pattern Incompatibility
- [19] No Progressive Learning
- [34] Performance Degradation

**Why:** Domain-specific features build on solid foundation.

### Phase 6: Optional Integrations
**Addresses:**
- [27] Integration as Requirement
- [28] Sync Conflicts and Dual Truth
- [29] API Complexity Leakage
- [30] Integration Maintenance Burden
- [31] Platform Lock-in Creep
- [32] Notification Overload

**Why:** Integrations are optional enhancements, come last.

### Continuous (All Phases):
**Addresses:**
- [13] Model Capability Assumptions
- [35] Backup/Recovery Blindspot
- [36] Security Naivety

**Why:** These must be considered in every phase's design.

## Key Insights from Reference Implementation

The ppmc (marketing agency) implementation revealed several working patterns:

1. **Dual Source of Truth Works**: External tool (Trello) for operational status, local repo for strategic context. Clear hierarchy prevents conflicts.

2. **Protocol-Based Instructions**: Specific protocols (Note Processing, Client Onboarding) provide structure without rigidity.

3. **Template + Example Pattern**: Rich template collection gives users starting points without forcing structure.

4. **Agent Pointer Files**: Thin agent-specific files (AGENTS.md, OPENCODE.md) that point to main instructions (CLAUDE.md) maintain consistency.

5. **Makefile Automation**: Shell scripts hide complexity, but this is a pit of failure for non-technical users who don't know make exists.

## Validation Questions

Before shipping, test each pitfall prevention:

- Can a non-technical PM complete tutorial without help?
- Does it work identically in Claude Code, opencode, and codex?
- Can user recover from any error state without technical knowledge?
- Is customization for new domain achievable in < 30 minutes?
- Do integrations remain optional for core value?
- Can user operate entirely via natural language (no file editing)?
- Does performance stay reasonable with 50+ entities?
- Are secrets and sensitive data protected by default?

## References

Based on analysis of:
- /Users/seth/Documents/GitHub/ppmc (reference implementation)
- PROJECT.md requirements
- Common AI agent UX failure patterns
- Non-technical user testing feedback patterns

---
*Research completed: 2026-01-24*
*Next: Map pitfalls to specific phase prevention strategies*
