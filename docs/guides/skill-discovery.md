# Skill Discovery Guide

Skills are reusable workflows that the AI knows how to perform. You don't need to memorize them - just describe what you want and the AI figures it out. But if you're curious about what's available, this guide shows you how to discover and use skills.

## The Short Version

Ask the AI:
```
What skills do you have?
```

Or use the slash command:
```
/skills
```

You'll get a list of everything available.

## What Are Skills?

Skills are like recipes the AI follows. They encode best practices for common tasks so you don't have to explain the steps each time.

**Example:** The "process-notes" skill knows to:
1. Read notes from your input or the entity's notes folder
2. Extract tasks, profile facts, calendar items, and follow-ups
3. Add tasks to the entity's roadmap
4. Update their profile with new facts
5. Archive the processed notes

Without the skill, you'd have to explain all that every time. With the skill, you just say "process notes for Acme" and it handles everything.

## Built-in Skills

Agent PM comes with these skills:

### process-notes

**What it does:** Extracts actionable items from meeting notes and distributes them to the right places.

**How to use:**
- "Process notes for [entity]"
- `/process-notes [entity]`
- "I have notes from my meeting with [entity]"

**What it extracts:**
- Tasks - Added to entity roadmap
- Profile facts - Added to entity profile
- Calendar items - Flagged for your calendar
- Follow-ups - Listed for action

**Where notes come from:**
- Paste directly in chat
- Put in `entities/[Entity]/notes/` folder
- Put in top-level `NOTES.md` inbox

### get-status

**What it does:** Shows the current state of an entity - active tasks, recent updates, key info.

**How to use:**
- "Show me [entity]'s status"
- "What's happening with [entity]?"
- `/status [entity]`

**What you see:**
- Profile summary (key contacts, background)
- Active tasks from roadmap
- Recent notes and updates
- Upcoming deadlines if any

### weekly-review

**What it does:** Generates a summary across all entities showing what happened and what's coming up.

**How to use:**
- "Run weekly review"
- "Show me what's happening across all clients"
- `/weekly-review`

**What you get:**
- Entity-by-entity status
- Completed work this week
- Upcoming tasks
- Items needing attention

## How to Invoke Skills

You have two options:

### Natural Language (Recommended)

Just describe what you want:
```
Process notes for Acme
What's the status of Beta?
Generate a weekly review
```

The AI matches your intent to the right skill automatically.

### Slash Commands

If you prefer explicit commands:
```
/process-notes Acme
/status Beta
/weekly-review
```

Both work equally well. Use whatever feels natural.

## Batch Operations

Skills work on multiple entities at once:

```
Process notes for all clients
Show status for Acme, Beta, and Gamma
```

The AI processes each one and gives you a summary at the end.

## Finding More Skills

The built-in skills are just the start. You can:

### Ask What's Available

```
What skills do you have?
What can you help me with?
Show me available workflows
```

### Get Details on a Specific Skill

```
How does process-notes work?
Tell me more about weekly-review
```

### Check the Skill Folders

Skills are stored in:
- `.github/skills/` - Cross-platform skills
- `.claude/skills/` - Claude Code specific skills

Each skill has a SKILL.md file explaining what it does.

## Creating Your Own Skills

If you have a workflow you repeat often, you can turn it into a skill.

See the [SKILL_AUTHORING_GUIDE](../SKILL_AUTHORING_GUIDE.md) for the complete guide.

**Quick overview:**
1. Create a folder in `.github/skills/` with your skill name
2. Add a SKILL.md file with:
   - Name and description
   - Trigger phrases (how users invoke it)
   - Step-by-step instructions
3. The AI automatically picks it up

## Skills vs Specialists

You'll also hear about "specialists" - they're different:

| Skills | Specialists |
|--------|-------------|
| Procedures - step-by-step workflows | Experts - domain knowledge and judgment |
| Anyone could follow the steps | Requires expertise to do well |
| Process notes, get status, weekly review | Assessments, strategy planning |

**Skills** are like recipes anyone can follow.
**Specialists** are like hiring an expert consultant.

Both are available. Ask "What specialists do you have?" to see the experts.

## Common Questions

**Do I need to know skill names?**
No. Just describe what you want. The AI figures out which skill to use.

**What if I describe something that doesn't match a skill?**
The AI will try to help anyway using its general capabilities. Skills are shortcuts, not requirements.

**Can I modify built-in skills?**
Yes. Copy the skill folder from `.github/skills/` to `.claude/skills/` (or your agent's folder), then edit the SKILL.md. Your version takes priority.

**How do skills know about my entities?**
Skills read from the file structure. When you say "process notes for Acme", the skill looks in `entities/Acme Corp/` for the files it needs.

---

**Related guides:**
- [SKILL_AUTHORING_GUIDE](../SKILL_AUTHORING_GUIDE.md) - Create custom skills
- [Getting started](getting-started.md) - How the system works
- [Customization](customization.md) - Adapt for your industry
