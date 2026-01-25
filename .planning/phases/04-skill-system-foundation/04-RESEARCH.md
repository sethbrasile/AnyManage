# Phase 4: Skill System Foundation - Research

**Researched:** 2026-01-24
**Domain:** AI agent skill systems, reusable workflow definitions, progressive disclosure patterns
**Confidence:** HIGH

## Summary

Phase 4 research focused on how modern AI agents (Claude Code, GitHub Copilot, OpenAI Codex, opencode) implement skill systems for reusable workflows. The ecosystem has standardized rapidly around the "Agent Skills" open specification introduced by Anthropic in October 2025 and published as an open standard in December 2025.

The core pattern is **progressive disclosure**: Skills are YAML frontmatter + Markdown files stored in `.claude/skills/` (or `.github/skills/`, `~/.copilot/skills/`). At startup, agents load only the name and description (a few dozen tokens). Full skill instructions load only when a task requires them. This architecture allows organizations to deploy extensive skill libraries without overwhelming the AI's context window.

Key insights from research:
1. **SKILL.md as standard format** - YAML frontmatter (name, description) + Markdown body (instructions). Works across Claude Code, GitHub Copilot, VS Code, OpenAI Codex, Gemini CLI, and other platforms.
2. **Description-based triggering** - The `description` field is the primary mechanism for skill activation. It should include both what the skill does AND specific triggers/contexts for when to use it.
3. **Natural language + slash commands** - Both work equally. Slash commands (`/skill-name`) are explicit shortcuts; natural language triggers skills automatically based on description matching.
4. **Batch operations are standard** - Multi-entity processing ("process notes for all clients") is a core pattern in 2026 agentic AI workflows.
5. **Skills are invisible to users** - Users talk naturally; agents decide when to load skills. Skills are implementation details, not user-facing concepts.

**Primary recommendation:** Use Agent Skills standard (SKILL.md format) with YAML frontmatter and Markdown instructions. Store skills in `.claude/skills/` for Claude Code compatibility and `.github/skills/` for cross-platform portability. Keep skill instructions under 5,000 tokens. Use the `description` field as the primary triggering mechanism.

## Standard Stack

The established tools and patterns for skill systems:

### Core Format
| Component | Version | Purpose | Why Standard |
|-----------|---------|---------|--------------|
| Agent Skills | 1.0 (Dec 2025) | Open standard for skill definition | Cross-platform compatibility, industry adoption (60k+ projects) |
| SKILL.md | YAML + Markdown | Skill definition file | Human-readable, AI-parseable, version-controllable |
| YAML frontmatter | 1.2 | Skill metadata (name, description) | Machine-readable, minimal overhead, standard across platforms |
| Markdown body | GFM | Skill instructions | Human-readable, supports code blocks, tables, links |

### Storage Locations
| Location | Purpose | When to Use |
|----------|---------|-------------|
| `.claude/skills/` | Claude Code skills (legacy) | Backward compatibility |
| `.github/skills/` | Project skills (recommended 2026) | Cross-platform portability, works with GitHub Copilot, VS Code |
| `~/.copilot/skills/` | Personal skills (recommended 2026) | User-specific skills, not committed to repo |
| `~/.claude/skills/` | Personal skills (legacy) | Backward compatibility |

### Supporting Tools
| Tool | Version | Purpose | When to Use |
|------|---------|---------|-------------|
| None required | N/A | Skills are pure markdown files | No dependencies, no libraries, no build process |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Agent Skills standard | Custom JSON/YAML format | Custom format lacks portability, no tool support |
| SKILL.md files | Inline prompts in CLAUDE.md | Inline prompts don't scale beyond ~3-5 workflows |
| YAML frontmatter + Markdown | Pure JSON | JSON less human-readable, harder to version control prose instructions |
| `.github/skills/` location | Custom `skills/` folder | Custom location may not work with GitHub Copilot, VS Code |

**Installation:**
No installation required - pure markdown files in designated folders.

## Architecture Patterns

### Recommended Project Structure
```
project-root/
├── .claude/
│   └── skills/              # Claude Code skills (legacy compatibility)
│       └── process-notes/
│           └── SKILL.md
├── .github/
│   └── skills/              # Cross-platform skills (recommended)
│       ├── process-notes/
│       │   ├── SKILL.md     # Required: name + description + instructions
│       │   ├── scripts/     # Optional: helper scripts
│       │   ├── references/  # Optional: detailed documentation
│       │   └── assets/      # Optional: templates, data files
│       ├── get-status/
│       │   └── SKILL.md
│       └── weekly-review/
│           └── SKILL.md
└── INSTRUCTIONS.md          # Core agent behavior (loads skills as needed)
```

### Pattern 1: SKILL.md Format (Agent Skills Standard)

**What:** YAML frontmatter defines metadata; Markdown body provides detailed instructions.

**When to use:** For all reusable workflows that encode best practices.

**Example:**
```markdown
---
name: process-notes
description: Process client notes into structured tasks and profile updates. Use when the user says "process notes", mentions extracting tasks from notes, or wants to update client information from meeting notes.
---

# Process Notes Skill

Extract tasks and profile facts from meeting notes.

## When to Use
- User says "process notes for [entity]"
- User mentions extracting tasks from notes
- User wants to update entity profile from meeting notes

## How It Works
1. Identify input source (pasted content, entity notes folder, or top-level NOTES.md)
2. Extract tasks, profile facts, calendar items, and follow-ups
3. Apply updates to entity's ROADMAP.md and PROFILE.md
4. Archive processed notes with timestamp header
5. Report what was extracted and where it went

## Detailed Protocol
See docs/protocols/note-processing.md for complete step-by-step protocol.
```

**Source:** [Agent Skills Specification](https://agentskills.io/specification), [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills)

### Pattern 2: Progressive Disclosure

**What:** Agent loads only skill metadata (name + description) at startup. Full instructions load only when skill is needed.

**When to use:** Always - this is how Agent Skills work by design.

**How it works:**
1. **Startup:** Agent scans `.claude/skills/`, `.github/skills/`, `~/.copilot/skills/` for SKILL.md files
2. **Load metadata:** Read YAML frontmatter from each SKILL.md (~50 tokens per skill)
3. **Match request:** When user request matches a skill's description, load full SKILL.md
4. **Execute:** Follow instructions in Markdown body
5. **Access resources:** Load scripts/, references/, assets/ only if needed during execution

**Token budget:**
- 100 skills × 50 tokens = 5,000 tokens at startup
- Full skill load = 2,000-5,000 tokens per activated skill
- Total context impact: ~10,000 tokens for typical session (5k metadata + 5k active skills)

**Source:** [Spring AI Agentic Patterns: Agent Skills](https://spring.io/blog/2026/01/13/spring-ai-generic-agent-skills/)

### Pattern 3: Natural Language + Slash Command Invocation

**What:** Skills work via both natural language (description-based matching) and explicit slash commands.

**When to use:** Always support both patterns for maximum accessibility and power-user efficiency.

**Example:**
```yaml
# SKILL.md frontmatter
---
name: weekly-review
description: Generate weekly status report across all entities. Use when the user asks for a "weekly review", "status update", or wants to see progress across all clients/projects.
---
```

**Natural language triggers:**
- "Run weekly review"
- "Show me a status update for all clients"
- "What's the progress across all entities?"
- "Generate my weekly report"

**Slash command trigger:**
- `/weekly-review`

**Agent behavior:**
- Natural language → Agent matches user input to description → Loads and executes skill
- Slash command → Agent directly loads and executes skill (bypasses description matching)

**Source:** [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)

### Pattern 4: Batch Operations Across Entities

**What:** Skills support batch processing across multiple entities in a single invocation.

**When to use:** For workflows that make sense to run across all or multiple entities (status reports, note processing, updates).

**Example:**
```markdown
# weekly-review/SKILL.md
---
name: weekly-review
description: Generate weekly status report across all entities.
---

# Weekly Review Skill

## Batch Processing
- Supports "weekly review for all clients" → processes all entities
- Supports "weekly review for Acme, Beta, Gamma" → processes specified entities
- Supports "weekly review for Acme" → processes single entity

## Output Format
Show aggregated summary:
- Total entities processed: 12
- Total active tasks: 47
- Tasks completed this week: 23
- Entities needing attention: 3 (Beta, Gamma, Zeta)

Then show per-entity breakdown:
- [Entity Name]: X active tasks, Y completed, last update [date]
```

**Implementation notes:**
- Infer "all entities" from natural language cues ("all clients", "everything", "full status")
- If ambiguous, ask user: "Run weekly review for all entities, or specific ones?"
- Process entities in parallel where possible (read-only operations)
- Process entities sequentially where required (write operations to avoid git conflicts)

**Source:** [Agentic AI Orchestration in 2026](https://onereach.ai/blog/agentic-ai-orchestration-enterprise-workflow-automation/)

### Pattern 5: Entity-Type Aware Skills

**What:** Skills can specify which entity types they work with using metadata or conditional logic.

**When to use:** When skill behavior differs based on entity type (clients vs projects vs products).

**Example:**
```yaml
# process-notes/SKILL.md frontmatter
---
name: process-notes
description: Process notes for entities. Works with any entity type (clients, projects, products).
metadata:
  compatible_entity_types: ["client", "project", "product", "patient"]
---
```

**Conditional logic in skill body:**
```markdown
## Entity Type Handling

Read entity type from CONFIG.md frontmatter:
- `entity_type: client` → Extract client-specific data (company size, industry)
- `entity_type: project` → Extract project-specific data (deadlines, milestones)
- `entity_type: product` → Extract product-specific data (features, releases)

Adapt profile section placement based on entity type.
```

**Source:** Project context from CONTEXT.md - "Entity-type aware: skills can specify which entity types they work with"

### Pattern 6: Skill Discovery Protocol

**What:** Skills are invisible to users by default, but users can optionally discover available skills.

**When to use:** For power users who want to understand agent capabilities.

**User-facing commands:**
- Natural language: "What skills do you have?", "Show me your skills", "What can you do?"
- Slash command: `/skills` (if defined)

**Agent response format:**
```
Available workflows:

Managing Entities:
- Process notes - Extract tasks and profile updates from meeting notes
- Weekly review - Generate status report across all entities
- Get status - Show current state of specific entity

The best part? You don't need to remember these - just describe what you want in plain English and I'll handle it.

Example: Instead of "/process-notes Acme", just say "I have notes from my meeting with Acme"
```

**Source:** Phase 4 CONTEXT.md - "Optional deep dive: '/skills' or 'show me your skills' reveals what's available for curious users"

### Anti-Patterns to Avoid

**Anti-Pattern 1: Forcing Skill Invocation**
- **Bad:** Require users to know skill names or use slash commands
- **Good:** Skills activate automatically from natural language; slash commands are optional shortcuts

**Anti-Pattern 2: Skill Instructions Over 5,000 Tokens**
- **Bad:** 10,000-token SKILL.md with exhaustive edge cases
- **Good:** Core instructions in SKILL.md, detailed protocols in `references/` folder, loaded only when needed

**Anti-Pattern 3: Skills Without Clear Triggering Language**
- **Bad:** `description: "Process notes"` (too vague)
- **Good:** `description: "Process client notes into tasks and profile updates. Use when the user says 'process notes', mentions extracting tasks, or wants to update profiles from meeting notes."`

**Anti-Pattern 4: Hardcoding Entity Names in Skills**
- **Bad:** Skill assumes entities are always called "clients"
- **Good:** Skills read `entity_type` from CONFIG.md and adapt terminology

**Anti-Pattern 5: Skills Asking for Confirmation on Non-Destructive Operations**
- **Bad:** "Process notes for Acme? (yes/no)" for every invocation
- **Good:** Execute immediately for adds/creates; confirm only for deletes/overwrites

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Skill discovery mechanism | Custom skill registry system | Agent Skills standard format | Industry standard, cross-platform, actively maintained |
| Natural language skill matching | Custom intent detection | LLM-native matching against description field | LLMs excel at semantic matching, no regex maintenance |
| Skill metadata format | Custom JSON schema | YAML frontmatter in SKILL.md | Human-readable, git-friendly, standard across tools |
| Skill storage location | Custom `skills/` folder | `.github/skills/` or `.claude/skills/` | Works with GitHub Copilot, VS Code, Claude Code out of the box |
| Batch entity processing | Loop with manual prompts | Single skill invocation with entity list | Better UX, less token overhead, faster execution |
| Skill documentation | Separate docs site | Inline Markdown in SKILL.md | Documentation lives with code, always in sync |
| Cross-agent compatibility | Per-agent skill files | Single SKILL.md with Agent Skills format | Write once, works everywhere (Claude, Copilot, Codex, etc.) |

**Key insight:** The Agent Skills standard (SKILL.md format with YAML frontmatter) solves 95% of skill system design problems. It's widely adopted (60k+ projects as of Jan 2026), actively maintained by Anthropic and the community, and supported by all major AI coding assistants. Custom solutions risk incompatibility and abandonment.

## Common Pitfalls

### Pitfall 1: Skill Description Doesn't Include Triggering Language

**What goes wrong:** User says "process my notes" but skill doesn't activate because description only says "Process notes" without mentioning common phrasings.

**Why it happens:** Description written from developer perspective, not user language perspective.

**How to avoid:**
- Include natural language triggers in description: "Use when the user says 'process notes', 'extract tasks from notes', or 'update profile from meeting notes'"
- Think like a user: How would they actually phrase this request?
- Test with multiple phrasings: "process notes", "I have notes", "extract tasks", etc.

**Warning signs:**
- Users consistently use slash commands instead of natural language
- "I tried asking for X but it didn't work" complaints
- Skill only activates with exact phrasing

**Source:** [Claude Code Customization Guide](https://alexop.dev/posts/claude-code-customization-guide-claudemd-skills-subagents/)

### Pitfall 2: Skill Context Budget Exceeded

**What goes wrong:** Agent has 100+ skills, descriptions exceed 15,000 character budget, some skills excluded from context.

**Why it happens:** Installing too many skills without considering token budget.

**How to avoid:**
- Keep skill descriptions concise (100-200 chars, max 300)
- Use `/context` command to check for "excluded skills" warning
- Increase budget if needed: `SLASH_COMMAND_TOOL_CHAR_BUDGET` environment variable
- For personal skills, keep only frequently-used ones in `~/.copilot/skills/`
- Project skills in `.github/skills/` should be essential only

**Warning signs:**
- `/context` shows "some skills excluded due to character budget"
- Skills that should activate don't load
- Agent suggests skills that no longer exist

**Source:** [Claude Code Skills Guide](https://claude-world.com/articles/skills-guide/)

### Pitfall 3: Batch Operations Without Progress Feedback

**What goes wrong:** User runs "weekly review for all entities", agent processes 50 entities silently for 2 minutes, user thinks it's stuck.

**Why it happens:** No interim progress updates during batch processing.

**How to avoid:**
- Show progress for batch operations: "Processing 12 entities... (3/12 complete)"
- Update after each entity in batch: "Processed Acme Corp... processing Beta Industries..."
- Final summary shows aggregated results, not just last entity

**Warning signs:**
- Users interrupting long-running batch operations
- "Is it stuck?" questions
- Users preferring to run skills one-at-a-time instead of batch

**Source:** Phase 4 CONTEXT.md - "Show progress updates during execution"

### Pitfall 4: Skills Duplicating INSTRUCTIONS.md Logic

**What goes wrong:** INSTRUCTIONS.md has note processing instructions; process-notes skill also has note processing instructions. Divergence happens over time.

**Why it happens:** Unclear boundary between core agent behavior (INSTRUCTIONS.md) and reusable workflows (skills).

**How to avoid:**
- INSTRUCTIONS.md: ~90 lines of core behavior, references to detailed protocols
- Skills: Reusable workflows that encode best practices, reference the same protocols
- Protocols (in `docs/protocols/`): Single source of truth for detailed procedures
- Skills should reference protocols, not duplicate them

**Pattern:**
```markdown
# SKILL.md
See docs/protocols/note-processing.md for complete step-by-step protocol.
This skill implements that protocol.
```

**Warning signs:**
- Same logic appears in multiple files
- Changes to one file (INSTRUCTIONS.md) don't reflect in skills
- Users get different behavior depending on how they invoke workflows

**Source:** Phase 2 decisions - "INSTRUCTIONS.md as source of truth; ~90 lines in INSTRUCTIONS.md with details in docs/protocols/"

### Pitfall 5: Skills Not Testing Entity Type Compatibility

**What goes wrong:** Skill designed for "clients" breaks when user has `entity_type: project` because it references client-specific profile sections.

**Why it happens:** Skill hardcoded for one entity type, not tested with others.

**How to avoid:**
- Read `entity_type` from CONFIG.md frontmatter at runtime
- Adapt terminology in output: "client" vs "project" vs "product"
- Adapt profile section names: "Client Background" vs "Project Overview"
- Document entity type compatibility in skill metadata

**Example:**
```yaml
# SKILL.md frontmatter
metadata:
  compatible_entity_types: ["client", "project"]
  notes: "Assumes entity profiles have 'Background' and 'Goals' sections"
```

**Warning signs:**
- Errors when entity type changes
- Hardcoded strings like "client" in skill output
- Profile updates fail because section names don't match

**Source:** Phase 4 CONTEXT.md - "Entity-type aware: skills can specify which entity types they work with"

### Pitfall 6: Skill vs Slash Command Confusion

**What goes wrong:** Developer creates both a skill at `.claude/skills/review/SKILL.md` and a slash command at `.claude/commands/review.md`, causing conflicts or duplicate behavior.

**Why it happens:** Unclear understanding that skills and commands merged in Claude Code (as of late 2025).

**How to avoid:**
- Use skills (`.claude/skills/` or `.github/skills/`) for all reusable workflows
- Slash commands (`.claude/commands/`) are legacy; migrated to skills
- Skills automatically create slash commands from the `name` field
- One SKILL.md = both natural language trigger AND slash command

**Clarification:**
- Old pattern: `.claude/commands/review.md` → creates `/review` command only
- New pattern: `.claude/skills/review/SKILL.md` → creates `/review` command AND enables natural language activation

**Warning signs:**
- Both `.claude/commands/` and `.claude/skills/` directories exist
- Confusion about which file to edit
- Duplicate slash commands

**Source:** [How to Use Claude Code Features](https://www.producttalk.org/how-to-use-claude-code-features/) - "Custom slash commands have been merged into skills"

## Code Examples

Verified patterns from official sources:

### PM Base Skill: Process Notes

```markdown
<!-- .github/skills/process-notes/SKILL.md -->
---
name: process-notes
description: Process entity notes into structured tasks and profile updates. Use when the user says "process notes", mentions extracting tasks from notes, or wants to update entity information from meeting notes. Supports batch processing across multiple entities.
license: Apache-2.0
metadata:
  author: agent-pm
  version: "1.0"
  compatible_entity_types: ["client", "project", "product"]
---

# Process Notes Skill

Extract tasks, profile facts, calendar items, and follow-ups from meeting notes and unstructured text.

## When to Use
- User says "process notes for [entity]"
- User mentions extracting tasks from notes
- User wants to update entity profile from meeting notes
- User provides notes inline, references files, or uses top-level NOTES.md

## How It Works
See `docs/protocols/note-processing.md` for complete step-by-step protocol.

**Summary:**
1. Identify input source (pasted content, entity notes/ folder, or NOTES.md)
2. Extract tasks, profile facts, calendar items, follow-ups
3. Apply updates to entity's ROADMAP.md and PROFILE.md
4. Archive processed notes with timestamp header
5. Report itemized results

## Batch Processing
Supports:
- "Process notes for Acme, Beta, Gamma" → specific entities
- "Process notes for all clients" → all entities
- "Process notes for Acme" → single entity

Shows progress: "Processing 3 entities... (1/3 complete)"

## Output Format
```
Processed notes for [Entity Name]:

- Tasks added to roadmap (3):
  - [ ] Follow up on budget proposal (by Friday)
  - [ ] Schedule quarterly review
  - [ ] Send revised timeline

- Profile updated:
  - Added to Key Contacts: Sarah Chen (CFO)
  - Added to Business Goals: Expand to 3 new markets

- Notes archived to: entities/[Name]/notes/archive/[filename].md
```

## Error Handling
- Entity not found → Offer to create entity first
- No notes found → Ask user to paste or add file
- Ambiguous content → Discussion mode (ask for clarification)
```

**Source:** Synthesized from Phase 3 note-processing protocol + Agent Skills format

### PM Base Skill: Get Status

```markdown
<!-- .github/skills/get-status/SKILL.md -->
---
name: get-status
description: Show current status of an entity or all entities. Use when the user asks "what's the status", "show me updates", or wants to see progress for clients/projects.
metadata:
  author: agent-pm
  version: "1.0"
---

# Get Status Skill

Display current state of entities: active tasks, recent updates, next steps.

## When to Use
- "What's the status of Acme?"
- "Show me updates for Beta Industries"
- "What's happening with all my clients?"

## How It Works
1. Read entity's ROADMAP.md
2. Extract active tasks, in-progress items, completed items
3. Read entity's PROFILE.md for last update timestamp
4. Format summary for user

## Output Format (Single Entity)
```
Status: Acme Corp

Active Work (3 tasks):
- [ ] Follow up on budget proposal (by Friday)
- [-] Security audit in progress (started 01/20)
- [ ] Schedule quarterly review

Recently Completed:
- [x] Send revised timeline ✅ 01/22

Last Profile Update: 2026-01-24
Next Steps: Follow up on budget by Friday
```

## Output Format (All Entities)
```
Status Across All Entities (12 total):

High Activity:
- Acme Corp: 5 active tasks, 2 in progress
- Beta Industries: 3 active tasks, 1 needs attention (deadline Friday)

On Track:
- Gamma LLC: 2 active tasks
- Delta Partners: 1 active task

Quiet:
- Epsilon Group: No active tasks (last update 01/15)
```

## Batch Processing
- "Get status for Acme, Beta" → specific entities
- "Get status for all clients" → all entities
- "Get status" (no entity specified) → ask user or default to all
```

**Source:** Phase 4 requirements + Agent Skills format

### PM Base Skill: Weekly Review

```markdown
<!-- .github/skills/weekly-review/SKILL.md -->
---
name: weekly-review
description: Generate weekly status report across all entities. Use when the user asks for a "weekly review", "status update across all clients", or wants to see progress across entities. Automatically runs for all entities unless specific ones are mentioned.
metadata:
  author: agent-pm
  version: "1.0"
---

# Weekly Review Skill

Generate comprehensive weekly status report showing progress across all entities.

## When to Use
- "Run weekly review"
- "Show me this week's progress"
- "What got done across all my clients?"
- End of week status check

## How It Works
1. Read all entity ROADMAPs
2. Extract tasks completed this week (checkbox `[x]` with date in past 7 days)
3. Extract tasks still in progress (`[-]`)
4. Identify entities needing attention (overdue tasks, no updates in 7+ days)
5. Generate aggregated summary + per-entity breakdown

## Output Format
```
Weekly Review: Jan 18-24, 2026

Summary:
- Total entities: 12
- Active tasks: 47
- Completed this week: 23
- Entities needing attention: 3 (Beta Industries, Zeta Corp, Epsilon Group)

Completed This Week (23 tasks):
[Acme Corp]
- [x] Send revised budget proposal ✅ 01/22
- [x] Schedule Q2 kickoff meeting ✅ 01/23

[Beta Industries]
- [x] Complete security audit ✅ 01/20
- [x] Submit compliance documentation ✅ 01/24

[... more entities ...]

In Progress (12 tasks):
[Acme Corp]
- [-] Security audit in progress (started 01/20)

[... more entities ...]

Needs Attention:
[Beta Industries] - Overdue: Follow up on proposal (due 01/23)
[Zeta Corp] - No updates in 10 days (last: 01/14)
[Epsilon Group] - 3 tasks pending, no progress this week

Next Week Priorities:
- Follow up on overdue items (Beta, Epsilon)
- Check in with Zeta (no recent updates)
- Continue in-progress work (Acme security audit)
```

## Customization
User can request:
- "Weekly review for high-priority clients only" → filter by priority
- "Weekly review for Acme and Beta" → specific entities
- "Weekly review showing only completed items" → filter output
```

**Source:** Phase 4 requirements + Agent Skills format

### Skill Discovery Response

```markdown
# In INSTRUCTIONS.md or as a dedicated skill

When user asks "What skills do you have?" or "/skills":

Agent response:
"""
I have several workflows available to help you:

**Managing Entities:**
- Process notes - Extract tasks and profile updates from meeting notes
- Get status - Show current state of entities
- Weekly review - Generate status report across all entities

**Working with Profiles:**
- Add facts - Update entity profiles with new information
- Show profile - Display complete entity profile

**Best part:** You don't need to remember these. Just describe what you want in plain English:
- Instead of "/process-notes Acme", say "I have notes from my meeting with Acme"
- Instead of "/get-status", say "What's the status of Beta Industries?"

Want to see a specific skill's details? Ask "How does [skill name] work?"
"""
```

**Source:** Phase 4 CONTEXT.md + user experience best practices

### Entity Type Detection in Skills

```markdown
<!-- In any SKILL.md that needs entity-type awareness -->

# Skill Instructions

## Entity Type Detection

Read entity type from CONFIG.md:
```bash
# Extract entity_type from frontmatter
ENTITY_TYPE=$(grep "^entity_type:" CONFIG.md | cut -d: -f2 | xargs)
```

Adapt terminology:
- If `entity_type: client` → Use "client", "Client Background", "Client Goals"
- If `entity_type: project` → Use "project", "Project Overview", "Project Milestones"
- If `entity_type: product` → Use "product", "Product Description", "Product Roadmap"

Adapt profile sections:
- Clients: "Company Information", "Key Contacts", "Business Goals"
- Projects: "Project Overview", "Team Members", "Project Milestones"
- Products: "Product Description", "Features", "Release Timeline"

If profile section doesn't exist, gracefully skip or ask user.
```

**Source:** CONFIG.md structure + phase 4 entity-type awareness requirement

## State of the Art

| Old Approach (2024) | Current Approach (2026) | When Changed | Impact |
|---------------------|-------------------------|--------------|--------|
| Hardcoded prompts in CLAUDE.md | Agent Skills standard (SKILL.md) | Oct-Dec 2025 | Scalable skill libraries, cross-platform portability |
| Custom JSON skill format | YAML frontmatter + Markdown | Dec 2025 (standard published) | Human-readable, version-controllable, industry standard |
| `.claude/commands/` for slash commands | `.claude/skills/` (commands merged into skills) | Late 2025 | Unified model: one SKILL.md = natural language + slash command |
| Skills loaded fully at startup | Progressive disclosure (metadata only) | 2025-2026 | 95% token reduction at startup, enables 100+ skill libraries |
| Monolithic agent instructions | Multi-skill modular architecture | 2025-2026 | Composable workflows, easier maintenance, reusability |
| Manual skill invocation | Automatic description-based activation | 2025-2026 | Better UX, skills invisible to users |
| Single-entity operations | Batch operations as standard | 2026 | 30-50% time savings on repetitive workflows |
| Platform-specific skills | Cross-platform Agent Skills standard | Dec 2025 | Write once, works with Claude, Copilot, Codex, etc. |

**Deprecated/outdated:**
- **`.claude/commands/` directory:** Merged into skills; legacy support remains but new implementations should use skills
- **Custom slash command format:** Agent Skills standard replaces custom formats
- **Hardcoded skills in CLAUDE.md:** Should be extracted to SKILL.md files
- **Platform-specific skill formats:** Agent Skills standard achieves cross-platform compatibility

**Emerging trends (2026):**
- **Skill marketplaces:** Pre-certified skill libraries for common use cases (Zapier, Notion preparing to launch)
- **Skill composition:** Chaining multiple skills together for complex workflows
- **Agentic skill discovery:** AI agents automatically searching for and adapting skills online during entity onboarding
- **Agent economics:** Protocols for micro-transactions and resource allocation in multi-agent skill ecosystems

**Source:** [AI Agent Skills: Implementation Guide for 2026](https://medium.com/@gemQueenx/ai-agent-skills-complete-tutorials-implementation-guide-and-best-practices-for-2026-860e1ee9b7b8), [Agentic AI Trends 2026](https://www.salesmate.io/blog/future-of-ai-agents/)

## Open Questions

Things that couldn't be fully resolved:

1. **Skill Acquisition During Entity Onboarding**
   - What we know: Phase 4 requirement is "agent searches for prebuilt skills online, assesses quality, adapts as needed"
   - What's unclear: What sources to search? How to assess skill quality? How to prevent malicious skills?
   - Recommendation: Phase 4 should enable skill *creation* (agent writes SKILL.md for entity-specific workflows) but defer skill *discovery from external sources* to Phase 5 or later. Security and quality assessment are non-trivial. Start with agent authoring skills from scratch using skill-creator pattern.
   - Confidence: MEDIUM - skill acquisition is mentioned in requirements but security/quality concerns need investigation

2. **Skill Naming Conventions**
   - What we know: Skills use lowercase-with-hyphens (process-notes, weekly-review)
   - What's unclear: Should entity-specific skills use prefixes? `client-process-notes` vs `process-notes-client`?
   - Recommendation: Use action-first naming: `process-notes`, `get-status`, `weekly-review`. If entity-type specificity needed, use metadata field, not name prefix. Keep names short and intuitive for slash commands.
   - Confidence: HIGH - verified in examples from Claude Code docs

3. **Skill Version Conflicts Between Agents**
   - What we know: Multiple agents (Claude Code, Copilot) can load same skill
   - What's unclear: If skill in `.claude/skills/` differs from `.github/skills/`, which wins?
   - Recommendation: Phase 4 CONTEXT.md says "Claude's discretion: How to handle skill version conflicts between agents". Recommend: `.github/skills/` is canonical (project-level), `.claude/skills/` is override (agent-specific). If both exist for same skill name, `.claude/skills/` wins for Claude Code, `.github/skills/` used for other agents.
   - Confidence: LOW - not explicitly documented in Agent Skills spec, needs testing

4. **Batch Operation Parallelization**
   - What we know: Batch operations are standard in 2026 agentic workflows
   - What's unclear: Should skills process entities in parallel or serial? What about git conflicts?
   - Recommendation: Read-only operations (get-status, weekly-review) can parallelize. Write operations (process-notes, profile updates) must serialize to avoid git merge conflicts. Document this in skill instructions.
   - Confidence: MEDIUM - based on git behavior understanding, not verified in Agent Skills docs

5. **Proactive Skill Suggestion Phrasing**
   - What we know: Phase 4 CONTEXT.md says "Claude decides phrasing based on context (action-focused vs outcome-focused)"
   - What's unclear: Specific guidelines for when to use action-focused vs outcome-focused suggestions
   - Recommendation: Action-focused when user is stuck ("Try: process notes for Acme"). Outcome-focused when user has completed a workflow ("You might also want to: run weekly review to see overall progress"). Let Claude's judgment decide in context.
   - Confidence: HIGH - this is Claude's natural behavior, just needs documentation

6. **Skill Instruction Length Guidelines**
   - What we know: Recommendations say "under 5,000 tokens", "consider splitting into referenced files"
   - What's unclear: Exact threshold, how to measure tokens without running through tokenizer
   - Recommendation: Rough heuristic: 5,000 tokens ≈ 3,500 words ≈ 7 pages of prose. If SKILL.md body exceeds 3,500 words, move details to `references/` folder. Core workflow should fit in ~2 pages.
   - Confidence: MEDIUM - based on token estimation heuristics, not precise

## Sources

### Primary (HIGH confidence)
- [Agent Skills Specification](https://agentskills.io/specification) - Official open standard
- [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills) - Anthropic official docs
- [Use Agent Skills in VS Code](https://code.visualstudio.com/docs/copilot/customization/agent-skills) - Microsoft official docs
- [Spring AI Agentic Patterns: Agent Skills](https://spring.io/blog/2026/01/13/spring-ai-generic-agent-skills/) - Implementation guide for Java ecosystem
- [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices) - Anthropic engineering blog
- [Agent Skills Deep Dive](https://addozhang.medium.com/agent-skills-deep-dive-building-a-reusable-skills-ecosystem-for-ai-agents-ccb1507b2c0f) - Technical deep dive (Jan 2026)

### Secondary (MEDIUM confidence)
- [Claude Code Customization Guide](https://alexop.dev/posts/claude-code-customization-guide-claudemd-skills-subagents/) - Third-party comprehensive guide
- [Understanding Claude Code: Skills vs Commands](https://www.youngleaders.tech/p/claude-skills-commands-subagents-plugins) - Conceptual overview
- [How to Use Claude Code Features](https://www.producttalk.org/how-to-use-claude-code-features/) - Feature comparison guide
- [Claude Skills Guide](https://claude-world.com/articles/skills-guide/) - Community guide with examples
- [AI Agent Skills: Implementation Guide for 2026](https://medium.com/@gemQueenx/ai-agent-skills-complete-tutorials-implementation-guide-and-best-practices-for-2026-860e1ee9b7b8) - Comprehensive tutorial
- [Agentic AI Orchestration in 2026](https://onereach.ai/blog/agentic-ai-orchestration-enterprise-workflow-automation/) - Enterprise patterns
- [Agentic AI Trends 2026](https://www.salesmate.io/blog/future-of-ai-agents/) - Industry trends

### Tertiary (LOW confidence - flagged for validation)
- [awesome-claude-code GitHub](https://github.com/hesreallyhim/awesome-claude-code) - Community-curated resources (quality varies)
- [awesome-agent-skills GitHub](https://github.com/skillmatic-ai/awesome-agent-skills) - Community-curated skills (quality varies)
- [Building Agent Skills from Scratch](https://dev.to/onlyoneaman/building-agent-skills-from-scratch-lbl) - Tutorial (author credibility unknown)

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - Agent Skills specification is official, widely adopted, multi-platform support verified
- Architecture: HIGH - Patterns verified across Anthropic docs, VS Code docs, Spring AI implementation
- Pitfalls: MEDIUM-HIGH - Based on Claude Code docs, community guides, and common sense extrapolation from context budget limits
- Code examples: HIGH - Synthesized from official Agent Skills spec + existing project protocols (note-processing, etc.)
- Skill acquisition (external sources): LOW - Mentioned in requirements but security/quality concerns need deeper research

**Research date:** 2026-01-24
**Valid until:** 2026-02-24 (30 days - Agent Skills standard is new but stabilizing rapidly)

**Key dependencies versions (as of research date):**
- Agent Skills specification: 1.0 (published Dec 2025)
- Claude Code: Latest (supports Agent Skills standard)
- GitHub Copilot in VS Code: Latest (Agent Skills support rolled out Jan 2026)
- Minimum: YAML 1.2 support, Markdown rendering (GFM)

**Breaking changes to watch:**
- Agent Skills 2.0: May introduce new metadata fields or change discovery mechanisms
- Claude Code skill location migration: `.claude/skills/` may fully deprecate in favor of `.github/skills/`
- Cross-platform skill format divergence: If GitHub/Anthropic/OpenAI standards diverge, may need platform-specific adaptations

**Notes for planner:**
- Phase 4 requires no external libraries - pure markdown files
- Skills reference existing protocols (note-processing, entity-onboarding, etc.) - no duplication
- Progressive disclosure design means 100+ skills is feasible without token budget issues
- Batch operations are table stakes in 2026 - all skills should support multi-entity processing where applicable
- Skill acquisition from external sources needs security review - recommend deferring to later phase
