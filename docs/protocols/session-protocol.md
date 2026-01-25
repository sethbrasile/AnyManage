# Session Protocol

Complete documentation for session initialization and state management.

## Startup Sequence

When a new session begins, follow these steps in order:

### Step 1: Load STATE.md

Check if `STATE.md` exists at the project root.

**If it exists:**
- Read the file
- Note the "Active Work" section for current context
- Check "Progress" for incomplete tasks
- Review "Context Files" for relevant files to have ready

**If it doesn't exist:**
- This is a fresh project or first session
- Offer to create it: "No session state found. Want me to create STATE.md?"
- If user agrees, create with the standard template

### Step 2: Check for PAUSE.md

Check if `PAUSE.md` exists at the project root.

**If it exists:**
- Read the pause reason and context
- Offer to resume: "You paused work on [task]. Resume where you left off?"
- If user says yes, load the paused context
- If user says no, archive or delete PAUSE.md

**If it doesn't exist:**
- No paused work, continue to ready state

### Step 3: Silent Ready

Do NOT:
- Greet the user ("Hello! How can I help?")
- Summarize project status unprompted
- List available commands
- Explain what you can do

DO:
- Wait silently for user instructions
- Be ready to act immediately when asked

### Step 4: Load Context On Demand

Don't preload everything. Load files as needed based on user requests:

| User Says | Load |
|-----------|------|
| "Process notes for Acme" | `entities/Acme/notes/`, templates |
| "Show all clients" | `ROADMAP.md`, `entities/` listing |
| "Update Acme's profile" | `entities/Acme/ENTITY_PROFILE.md` |

## Token Budget Guidelines

Keep startup context under 2,000 tokens:
- STATE.md: ~200-500 tokens
- PAUSE.md (if exists): ~100-300 tokens
- Total startup: <1,000 tokens ideal

Load additional context as needed during the session.

## STATE.md Format

```markdown
# Current Work State

**Last Updated:** 2026-01-24 3:30 PM

## Active Work
Processing notes for Acme Corp

## Progress
- [x] Read meeting notes
- [-] Extract action items
- [ ] Update ENTITY_ROADMAP.md

## Last Action
Created entities/Acme/ folder structure

## Next Steps
- Complete note processing
- Review extracted tasks with user

## Context Files
- entities/Acme/notes/2026-01-24.md
- entities/Acme/ENTITY_ROADMAP.md
```

## PAUSE.md Format

Created when user pauses mid-task:

```markdown
# Paused Work

**Paused:** 2026-01-24 3:45 PM
**Reason:** User stepping away

## What Was Happening
Processing notes for Acme Corp - halfway through extracting action items.

## Resume Instructions
1. Open entities/Acme/notes/2026-01-24.md
2. Continue from "Budget Discussion" section
3. Complete action item extraction

## Files In Progress
- entities/Acme/ENTITY_ROADMAP.md (partially updated)
```

## Rationale

**Why silent start?**
- Non-technical users don't need explanations
- Reduces cognitive load
- Faster to actual work
- Professional, not chatty

**Why minimal preload?**
- Saves tokens for actual work
- Avoids loading irrelevant context
- Scales to large projects
- Faster session initialization
