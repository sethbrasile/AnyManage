# Phase 4: Skill System Foundation - Context

**Gathered:** 2026-01-24
**Status:** Ready for planning

<domain>
## Phase Boundary

Create a skill system that enables reusable workflows encoding best practices. Users invoke skills through natural language — skills are implementation details, not user-facing concepts. The system supports batch operations across entities and can discover/adapt skills during entity type onboarding.

</domain>

<decisions>
## Implementation Decisions

### Skill Invocation
- Both natural language and slash commands work equally ("run weekly review" = "/weekly-review")
- Confirm only before destructive actions (deletes, overwrites) — not for creates/updates
- Infer entity from context when possible — only ask if ambiguous
- Batch operations supported: "process notes for all clients" works

### Skill Output
- Show progress updates during execution ("Processing notes... Updating profile... Done")
- After completion: summarize actions taken + suggest next steps
- Each skill defines its own output format (optimized per task, not forced consistency)
- Batch results shown as aggregated summary ("Processed 5 clients: 12 tasks added")

### Skill Discovery
- Skills are invisible to users — they just talk naturally and agent handles it
- Optional deep dive: "/skills" or "show me your skills" reveals what's available for curious users
- Agent attempts to help even without a matching skill — uses general capabilities, not just defined workflows
- Proactive suggestions: Claude decides phrasing based on context (action-focused vs outcome-focused)

### Skill File Format
- YAML frontmatter + Markdown body (structured metadata, prose instructions below)
- Hybrid storage: shared /skills/ folder, agent wrappers can override or extend
- All skills (including entity-specific) stored in /skills/ with type metadata
- Entity-type aware: skills can specify which entity types they work with

### Skill Acquisition (Entity Onboarding)
- During entity type onboarding, agent identifies skill needs
- Agent searches for prebuilt skills online, assesses quality, adapts as needed
- Asks user for clarification during skill creation/adaptation
- Creates well-designed entity-specific skills that live in shared /skills/ folder

### Claude's Discretion
- Proactive suggestion phrasing (action-focused vs outcome-focused)
- Specific YAML frontmatter schema
- Skill file naming conventions
- How to handle skill version conflicts between agents

</decisions>

<specifics>
## Specific Ideas

- "The user shouldn't need to worry about skills" — skills are pure implementation, users express intent naturally
- Natural language examples: "What is the status of [entity]?", "Do I have any pending action items?", "Hey I just had a meeting with ben at [entity] and he said we are a go for launch on friday"
- Agent should be capable even without defined skills — general intelligence fills gaps

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 04-skill-system-foundation*
*Context gathered: 2026-01-24*
