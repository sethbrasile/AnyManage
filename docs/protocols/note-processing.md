# Note Processing Protocol

Complete documentation for processing unstructured notes into structured tasks, profile updates, and follow-ups.

## Overview

This protocol handles extracting structured data from meeting notes, voice memos, and unstructured text. Users can paste notes, reference files, or use a top-level inbox, and the agent triages content into appropriate destinations.

**Key Principles:**
- Three flexible input sources
- Full triage extraction (tasks, profile facts, calendar items, follow-ups)
- Processed notes marked with header and archived
- Itemized reporting of what was extracted
- Discussion mode for ambiguous content
- Never delete original notes

**Reference:** Adapted from process-client-notes skill pattern (harvest tasks, update knowledge, refine strategy, mark complete, report).

## Trigger Detection

The agent should recognize these patterns as note processing requests:

### Natural Language Patterns

| Pattern | Example |
|---------|---------|
| "Process notes for [entity]" | "Process notes for Acme Corp" |
| "Extract tasks from these notes" | "Extract tasks from these notes: [pasted content]" |
| "Process meeting notes for [entity]" | "Process meeting notes for Beta Industries" |
| "Process these notes" | "Process these notes" (followed by pasted content) |
| "I have notes for [entity]" | "I have notes for Acme" (prompts for input) |

### Slash Command

```
/process-notes [entity]
```

Example: `/process-notes Acme Corp`

### Batch Processing

User can request processing for multiple entities:

```
Process notes for Acme, Beta, and Gamma
```

Agent processes each entity's notes sequentially, provides consolidated report.

### Entity Name Extraction

Extract entity name using same normalization as entity onboarding:
- Convert to title case
- Match against existing entities/ folder names
- If entity not found, ask user or route to internal ops

## Input Sources

Three flexible ways for users to provide note content.

### Source 1: Pasted Inline

User pastes note content directly in the chat:

```
User: Process notes for Acme Corp

Here are my notes from today's meeting:
- Discussed Q2 budget proposal
- John mentioned they need help with security audit
- Sarah will send revised timeline by Friday
- They want to expand to 3 new markets this year
```

**Agent action:**
- Process the pasted content immediately
- Extract and categorize items
- Apply to Acme Corp's files

### Source 2: File in Entity Notes Folder

User has already saved notes to the entity's notes/ folder:

```
User: Process notes for Acme Corp
```

**Agent action:**
1. Check `entities/Acme Corp/notes/` for unprocessed files
2. Identify unprocessed notes (files without "Processed by Claude" header)
3. Read and process each unprocessed file
4. If multiple files found, process all and provide consolidated report

**File detection:**
- Look for `.md`, `.txt` files in `entities/[Name]/notes/`
- Exclude files in `notes/archive/` (already processed)
- Exclude files with `<!-- Processed by Claude on YYYY-MM-DD -->` header

### Source 3: Top-Level NOTES.md

User adds notes to a top-level `NOTES.md` file as a catch-all inbox:

```
User: Process notes from NOTES.md
```

Or simply:

```
User: Process my notes
```

**Agent action:**
1. Read `NOTES.md` in project root
2. Determine routing based on content:
   - Contains entity name mentions → Route to specific entity
   - Multiple entities mentioned → Ask user which entity, or split
   - No entity mentioned → Route to internal ops
3. Process content for each destination
4. Mark section as processed or clear NOTES.md if fully processed

**Routing logic:**
- Scan for entity names (exact or fuzzy match)
- "Meeting with Acme" or "Acme Corp discussion" → Route to Acme Corp
- "Internal team meeting" or "Ops planning" → Route to ops/
- Ambiguous → Ask user: "These notes mention Acme and Beta. Process for which entity?"

## Extraction Categories

Triage note content into four categories based on content type.

### Category 1: Tasks

**Identify tasks by:**
- Action items: "Need to...", "Must...", "Should..."
- Explicit tasks: "Follow up on...", "Send...", "Schedule..."
- Requests: "They asked us to...", "Need help with..."
- Deadlines mentioned: "By Friday", "Before Q2", "Next week"

**Destination:** Entity's `ENTITY_ROADMAP.md`

**Placement:**
- Add to "Active Work" section if due soon or high priority
- Add to "Upcoming" section if future or lower priority
- Use checkbox syntax: `- [ ] [Task description]`
- Include deadline if mentioned: `- [ ] Send revised timeline (by Friday)`

**Before adding:**
- Check for duplicate or similar tasks (see Duplicate Task Detection section)
- If duplicate found, skip or ask user

**Example extraction:**

From note: "John mentioned they need help with security audit by end of Q2"

Extracted task: `- [ ] Help with security audit (by end of Q2)`

### Category 2: Profile Facts

**Identify profile facts by:**
- Information about the entity: "They have 200 employees", "Founded in 2015"
- Contact details: "Sarah Chen is the new CFO", "John's email is john@acme.com"
- Preferences: "Prefers email communication", "Likes detailed reports"
- Goals: "Want to expand to 3 new markets", "Targeting $5M revenue"
- Background: "Referred by Beta Industries", "Previously worked with competitor"

**Destination:** Entity's `ENTITY_PROFILE.md`

**Processing:**
Use the profile-building protocol (see `docs/protocols/profile-building.md`) to:
- Map fact type to appropriate section
- Add to tables, bullets, or prose as appropriate
- Replace conflicting information silently

**Example extraction:**

From note: "Sarah Chen is the new CFO, replacing Michael who retired"

Extracted fact:
- Add to Key Contacts: Sarah Chen (CFO)
- Note in Background: Michael (previous CFO) retired

### Category 3: Calendar Items

**Identify calendar items by:**
- Specific dates: "Meeting on March 15", "Deadline April 1"
- Recurring events: "Weekly status call", "Monthly review"
- Scheduled meetings: "Next meeting scheduled for...", "Call at 2pm"

**Destination:**
- **If task-related:** Add to roadmap with date: `- [ ] Review proposal (due March 15)`
- **If recurring meeting:** Add to Communication Preferences > Meeting Cadence in profile
- **If important deadline:** Add to Communication Preferences > Key Dates in profile

**Example extraction:**

From note: "Weekly status calls every Monday at 10am"

Extracted calendar item:
- Add to profile Communication Preferences > Meeting Cadence: "Status update | Weekly | [Attendees] | Monday 10am"

### Category 4: Follow-ups

**Identify follow-ups by:**
- Questions needing user input: "Not sure which approach they prefer"
- Decisions needed: "Should we offer discount or extended timeline?"
- Clarifications required: "Unclear if they meant Q2 or Q3"
- Items needing user action: "Need to check with team before committing"

**Destination:** Present to user in processing report, don't add to files automatically

**Format in report:**
```
Follow-ups for you:
- [ ] Confirm if April 15 deadline works for the team
- [ ] Decide: offer discount or extended timeline?
- [ ] Clarify: did they mean Q2 or Q3 for the security audit?
```

**User can then:**
- Provide answers immediately
- Add to their own task list
- Ignore if not relevant

## Duplicate Task Detection

Before adding tasks to roadmap, check for duplicates to avoid cluttering.

### Detection Method

1. Read existing `ENTITY_ROADMAP.md`
2. Extract all current tasks (Active Work, Upcoming, Completed)
3. Compare new task against existing tasks:
   - **Exact match:** Same wording → Skip, note in report
   - **Similar match:** >70% text similarity → Ask user
   - **No match:** Add to roadmap

### Exact Match

New task: "Send revised timeline"
Existing task: "Send revised timeline"

**Agent action:**
- Skip adding duplicate
- Note in report: "Task already exists: Send revised timeline"

### Similar Match

New task: "Follow up on budget proposal"
Existing task: "Review budget proposal with John"

**Agent action:**
- Pause and ask user

**Agent response:**
```
Similar task exists: "Review budget proposal with John"
New task from notes: "Follow up on budget proposal"

Add anyway? (yes/no)
```

**If user confirms:** Add new task
**If user declines:** Skip, note in report

### No Match

New task is unique → Add to roadmap immediately.

## Processed Note Handling

After processing, mark notes as processed and archive them.

### Add Processed Header

Add this header at the very top of the processed note file:

```markdown
<!-- Processed by Claude on 2026-01-24 -->
```

Use current date in YYYY-MM-DD format.

**Purpose:**
- Future processing can skip already-processed files
- User can see at a glance what's been handled
- Provides audit trail

### Archive Note

After adding header, move file to archive:

**Original location:**
```
entities/Acme Corp/notes/meeting-2026-01-24.md
```

**Archive location:**
```
entities/Acme Corp/notes/archive/meeting-2026-01-24.md
```

**Steps:**
1. Add header to note file
2. Move file to `notes/archive/` subfolder
3. Preserve filename (easier to reference later)

### Never Delete

**Critical:** Never delete original notes, even after processing.

**Rationale:**
- User may want to reference original context
- Extraction might miss something
- Git history doesn't replace having the file
- Storage is cheap, lost context is expensive

### Top-Level NOTES.md Handling

For top-level `NOTES.md`:

**Option A - Clear after processing:**
If all content was successfully extracted and routed:
- Add processed header at top
- Optionally clear content (or move to ops/archive/)

**Option B - Mark section processed:**
If NOTES.md contains multiple sections:
- Add `<!-- Processed 2026-01-24 -->` comment after each processed section
- Leave file intact for reference

**Default approach:** Option A (clear after processing) unless user prefers to keep history.

## Digest and Index Updates

After applying extractions and archiving notes, update derived files to keep the entity's digest and indexes current.

### Update Entity Digest

Read `entities/[Name]/DIGEST.md` and refresh the following sections based on what was just extracted:

1. **Active Work** — If new tasks were added to the roadmap, reflect them here
2. **Upcoming Deadlines** — If extracted tasks have deadlines, add to the table
3. **Recent Activity** — Add an entry for this note processing event (e.g., "2026-01-24: Processed meeting notes — 3 tasks, 1 profile update")
4. **Key Context** — If a genuinely significant new insight was learned (not routine tasks), add or update a bullet
5. **Topics** — If extracted content introduces a new topic area, add the tag

If `DIGEST.md` doesn't exist yet, generate one from the current state of the entity's profile, roadmap, and knowledge files using `templates/DIGEST_TEMPLATE.md` as the structure.

Update the `<!-- Last refreshed: -->` date in the header.

### Update Key Insights

If note processing revealed a genuinely important insight about the entity (decision-making style, risk profile, strategic direction, key relationship dynamic), promote it to the **Key Insights** section of `entities/[Name]/knowledge/LEARNED_CONTEXT.md`.

**Promotion criteria:**
- Would this change how we approach this entity?
- Would a new team member need to know this?
- Is this a pattern, not a one-time data point?

If Key Insights already has ~20 bullets, replace the least-relevant one rather than exceeding the target.

Most note processing events will NOT produce new Key Insights — routine tasks and calendar items don't qualify. Only significant context about the entity's preferences, constraints, opportunities, or relationship dynamics qualifies.

### Tag Topics

As items are extracted, assign one or more topic tags to the processing event. Topics are emergent — use whatever feels natural for the domain. Within a given user's domain, reuse existing topic names for consistency.

**Examples:** seo, website, pricing, content-strategy, lead-generation, competitive-landscape, hiring, compliance

These tags are recorded in:
- The entity's `DIGEST.md` Topics field
- `.index/TOPICS.md` (if it exists) — add/update the entity's entry under each relevant topic

If `.index/TOPICS.md` doesn't exist yet, skip this step. The cross-entity index is built during Phase 2.

## Itemized Reporting

After processing, provide detailed report of what was extracted and where it went.

### Report Template

```
Processed notes for [Entity Name]:

- Tasks added to roadmap ([count]):
  - [ ] [Task 1 with deadline if applicable]
  - [ ] [Task 2]
  - [ ] [Task 3]

- Profile updated:
  - Added to [Section Name]: [Brief description]
  - Updated [Section Name]: [Brief description]

- Calendar items:
  - Added to [Location]: [Item description]

- Follow-ups for you:
  - [ ] [Follow-up item requiring user input]
  - [ ] [Another follow-up]

- Notes archived to: entities/[Name]/notes/archive/[filename].md
```

### Multiple Files

If multiple note files were processed:

```
Processed 3 note files for Acme Corp:

From meeting-2026-01-24.md:
- Tasks added (2): Follow up on budget, Schedule Q2 review
- Profile updated: Added Sarah Chen as CFO

From call-notes-2026-01-23.md:
- Tasks added (1): Send revised timeline
- Follow-up: Confirm April deadline?

From brainstorm.md:
- Tasks added (3): Research competitors, Draft proposal, Schedule demo

All files archived to entities/Acme Corp/notes/archive/
```

### Nothing Extracted

If notes don't contain actionable items:

```
Processed notes for Acme Corp:

- No new tasks found
- No profile updates needed
- Notes archived to: entities/Acme Corp/notes/archive/meeting-2026-01-24.md

The notes have been saved for reference.
```

### Skipped Duplicates

Include skipped items in report:

```
Processed notes for Beta Industries:

- Tasks added to roadmap (2):
  - [ ] Prepare Q2 presentation
  - [ ] Schedule team sync

- Tasks skipped (already exist):
  - Follow up on proposal (already in roadmap)

- Profile updated:
  - Added to Things to Remember: Prefers morning meetings
```

## Discussion Mode

When note content is ambiguous or "prompt-like", pause extraction and clarify with user.

### Prompt-Like Detection

**Indicators that notes are actually instructions:**
- Second-person language: "You should...", "Make sure to..."
- Commands: "Create a task for...", "Add to profile..."
- Questions to agent: "Can you help with...", "What do you think about..."

**Example prompt-like content:**
```
Notes for Acme:
- You should create a task to follow up with John
- Add Sarah Chen to their profile as CFO
- Can you help me draft a proposal for them?
```

This reads like instructions to the agent, not notes to process.

### Discussion Mode Response

**Agent pauses and asks:**

```
These notes look like instructions rather than content to extract. Should I:
1. Follow the instructions (create task, update profile, help with proposal)
2. Process as notes (extract tasks from the text itself)
3. Show me what you mean

Which would you prefer?
```

**Wait for user clarification** before proceeding.

### Ambiguous Content

**Example ambiguous content:**
```
Acme Corp - not sure if they're ready for the advanced features. Might need to scale back scope.
```

**Agent asks:**
```
Should I add this as:
1. A task: "Evaluate if Acme is ready for advanced features"
2. A profile note under Things to Remember
3. A follow-up for you to decide

Which makes sense?
```

### When to Use Discussion Mode

**Use discussion mode when:**
- Content could be interpreted multiple ways
- Instructions mixed with data
- User intent unclear
- Extraction would require assumptions

**Don't overuse:**
- If 90% clear, process it and note uncertainties in follow-ups
- Don't ask about every minor ambiguity
- Err toward processing unless genuinely unclear

## Error Handling

Handle common failure scenarios gracefully.

### Entity Not Found

**User says:** "Process notes for Acme"

**But:** No entity named "Acme" exists

**Agent response:**
```
No entity named 'Acme' found. Should I:
1. Create entity 'Acme' first, then process notes
2. Process notes for a different entity
3. Save notes to internal ops

Which would you prefer?
```

### No Notes Found

**User says:** "/process-notes Beta Industries"

**But:** `entities/Beta Industries/notes/` is empty or only has archived files

**Agent response:**
```
No unprocessed notes found for Beta Industries. Want to paste some notes here, or add a file to entities/Beta Industries/notes/ first?
```

### File Read Error

**Rare case:** Note file exists but can't be read (permissions, corruption)

**Agent action:**
1. Log error to `ops/logs/error.log`
2. Skip file, continue with others if batch processing

**Agent response:**
```
Couldn't read [filename].md (might be corrupted). Skipping for now. Other files processed successfully.
```

### Extraction Found Nothing

**User provides notes, but content is purely informational with no actionable items:**

**Agent response:**
```
Processed notes for Acme Corp:

- No new tasks found
- No profile updates needed
- Notes archived for reference

The content was informational only. Let me know if I missed something!
```

## Example Interactions

### Example 1: Pasted Inline Notes

**User:**
```
Process notes for Acme Corp

Here are my notes from today's meeting:
- Discussed Q2 budget proposal, need to follow up by Friday
- Sarah Chen is the new CFO, replacing Michael
- They want to expand to 3 new markets this year
- Need to schedule quarterly review meeting
- John prefers email over Slack
```

**Agent actions:**
1. Extract entity: Acme Corp
2. Process inline content:
   - Task: Follow up on Q2 budget proposal (by Friday)
   - Profile: Sarah Chen as CFO, Michael retired
   - Profile: Goal to expand to 3 new markets
   - Task: Schedule quarterly review meeting
   - Profile: Prefers email communication
3. Apply extractions
4. No file to archive (inline content)

**Agent response:**
```
Processed notes for Acme Corp:

- Tasks added to roadmap (2):
  - [ ] Follow up on Q2 budget proposal (by Friday)
  - [ ] Schedule quarterly review meeting

- Profile updated:
  - Added to Key Contacts: Sarah Chen (CFO)
  - Added to Background Context: Michael (previous CFO) retired
  - Added to Business Goals: Expand to 3 new markets this year
  - Added to Communication Preferences: Prefers email over Slack
```

### Example 2: File in Entity Notes Folder

**User:**
```
Process notes for Beta Industries
```

**Agent actions:**
1. Check `entities/Beta Industries/notes/` for unprocessed files
2. Find `meeting-2026-01-24.md` (no processed header)
3. Read file content
4. Extract tasks, profile facts, etc.
5. Add processed header to file
6. Move to `entities/Beta Industries/notes/archive/`
7. Report results

**Agent response:**
```
Processed notes for Beta Industries:

- Tasks added to roadmap (3):
  - [ ] Prepare Q2 presentation for board meeting
  - [ ] Research competitor pricing
  - [ ] Send contract renewal reminder (due March 1)

- Profile updated:
  - Added to Key Dates: Board meeting on March 15
  - Added to Things to Remember: Sensitive about pricing discussions

- Notes archived to: entities/Beta Industries/notes/archive/meeting-2026-01-24.md
```

### Example 3: Top-Level NOTES.md Routing

**User:**
```
Process my notes
```

**Agent actions:**
1. Read `NOTES.md` in project root
2. Content mentions "Acme Corp" and "internal team"
3. Detect mixed content → Ask user or auto-route

**NOTES.md content:**
```
Meeting with Acme Corp:
- Follow up on budget by Friday
- Sarah is new CFO

Internal team planning:
- Hire new designer
- Update website
```

**Agent response:**
```
Found notes for both Acme Corp and internal team. Processing both:

Acme Corp:
- Tasks added (1): Follow up on budget (by Friday)
- Profile updated: Added Sarah as CFO

Internal team (ops):
- Tasks added (2): Hire new designer, Update website

Notes cleared from NOTES.md
```

### Example 4: Duplicate Task Detection

**User:**
```
Process notes for Gamma LLC

Notes:
- Follow up on proposal
- Send invoice for January
```

**Existing roadmap has:** `- [ ] Follow up on proposal with Sarah`

**Agent actions:**
1. Extract tasks from notes
2. Check against existing roadmap
3. Find similar match for "Follow up on proposal"
4. Ask user

**Agent response:**
```
Similar task exists: "Follow up on proposal with Sarah"
New task from notes: "Follow up on proposal"

Add anyway? (yes/no)
```

**User:** "no"

**Agent final response:**
```
Processed notes for Gamma LLC:

- Tasks added to roadmap (1):
  - [ ] Send invoice for January

- Tasks skipped (already exist):
  - Follow up on proposal (similar to existing task)
```

### Example 5: Discussion Mode - Prompt-Like Content

**User:**
```
Process notes for Delta Partners

Can you help me create a proposal for them? Make sure to include pricing for both packages and add a note to their profile that they're price-sensitive.
```

**Agent recognizes:** This is instructions, not notes

**Agent response:**
```
These notes look like instructions rather than content to extract. Should I:
1. Follow the instructions (help create proposal, update profile with price-sensitive note)
2. Process as notes (extract "create proposal" as task, "price-sensitive" as profile fact)

Which would you prefer?
```

**User:** "1 - follow the instructions"

**Agent response:**
```
Got it. I'll help with the proposal and update their profile.

First, let me add to Delta Partners profile: Price-sensitive (added to Things to Remember).

Now, for the proposal - what are the two packages you want to include?
```

### Example 6: Multiple Files Batch Processing

**User:**
```
/process-notes Epsilon Group
```

**Agent finds 3 files in `entities/Epsilon Group/notes/`:**
- `call-2026-01-20.md`
- `meeting-2026-01-22.md`
- `brainstorm-2026-01-23.md`

**Agent actions:**
1. Process each file sequentially
2. Consolidate extractions
3. Archive all files
4. Report combined results

**Agent response:**
```
Processed 3 note files for Epsilon Group:

From call-2026-01-20.md:
- Tasks added (1): Schedule kickoff meeting
- Profile updated: Added contact Mike Johnson (CTO)

From meeting-2026-01-22.md:
- Tasks added (2): Send SOW for review, Prepare technical demo
- Follow-up: Confirm if March 1 start date works?

From brainstorm-2026-01-23.md:
- Tasks added (3): Research integration requirements, Draft architecture proposal, Schedule technical review

Total: 6 tasks added, 1 profile update, 1 follow-up

All files archived to entities/Epsilon Group/notes/archive/
```

## Integration with Other Workflows

### Entity Onboarding → Note Processing

User creates entity, then immediately processes notes:

```
User: Add new client Zeta Corp
Agent: Created Zeta Corp with profile and roadmap. Have meeting notes to process?
User: Yes, process notes for Zeta

[User pastes notes]

Agent: Processed notes for Zeta Corp:
       - Tasks added to roadmap (3)
       - Profile updated with contact info and background
```

### Note Processing → Profile Building

Processing notes uses profile-building protocol for profile facts:

```
User: Process notes for Acme

Notes: Sarah Chen is new CFO, they have 200 employees

Agent: [Uses profile-building protocol to add facts]
Agent: Processed notes for Acme:
       - Profile updated:
         - Added to Key Contacts: Sarah Chen (CFO)
         - Updated Entity Information: 200 employees
```

See `docs/protocols/profile-building.md` for profile update mechanics.

### Batch Operations

User can process notes for multiple entities:

```
User: Process notes for Acme, Beta, and Gamma
Agent: Processing notes for 3 entities...
       [Processes each sequentially]
       [Provides consolidated report for all three]
```

## Commit Behavior

Note processing triggers automatic git commits (invisible to user).

### Commit Message Format

```
feat(notes): process notes for [Entity Name]

- [X] tasks added to roadmap
- [Y] profile updates
- Notes archived
```

**Example:**
```
feat(notes): process notes for Acme Corp

- 3 tasks added to roadmap
- 2 profile updates (CFO, company size)
- Notes archived to entities/Acme Corp/notes/archive/
```

### Commit Timing

Commit after all processing complete, before showing report to user.

### Batched Updates

If processing updates multiple files (roadmap, profile, multiple entities), commit once with all changes.

### Commit Failure Handling

If git commit fails:
- Log error to `ops/logs/error.log`
- DO NOT expose git terminology to user
- Extracted data is still applied (user can work with it)
- Notes are still archived
- Retry commit on next operation

**User never sees:** "Git commit failed"

**User might see (only if persistent):** "Couldn't save changes automatically. Your extracted data is in place, but let me know if you see this again."

## Related Protocols

- **Entity Onboarding:** See `docs/protocols/entity-onboarding.md`
- **Profile Building:** See `docs/protocols/profile-building.md`
- **Session Protocol:** See `docs/protocols/session-protocol.md`
- **Command Patterns:** See `docs/protocols/command-patterns.md`

---

*Protocol: note-processing*
*Version: 1.0*
*Last updated: 2026-01-24*
