---
name: get-status
description: Show current status of an entity or all entities. Use when the user asks "what's the status", "show me updates", wants to see progress for clients/projects, asks "how is [entity] doing", or wants a status check.
metadata:
  author: agent-pm
  version: "1.0"
  compatible_entity_types: ["client", "project", "product"]
---

# Get Status Skill

Display current state of entities: active tasks, in-progress items, recent completions, and next steps.

## When to Use

Trigger this skill when user:
- Asks "What's the status of [entity]?"
- Says "Show me updates for [entity]"
- Asks "What's happening with [entity]?"
- Says "How is [entity] doing?"
- Asks "What's the progress on [entity]?"
- Wants a quick overview of entity state
- Says "status check" or "status update"

## How It Works

1. **Identify target entity(ies)**
   - Single entity: "Status of Acme"
   - Multiple entities: "Status of Acme and Beta"
   - All entities: "Status of all clients"
   - No entity specified: Ask user or default to all

2. **Read entity ROADMAP.md**
   - Extract tasks with `- [ ]` (todo)
   - Extract tasks with `- [-]` (in progress)
   - Extract tasks with `- [x]` (completed)
   - Note deadlines if present

3. **Read entity PROFILE.md**
   - Get last updated timestamp
   - Note key dates or upcoming events

4. **Format status summary**
   - Group by status (active, in-progress, completed)
   - Highlight items needing attention
   - Suggest next steps

## Output Format (Single Entity)

```
Status: [Entity Name]

Active Work (3 tasks):
- [ ] Follow up on budget proposal (by Friday)
- [-] Security audit in progress (started 01/20)
- [ ] Schedule quarterly review

Recently Completed:
- [x] Send revised timeline (completed 01/22)
- [x] Initial kickoff meeting (completed 01/15)

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

Needs Attention:
- Zeta Corp: 3 overdue tasks
```

## Batch Processing

Supports status checks across multiple entities:

- **Specific entities:** "Get status for Acme, Beta"
- **All entities:** "Get status for all clients" or "What's happening across all projects?"
- **Single entity:** "Status of Acme"
- **No entity specified:** Ask user which entity, or offer all-entities summary

**Progress feedback for batch operations:**
```
Checking status for 5 entities...
```

## Entity Type Awareness

Read entity type from `CONFIG.md` if present:
- Use appropriate terminology (client, project, product)
- Adapt summary framing to entity type

## Status Indicators

| Marker | Status | Display |
|--------|--------|---------|
| `- [ ]` | Todo | Active task |
| `- [-]` | In Progress | Currently working on |
| `- [x]` | Completed | Done |
| Deadline passed | Overdue | Needs attention (highlight) |
| No updates 7+ days | Quiet | May need check-in |

## Attention Flags

Automatically flag entities that need attention:

- **Overdue tasks:** Deadline has passed
- **Stale entities:** No updates in 7+ days
- **High task count:** More than 10 active tasks
- **No progress:** All tasks still in todo state

## Error Handling

- **Entity not found:** "No entity named '[name]' found. Did you mean [similar]?"
- **No entities exist:** "No entities found. Create one with 'add new client [name]'"
- **Empty roadmap:** "No tasks found for [entity]. Add some with 'add task to [entity]'"

## Slash Command

`/get-status [entity]` or `/status [entity]`

Example: `/status Acme Corp`

## Related Files

- Entity roadmap: `entities/[Name]/ENTITY_ROADMAP.md`
- Entity profile: `entities/[Name]/ENTITY_PROFILE.md`

---

*Skill: get-status*
*Version: 1.0*
