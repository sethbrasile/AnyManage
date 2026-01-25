---
name: process-notes
description: Process entity notes into structured tasks and profile updates. Use when the user says "process notes", mentions extracting tasks from notes, wants to update entity information from meeting notes, or provides meeting notes to organize. Supports batch processing across multiple entities.
license: Apache-2.0
metadata:
  author: agent-pm
  version: "1.0"
  compatible_entity_types: ["client", "project", "product"]
---

# Process Notes Skill

Extract tasks, profile facts, calendar items, and follow-ups from meeting notes and unstructured text.

## When to Use

Trigger this skill when user:
- Says "process notes for [entity]"
- Mentions extracting tasks from notes
- Wants to update entity profile from meeting notes
- Provides notes inline, references files, or uses top-level NOTES.md
- Says "I have notes from my meeting with [entity]"
- Says "extract tasks from these notes"
- Says "update [entity] from meeting notes"

## How It Works

This skill implements the note-processing protocol. See `docs/protocols/note-processing.md` for the complete step-by-step protocol.

**Summary:**

1. **Identify input source**
   - Pasted content inline
   - Files in entity's `notes/` folder
   - Top-level `NOTES.md` inbox

2. **Extract and categorize**
   - Tasks: Action items, deadlines, follow-ups
   - Profile facts: Contact info, company details, preferences
   - Calendar items: Meetings, recurring events, key dates
   - Follow-ups: Questions needing user input

3. **Apply updates**
   - Tasks go to entity's `ENTITY_ROADMAP.md`
   - Profile facts go to entity's `ENTITY_PROFILE.md`
   - Uses profile-building protocol for intelligent section placement

4. **Archive processed notes**
   - Add `<!-- Processed by Claude on YYYY-MM-DD -->` header
   - Move to `notes/archive/` subfolder
   - Never delete original notes

5. **Report itemized results**
   - List all tasks added
   - List all profile updates
   - Show follow-ups requiring user input
   - Confirm archive location

## Batch Processing

Supports processing notes for multiple entities in one request:

- **Specific entities:** "Process notes for Acme, Beta, Gamma"
- **All entities:** "Process notes for all clients"
- **Single entity:** "Process notes for Acme"

**Progress feedback for batch operations:**
```
Processing 3 entities... (1/3 complete)
Processed Acme Corp... processing Beta Industries...
```

## Entity Type Awareness

Read entity type from `CONFIG.md` frontmatter if present:
- Adapt terminology based on entity type (client, project, product)
- Map profile sections appropriately for each type
- See `docs/protocols/profile-building.md` for section mapping

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

**For batch operations:**
```
Processed notes for 3 entities:

[Acme Corp]
- Tasks added (2): Follow up on budget, Schedule Q2 review
- Profile updated: Added Sarah Chen as CFO

[Beta Industries]
- Tasks added (1): Send revised timeline
- Follow-up: Confirm April deadline?

Total: 3 tasks added, 1 profile update, 1 follow-up
```

## Error Handling

- **Entity not found:** Offer to create entity first
- **No notes found:** Ask user to paste or add file
- **Ambiguous content:** Enter discussion mode (ask for clarification)
- **Similar tasks exist:** Ask before adding potential duplicates

## Slash Command

`/process-notes [entity]`

Example: `/process-notes Acme Corp`

## Related Protocols

- Note processing: `docs/protocols/note-processing.md`
- Profile building: `docs/protocols/profile-building.md`

---

*Skill: process-notes*
*Version: 1.0*
