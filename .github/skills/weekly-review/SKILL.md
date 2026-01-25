---
name: weekly-review
description: Generate weekly status report across all entities. Use when the user asks for a "weekly review", "weekly summary", "status update across all clients", wants to see progress across entities, asks "what got done this week", or wants end-of-week status check. Automatically runs for all entities unless specific ones are mentioned.
metadata:
  author: agent-pm
  version: "1.0"
  compatible_entity_types: ["client", "project", "product"]
---

# Weekly Review Skill

Generate comprehensive weekly status report showing progress, completions, and items needing attention across all entities.

## When to Use

Trigger this skill when user:
- Says "run weekly review"
- Asks "Show me this week's progress"
- Says "What got done across all my clients?"
- Asks for "weekly summary" or "weekly status"
- Wants end of week status check
- Says "weekly update" or "what happened this week"
- Asks "what's been accomplished lately"

## How It Works

1. **Scan all entity ROADMAP.md files**
   - Read `entities/*/ENTITY_ROADMAP.md` for all entities
   - Get entity names from folder names

2. **Extract tasks by status**
   - Completed this week: `[x]` items with date in past 7 days
   - In progress: `[-]` items
   - Active: `[ ]` items
   - Overdue: Items with deadline in past

3. **Identify entities needing attention**
   - Overdue tasks (deadline passed)
   - No updates in 7+ days
   - High task count (10+ active tasks)
   - No progress this week

4. **Generate aggregated summary**
   - Total counts across all entities
   - Per-entity breakdown
   - Attention flags and recommended actions
   - Next week priorities

## Output Format

```
Weekly Review: [Date Range]

Summary:
- Total entities: 12
- Active tasks: 47
- Completed this week: 23
- Entities needing attention: 3 (Beta Industries, Zeta Corp, Epsilon Group)

Completed This Week (23 tasks):

[Acme Corp]
- [x] Send revised budget proposal (01/22)
- [x] Schedule Q2 kickoff meeting (01/23)

[Beta Industries]
- [x] Complete security audit (01/20)
- [x] Submit compliance documentation (01/24)

[... more entities ...]

In Progress (12 tasks):

[Acme Corp]
- [-] Security audit in progress (started 01/20)

[... more entities ...]

Needs Attention:
- [Beta Industries] Overdue: Follow up on proposal (due 01/23)
- [Zeta Corp] No updates in 10 days (last: 01/14)
- [Epsilon Group] 3 tasks pending, no progress this week

Next Week Priorities:
- Follow up on overdue items (Beta, Epsilon)
- Check in with Zeta (no recent updates)
- Continue in-progress work (Acme security audit)
```

## Customization Options

User can customize the review scope:

- **High-priority only:** "Weekly review for high-priority clients only"
- **Specific entities:** "Weekly review for Acme and Beta"
- **Completed only:** "Weekly review showing only completed items"
- **In-progress only:** "Show what's currently in progress"

## Date Detection

Look for dates in task text to determine completion timing:

- Explicit dates: "(01/22)", "(completed Jan 22)", "(done 2026-01-22)"
- Relative dates: "(completed Friday)", "(done yesterday)"
- Due dates: "(by Friday)", "(due Jan 24)"

**Fallback:** If no date in task text, use file modification time.

**"This week" definition:** Past 7 days from current date.

## Batch Processing

The weekly review inherently operates across all entities:

- **Default:** "Run weekly review" -> all entities
- **Filtered:** "Weekly review for Acme, Beta" -> specific entities
- **By type:** "Weekly review for all projects" -> filter by entity type

**Progress feedback:**
```
Generating weekly review...
Scanning 12 entities...
```

## Entity Type Awareness

Read entity type from `CONFIG.md` if present:
- Group entities by type in report if mixed types exist
- Use appropriate terminology (clients vs projects vs products)

## Attention Flags

Automatically flag items that need attention:

| Condition | Flag | Recommendation |
|-----------|------|----------------|
| Deadline passed | Overdue | Follow up immediately |
| No updates 7+ days | Stale | Check in with entity |
| 10+ active tasks | Overloaded | Prioritize or delegate |
| All tasks todo (none in progress) | Stuck | Start something |
| No completed tasks this week | No progress | Review blockers |

## Error Handling

- **No entities exist:** "No entities found. Create some first with 'Add new client [Name]'"
- **No completed tasks this week:** "No tasks completed this week. Here's what's in progress..."
- **No in-progress tasks:** "Nothing currently in progress. Here are pending tasks..."
- **Empty week:** "Quiet week - no task updates. Here's the current state..."

## Slash Command

`/weekly-review` or `/weekly`

Example: `/weekly-review`

## Related Files

- Entity roadmaps: `entities/*/ENTITY_ROADMAP.md`
- Entity profiles: `entities/*/ENTITY_PROFILE.md`
- Get status skill: `.github/skills/get-status/SKILL.md`

---

*Skill: weekly-review*
*Version: 1.0*
