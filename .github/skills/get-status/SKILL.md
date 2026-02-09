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

### Single Entity

1. **Identify target entity**
   - "Status of Acme" → single entity
2. **Read entity DIGEST.md** (fast path)
   - If `entities/[Name]/DIGEST.md` exists, use it for the summary
   - This gives Active Work, Key Context, Upcoming Deadlines, and Recent Activity in ~40-60 lines
   - If the user asks follow-up questions needing more detail, then read full ROADMAP.md and PROFILE.md
3. **Fallback: Read ROADMAP.md + PROFILE.md**
   - If DIGEST.md doesn't exist, read the full source files (pre-index behavior)
   - Extract tasks with `- [ ]` (todo), `- [-]` (in progress), `- [x]` (completed)
   - Note deadlines, key dates from profile
4. **Format status summary**
   - Group by status (active, in-progress, completed)
   - Highlight items needing attention
   - Suggest next steps

### All Entities / Multiple Entities

1. **Read `.index/CROSS_ENTITY_INDEX.md`** (fast path)
   - If the cross-entity index exists, use it for the overview
   - Entity Summary table gives phase, next deadline, status, and task count for every entity
   - This replaces reading every individual roadmap
2. **Fallback: Read all ROADMAP.md files**
   - If the index doesn't exist, read `entities/*/ENTITY_ROADMAP.md` for each entity
3. **Format multi-entity summary**
   - Group by activity level or attention needed
   - Highlight entities with overdue tasks or no recent updates

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

- Cross-entity index: `.index/CROSS_ENTITY_INDEX.md` (fast overview for multi-entity queries)
- Entity digests: `entities/[Name]/DIGEST.md` (fast summary for single-entity queries)
- Entity roadmap: `entities/[Name]/ENTITY_ROADMAP.md` (full detail, deep-dive as needed)
- Entity profile: `entities/[Name]/ENTITY_PROFILE.md`

---

*Skill: get-status*
*Version: 1.0*
