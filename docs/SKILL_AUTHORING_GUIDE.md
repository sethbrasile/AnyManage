# Skill Authoring Guide

Learn how to create custom skills that automate your workflows.

---

## What Are Skills?

Skills are reusable workflows that teach the AI agent how to perform specific tasks. When you create a skill, you're essentially writing instructions that the agent follows when triggered.

**Key points:**
- Skills live in a single file called `SKILL.md`
- Skills are triggered automatically by natural language (you don't need to memorize commands)
- Skills can also be invoked with slash commands for power users

**Example:** You create a "weekly-report" skill. Now when you say "run my weekly report" or "what happened this week?", the agent knows exactly what to do.

---

## Quick Start

Create your first skill in 3 steps:

### Step 1: Create the skill folder

```
.github/skills/my-skill-name/
  SKILL.md
```

### Step 2: Write the SKILL.md file

```markdown
---
name: my-skill-name
description: Brief description of what this skill does. Include trigger phrases like "Use when user asks for X, says Y, or wants Z".
---

# My Skill Name

Explain what this skill does and when to use it.

## Instructions

1. First, do this
2. Then, do that
3. Finally, complete with this

## Output Format

Show what the result should look like.
```

### Step 3: Test it

Just ask the agent to do the thing your skill describes. If the description matches your request, the skill activates automatically.

---

## SKILL.md Format

Every skill has two parts: **frontmatter** (metadata) and **body** (instructions).

### Minimal Example

```markdown
---
name: daily-standup
description: Generate daily standup notes. Use when user asks for standup, daily update, or what they worked on.
---

# Daily Standup

Generate a summary of recent work for standup meetings.

## Instructions

1. Look at recent commits and file changes
2. Summarize what was completed
3. List any blockers
4. Note what's planned for today
```

### Complete Example

```markdown
---
name: process-notes
description: Process meeting notes into structured tasks and profile updates. Use when the user says "process notes", mentions extracting tasks from notes, or wants to update entity information from meeting notes.
metadata:
  author: your-name
  version: "1.0"
  compatible_entity_types: ["client", "project", "product"]
---

# Process Notes Skill

Extract tasks, profile facts, calendar items, and follow-ups from meeting notes.

## When to Use

- User says "process notes for [entity]"
- User mentions extracting tasks from notes
- User pastes meeting notes and wants them organized

## How It Works

Follow the detailed protocol in `docs/protocols/note-processing.md`.

**Summary:**
1. Identify input source (pasted content, entity notes folder, or NOTES.md)
2. Extract tasks, profile facts, calendar items, follow-ups
3. Add tasks to entity's ROADMAP.md
4. Update profile with new facts
5. Archive processed notes with timestamp

## Batch Processing

Supports processing multiple entities at once:
- "Process notes for Acme, Beta, Gamma" - specific entities
- "Process notes for all clients" - all entities
- "Process notes for Acme" - single entity

Show progress during batch operations:
```
Processing 3 entities... (1/3 complete)
  [Acme Corp] 4 tasks extracted, profile updated
  [Beta Industries] Processing...
```

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

- Notes archived to: entities/[Name]/notes/archive/meeting-notes.md
```

## Error Handling

- Entity not found: Offer to create entity first
- No notes found: Ask user to paste or specify file
- Ambiguous content: Ask for clarification
```

---

## The Description Field

**This is the most important part of your skill.**

The `description` field determines when your skill activates. Write it from the user's perspective - how would they naturally ask for this?

### Good Descriptions

```yaml
# Good: Includes trigger phrases
description: Generate weekly status report across all entities. Use when the user asks for a "weekly review", "status update", or wants to see progress across all clients/projects.

# Good: Multiple natural phrasings
description: Show current status of an entity or all entities. Use when the user asks "what's the status", "show me updates", or wants to see progress for clients/projects.

# Good: Specific context
description: Process meeting notes into tasks. Use when user says "process notes", "extract tasks from notes", "I have meeting notes", or pastes text and asks to organize it.
```

### Bad Descriptions

```yaml
# Bad: Too vague
description: Process notes.

# Bad: Missing trigger phrases
description: Extracts tasks from notes and updates profiles.

# Bad: Technical language users won't say
description: Invokes note-processing protocol to parse unstructured text.
```

### Testing Your Description

Ask yourself: "If I said [phrase], would I expect this skill to activate?"

Test with variations:
- "process my notes" - should match "process notes" skill
- "I have notes from my meeting" - should match
- "what tasks are in these notes?" - should match
- "show me the notes" - should NOT match (this is viewing, not processing)

---

## Skill Storage Locations

Skills can be stored in different locations for different purposes:

### Cross-Platform (Recommended)

```
.github/skills/skill-name/SKILL.md
```

This location works with Claude Code, GitHub Copilot, VS Code, and other tools that support the Agent Skills standard.

### Agent-Specific

```
.claude/skills/skill-name/SKILL.md     # Claude Code only
.opencode/skills/skill-name/SKILL.md   # opencode only
```

Use agent-specific locations when:
- You need different behavior per agent
- You want to override a cross-platform skill for one agent

### Override Priority

If the same skill exists in multiple locations:
1. Agent-specific directory wins for that agent
2. `.github/skills/` is the fallback

Example:
- `.github/skills/weekly-review/SKILL.md` exists (cross-platform version)
- `.claude/skills/weekly-review/SKILL.md` also exists (Claude-specific version)
- Claude Code uses the `.claude/` version
- Other agents use the `.github/` version

### Personal Skills

Skills can also live in your home directory for personal use across projects:
- `~/.copilot/skills/` - Personal skills (not committed to repo)

---

## Referencing Existing Protocols

Don't duplicate detailed procedures. Reference existing protocol documents:

```markdown
## How It Works

See `docs/protocols/note-processing.md` for the complete step-by-step protocol.

This skill implements that protocol with entity-specific handling.
```

**Why reference instead of duplicate?**
- Single source of truth - updates in one place
- Skills stay concise (better for token budget)
- No divergence between skill and protocol

### Protocol Reference Pattern

```markdown
# In SKILL.md

## Detailed Protocol

This skill follows `docs/protocols/[protocol-name].md`.

Key points for this skill:
- Point 1 specific to this skill
- Point 2 specific to this skill
```

See the skill discovery protocol at `docs/protocols/skill-discovery.md` for how skills interact with the discovery system.

---

## Supporting Batch Operations

Many skills should support processing multiple entities at once.

### Detecting Batch Requests

| User Says | Interpretation |
|-----------|----------------|
| "Process notes for all clients" | All entities |
| "Weekly review for Acme, Beta, Gamma" | Specific entities |
| "Get status for everything" | All entities |
| "Process notes for Acme" | Single entity |

### Progress Feedback

Always show progress during batch operations:

```
Processing 5 entities... (2/5 complete)
  [Acme Corp] Done - 4 tasks extracted
  [Beta Industries] Done - 2 tasks extracted
  [Gamma LLC] Processing...
```

### Aggregated Results

After batch completes, summarize:

```
Processed 5 entities:
- Total tasks added: 18
- Profiles updated: 4
- Notes archived: 7

See entity roadmaps for details.
```

### Write vs Read Operations

- **Read-only** (get-status, weekly-review): Can run in parallel
- **Write operations** (process-notes, profile updates): Run sequentially to avoid conflicts

---

## Entity Type Awareness

Skills can adapt behavior based on entity type (client, project, product, etc.).

### Reading Entity Type

Read from the entity's CONFIG.md:

```yaml
entity_type: client
```

### Adapting Terminology

Adjust output language based on type:
- `client` - "Client Background", "Client Goals"
- `project` - "Project Overview", "Project Milestones"
- `product` - "Product Description", "Release Timeline"

### Specifying Compatibility

In your skill's metadata:

```yaml
---
name: process-notes
description: Process notes for entities.
metadata:
  compatible_entity_types: ["client", "project", "product"]
---
```

---

## Best Practices

### DO

- **Write descriptions from the user's perspective** - Include natural phrases they would actually say
- **Keep skills focused** - One skill, one purpose
- **Reference existing protocols** - Don't duplicate detailed procedures
- **Support batch operations** - If it makes sense for multiple entities
- **Show progress** - Users should know what's happening
- **Handle errors gracefully** - Offer alternatives, not just error messages
- **Test with multiple phrasings** - Make sure your description catches common variations

### DON'T

- **Don't use technical jargon in descriptions** - Users say "process my notes", not "invoke note extraction pipeline"
- **Don't create massive skills** - Keep under 5,000 tokens; split into referenced files if needed
- **Don't hardcode entity types** - Read from CONFIG.md instead
- **Don't ask for confirmation on non-destructive operations** - Just do it (adds, creates, processes)
- **Don't duplicate protocol logic** - Reference docs/protocols/ instead
- **Don't require slash commands** - Natural language should always work

---

## Token Budget

Skills are loaded progressively to save context space:

| Stage | What's Loaded | Token Cost |
|-------|--------------|------------|
| Startup | Name + description only | ~50 tokens per skill |
| Activated | Full SKILL.md content | 2,000-5,000 tokens |
| As needed | Referenced files, scripts | Variable |

### Guidelines

- **Keep descriptions under 300 characters** - Just enough for triggering
- **Keep full skill under 5,000 tokens** - About 3,500 words or 7 pages
- **Move details to references/** - If your skill needs more, create supporting files

### Skill Folder Structure

For complex skills:

```
.github/skills/my-skill/
  SKILL.md           # Core skill (required)
  references/        # Detailed documentation (optional)
  scripts/           # Helper scripts (optional)
  assets/            # Templates, data files (optional)
```

---

## Testing Your Skill

### Manual Testing

1. Start a new session with the agent
2. Try natural language: "Do the thing my skill does"
3. Verify the skill activated (you'll see it following your instructions)
4. Try variations of the trigger phrase
5. Test edge cases (missing data, batch operations, errors)

### Checklist

- [ ] Skill activates from natural language request
- [ ] Skill activates from variations of the request
- [ ] Slash command works (if you defined one)
- [ ] Output format matches documentation
- [ ] Batch operations work (if supported)
- [ ] Errors are handled gracefully
- [ ] Entity type is respected (if applicable)

---

## Common Pitfalls

### 1. Description Doesn't Include Trigger Language

**Problem:** User says "process my notes" but skill doesn't activate.

**Cause:** Description only says "Process notes" without mentioning how users ask.

**Fix:** Include trigger phrases:
```yaml
description: Process notes. Use when user says "process notes", "extract tasks from notes", or "I have meeting notes".
```

### 2. Skill Too Long

**Problem:** Skill exceeds token budget, gets truncated or excluded.

**Fix:**
- Move detailed procedures to `references/` folder
- Reference existing protocols in `docs/protocols/`
- Keep SKILL.md focused on when/what, not every detail of how

### 3. Hardcoded Entity Names

**Problem:** Skill assumes entities are always "clients" but user has "projects".

**Fix:** Read entity type from CONFIG.md:
```markdown
## Entity Type Handling

Read entity type from entity's CONFIG.md and adapt terminology accordingly.
```

### 4. No Progress Feedback

**Problem:** Batch operation runs silently for 2 minutes, user thinks it's stuck.

**Fix:** Add progress updates:
```
Processing 10 entities... (4/10 complete)
```

### 5. Duplicating Protocol Logic

**Problem:** SKILL.md has 500 lines of detailed steps. Protocol file has the same 500 lines. They diverge over time.

**Fix:** Reference, don't duplicate:
```markdown
## How It Works

See `docs/protocols/note-processing.md` for complete protocol.
```

### 6. Asking Permission for Everything

**Problem:** "Process notes for Acme? (yes/no)" before every single operation.

**Fix:** Only confirm destructive operations (delete, overwrite). Execute adds/creates immediately.

---

## Example: Creating a Custom Skill

Let's create a skill for generating client reports.

### Step 1: Plan the skill

- **Name:** client-report
- **Purpose:** Generate a summary report for a specific client
- **Triggers:** "generate report for X", "client report", "summary for X"

### Step 2: Create the file

Create `.github/skills/client-report/SKILL.md`:

```markdown
---
name: client-report
description: Generate a summary report for a client. Use when user asks for "client report", "generate report for [client]", or "summary for [client]".
metadata:
  compatible_entity_types: ["client"]
---

# Client Report Skill

Generate a comprehensive summary report for a client entity.

## When to Use

- User asks for "client report for Acme"
- User says "generate a summary for Beta"
- User wants "report on what's happening with Gamma"

## Instructions

1. Read the client's ENTITY_PROFILE.md for background
2. Read the client's ENTITY_ROADMAP.md for tasks
3. Compile into a structured report

## Output Format

Generate a report like this:

---

# Client Report: [Client Name]

**Generated:** [Today's date]

## Overview

[Brief summary from profile - who they are, what they do]

## Current Status

### Active Work
- [ ] Task 1
- [-] Task 2 (in progress)
- [ ] Task 3

### Recently Completed
- [x] Completed task 1
- [x] Completed task 2

## Key Contacts

| Name | Role | Notes |
|------|------|-------|
| [Name] | [Role] | [Notes] |

## Next Steps

[Recommendations based on current status]

---
```

### Step 3: Test

- "Generate a client report for Acme" - should activate
- "What's the summary for Beta?" - should activate
- "Show me Acme's profile" - should NOT activate (that's viewing profile, not generating report)

---

## Summary

Creating effective skills:

1. **Name clearly** - lowercase-with-hyphens, action-focused
2. **Describe thoroughly** - Include trigger phrases users actually say
3. **Store appropriately** - `.github/skills/` for cross-platform
4. **Reference protocols** - Don't duplicate detailed procedures
5. **Support batch** - If applicable to multiple entities
6. **Handle errors** - Gracefully, with alternatives
7. **Stay concise** - Under 5,000 tokens, split if needed

For more on how skills are discovered and loaded, see `docs/protocols/skill-discovery.md`.

---

*Guide version: 1.0*
*Last updated: 2026-01-24*
