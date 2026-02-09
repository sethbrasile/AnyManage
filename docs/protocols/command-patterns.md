# Command Patterns

How the agent interprets and executes user commands.

## Dual Format Support

Users can give commands in two formats:

### Natural Language
Conversational requests that the agent interprets:
- "Process the notes for Acme Corp"
- "Show me what's happening with Beta Industries"
- "Create a new client called Gamma Solutions"

### Slash Commands
Structured commands for power users:
- `/process-notes Acme`
- `/status Beta`
- `/new Gamma`

Both formats trigger the same underlying workflow.

## Command Parsing

### Entity Extraction

The agent identifies entity names from natural language:

| User Says | Extracted Entity |
|-----------|------------------|
| "Process notes for Acme Corp" | Acme Corp |
| "Update the Acme profile" | Acme |
| "Show Beta Industries status" | Beta Industries |

**Matching rules:**
1. Exact match in `entities/` folder
2. Case-insensitive match
3. Partial match (ask for confirmation)
4. No match (ask user to clarify)

### Batch Operations

Multiple entities in one command:

**Natural language:**
- "Process notes for Acme, Beta, and Gamma"
- "Show status of all high-priority clients"

**Slash command:**
- `/process-notes Acme Beta Gamma`
- `/status --priority high`

**Batch behavior:**
1. Parse all entity names
2. Show execution plan: "Will process notes for: Acme, Beta, Gamma"
3. Ask for confirmation: "Proceed?"
4. Execute sequentially, report results

## Confirmation Rules

### Always Confirm (Destructive)
- Delete entity or files
- Overwrite existing content
- Bulk changes affecting multiple entities
- Archive or remove from dashboard

### Execute Immediately (Safe)
- Read operations (show, list, status)
- Create operations (new entity, add note)
- Process operations (extract tasks, generate updates)
- Single-entity updates

### Batch Confirmation
For operations on multiple entities:
1. Show what will happen
2. Ask once for the batch
3. Execute all if approved

## Command Reference

| Intent | Natural Language | Slash Command |
|--------|------------------|---------------|
| Process notes | "Process notes for X" | `/process-notes X` |
| Show status | "Show status of X" | `/status X` |
| Create entity | "Create new client X" | `/new X` |
| Update profile | "Add [fact] to X's profile" | `/update-profile X` |
| List entities | "Show all clients" | `/list` |
| Weekly review | "Run weekly review" | `/weekly-review` |
| Rebuild index | "Rebuild index" | `/rebuild-index` |

## Namespace Conventions

Future skills will use namespaced commands:

```
/pm:process-notes    <- Project management skill
/report:weekly       <- Reporting skill
/sync:trello         <- Integration skill
```

This prevents conflicts as the skill library grows.

## Error Handling

**Entity not found:**
```
I couldn't find "Acma" in your entities.
Did you mean "Acme Corp"?
```

**Ambiguous command:**
```
I found multiple matches for "Beta":
1. Beta Industries
2. Beta Testing Project

Which one did you mean?
```

**Missing required info:**
```
To process notes, I need to know which client.
Which client's notes should I process?
```

## Rebuild Index Command

Regenerates all derived files (digests and cross-entity index) from source files.

### Trigger Detection

| Pattern | Example |
|---------|---------|
| "Rebuild index" | "Rebuild index" |
| "Refresh index" | "Refresh the index" |
| "Rebuild digests" | "Rebuild all digests" |
| `/rebuild-index` | `/rebuild-index` |

### What It Does

1. For each entity in `entities/`:
   - Read ENTITY_PROFILE.md, ENTITY_ROADMAP.md, and knowledge/LEARNED_CONTEXT.md
   - Generate fresh `DIGEST.md` from current state
2. Generate fresh `.index/CROSS_ENTITY_INDEX.md` from all entity data
3. Generate fresh `.index/TOPICS.md` from all entity digests and knowledge files (if topic index exists)
4. Report: "Index rebuilt for N entities"

### When It Runs Automatically

- **Weekly review** always includes a full index refresh as its final step
- **Entity onboarding** creates an initial DIGEST.md for the new entity
- **Note processing** incrementally updates the relevant entity's digest and the cross-entity index

### When to Run Manually

- After importing or manually editing entity files outside the agent
- If digests or indexes seem out of date
- After recovering from errors

### Graceful Behavior

- If no entities exist: "No entities found — nothing to index."
- If a single entity file is corrupted: skip it, rebuild the rest, report which entity was skipped
