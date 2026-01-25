# Skill Discovery Protocol

**Purpose:** Define how users discover available skills and how agents expose skill capabilities.

**Principle:** Skills are invisible to users by default. Users talk naturally; agents decide when to activate skills. Discovery is optional for curious users who want to understand agent capabilities.

---

## Trigger Detection

### Natural Language Triggers

Users may ask about skills using various phrasings:

| User Says | Intent |
|-----------|--------|
| "What skills do you have?" | Discovery |
| "Show me your skills" | Discovery |
| "What can you do?" | Discovery |
| "What workflows are available?" | Discovery |
| "Help" (in skill context) | Discovery |

### Slash Command Trigger

If supported by the agent:
- `/skills` - List all available skills
- `/skills [name]` - Show details for specific skill

---

## Discovery Response Format

When users ask to see available skills, respond with a categorized, user-friendly list:

```
I have several workflows available to help you:

**Managing Entities:**
- Process notes - Extract tasks and profile updates from meeting notes
- Get status - Show current state of entities
- Weekly review - Generate status report across all entities

**Best part:** You don't need to remember these. Just describe what you want in plain English.

Want details on a specific skill? Ask "How does [skill name] work?"
```

### Response Guidelines

1. **Group by category** - Organize skills into logical groups (Managing Entities, Working with Profiles, etc.)
2. **One-line descriptions** - Each skill gets name + brief description
3. **Reassure the user** - Remind them natural language works fine
4. **Offer deep dive** - Let them ask for specific skill details

---

## How Skills Work

### Invisible by Default

Skills are implementation details, not user-facing concepts:

- Users express intent naturally ("I have notes from my meeting with Acme")
- Agent matches intent to skill descriptions automatically
- Agent activates skill and executes workflow
- User never needs to know a "skill" was involved

### Dual Invocation

Both patterns work equally:

| Natural Language | Slash Command |
|------------------|---------------|
| "Process notes for Acme" | `/process-notes Acme` |
| "What's the status of Beta?" | `/status Beta` |
| "Run weekly review" | `/weekly-review` |

### Skills Reference Protocols

Skills invoke detailed protocols - they don't duplicate them:

```
SKILL.md (brief)
  └── References docs/protocols/note-processing.md (detailed)
```

This prevents divergence between skill instructions and protocol documentation.

---

## Skill Loading Protocol

### Progressive Disclosure

Agents load skills efficiently to minimize context usage:

1. **Startup:** Scan skill directories for SKILL.md files
2. **Load metadata only:** Read YAML frontmatter (name + description)
3. **Token budget:** ~50 tokens per skill for metadata
4. **On match:** When user intent matches description, load full SKILL.md
5. **Full skill:** 2,000-5,000 tokens for active skill instructions
6. **Access resources:** Load scripts/, references/, assets/ only if needed

### Token Budget Example

| Skills | Startup Cost | Per-Activation |
|--------|--------------|----------------|
| 10 skills | ~500 tokens | ~3,000 tokens |
| 50 skills | ~2,500 tokens | ~3,000 tokens |
| 100 skills | ~5,000 tokens | ~3,000 tokens |

---

## Skill Locations

Skills are stored in standardized directories:

| Location | Purpose | When to Use |
|----------|---------|-------------|
| `.github/skills/` | Cross-platform (recommended) | Default for portability |
| `.claude/skills/` | Claude Code specific | Agent-specific overrides |
| `.opencode/skills/` | opencode specific | Agent-specific overrides |

### Override Priority

If the same skill exists in multiple locations:

1. Agent-specific directory wins for that agent
2. `.github/skills/` is fallback for all agents

Example:
- `.github/skills/process-notes/SKILL.md` exists
- `.claude/skills/process-notes/SKILL.md` also exists
- Claude Code uses `.claude/` version
- Other agents use `.github/` version

---

## Batch Operations

Skills support batch processing across multiple entities:

### Triggering Batch Operations

| User Says | Interpretation |
|-----------|----------------|
| "Process notes for all clients" | All entities |
| "Weekly review for Acme, Beta, Gamma" | Specific entities |
| "Get status for everything" | All entities |
| "Process notes for Acme" | Single entity |

### Progress Feedback

During batch operations, show progress:

```
Processing 3 entities... (1/3 complete)
  [Acme Corp] 4 tasks extracted, profile updated
  [Beta Industries] Processing...
```

### Aggregated Results

After batch completes, summarize:

```
Processed 3 entities:
- Total tasks added: 12
- Profiles updated: 3
- Notes archived: 5

See entity roadmaps for details.
```

### Serialization

- **Read-only operations** (get-status, weekly-review): Can parallelize
- **Write operations** (process-notes, profile updates): Must serialize to avoid git conflicts

---

## Error Handling

### Skill Not Found

When no skill matches user intent:

1. Attempt to help using general agent capabilities
2. If topic is outside agent scope, explain limitations
3. Suggest related skills if any seem relevant

Example response:
```
I don't have a specific workflow for that, but I can try to help.
[Attempts general solution]

Related workflows you might find useful:
- Process notes - for extracting tasks from meeting notes
- Get status - for checking entity progress
```

### Multiple Matches

When user intent matches multiple skills:

1. Pick best match if clearly stronger
2. If ambiguous, ask for clarification:

```
I can help with that a couple ways:
- **Process notes** - Extract tasks from your meeting notes
- **Add facts** - Update entity profile with specific information

Which would you like?
```

### Skill Execution Fails

When a skill fails during execution:

1. Log error to `ops/logs/` (per action-logging protocol)
2. Report to user in plain English (no technical details)
3. Suggest alternatives or manual workarounds

Example:
```
I ran into a problem processing those notes. The file format wasn't what I expected.

You can:
- Paste the notes directly in chat and I'll try again
- Check the file is plain text (not PDF or Word)
- Manually add tasks to the entity's roadmap
```

---

## Skill Detail Request

When user asks "How does [skill] work?":

Provide brief explanation with examples:

```
**Process Notes** extracts actionable items from meeting notes.

What it does:
1. Reads notes you paste or point to in entity's notes/ folder
2. Extracts tasks, profile facts, calendar items, and follow-ups
3. Adds tasks to entity's ROADMAP.md
4. Updates profile with new facts
5. Archives processed notes

Example:
"Process notes for Acme" → Extracts 5 tasks, updates 2 profile fields, archives note

Full details: docs/protocols/note-processing.md
```

---

## Integration with Existing Protocols

Skills are thin wrappers that invoke existing protocols:

| Skill | Invokes Protocol |
|-------|------------------|
| process-notes | `docs/protocols/note-processing.md` |
| entity onboarding | `docs/protocols/entity-onboarding.md` |
| profile updates | `docs/protocols/profile-building.md` |
| get-status | Reads entity ROADMAP.md directly |
| weekly-review | Aggregates across entity ROADMAPs |

This ensures:
- Single source of truth for detailed procedures
- Skills stay concise (under 5,000 tokens)
- Protocol updates automatically improve skills

---

## PM Base Skills

The following skills are standard for Agent PM:

| Skill | Description | Batch Support |
|-------|-------------|---------------|
| process-notes | Extract tasks from meeting notes | Yes |
| get-status | Show entity status | Yes |
| weekly-review | Generate weekly status report | Yes (default: all) |

These skills are created in Plan 04-02.

---

*Protocol: skill-discovery*
*Created: 2026-01-24*
