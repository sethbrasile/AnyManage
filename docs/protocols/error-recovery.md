# Error Recovery

Patterns for handling failures and recovering gracefully.

## Core Principles

1. **Never lose work** - Save progress before it can be lost
2. **Plain English** - No technical jargon in error messages
3. **Offer options** - Let user decide how to proceed
4. **Learn and prevent** - Document recurring issues

## Checkpoint System

### When to Create Checkpoints

Create a checkpoint before any operation that:
- Modifies multiple files
- Takes more than a few seconds
- Has multiple steps that could fail independently
- Involves external systems (if integrated)

### Checkpoint Format

Stored in `.checkpoints/` directory:

```json
{
  "checkpoint_id": "process-notes-20260124-153045",
  "operation": "process-notes",
  "entity": "Acme Corp",
  "timestamp": "2026-01-24T15:30:45Z",
  "completed_steps": [
    "validated_entity_exists",
    "read_notes_file",
    "extracted_action_items"
  ],
  "current_step": "update_entity_roadmap",
  "pending_steps": [
    "update_master_roadmap",
    "update_state"
  ],
  "artifacts": [
    "entities/Acme Corp/notes/2026-01-24.md"
  ],
  "context": {
    "action_items_count": 5,
    "notes_file": "entities/Acme Corp/notes/2026-01-24.md"
  }
}
```

### Checkpoint Lifecycle

1. **Create** - Before starting multi-step operation
2. **Update** - After each step completes
3. **Delete** - When operation completes successfully
4. **Preserve** - On failure, keep for recovery

## Failure Handling

### FAILED.md Format

When an operation fails, create `FAILED.md` at project root:

```markdown
# Operation Failed

**What happened:** I was processing notes for Acme Corp and couldn't complete the update.

**Where it stopped:** Step 4 of 6 - Updating the entity roadmap

**What went wrong:** The roadmap file appears to be formatted incorrectly. There's a missing section header.

**What was saved:**
- Notes were read successfully
- 5 action items were extracted
- Checkpoint saved at .checkpoints/process-notes-20260124-153045.json

**Your options:**
1. **Fix and resume** - Correct the roadmap format, then say "resume"
2. **Skip this step** - Continue without updating the roadmap
3. **Start over** - Discard progress and try again
4. **Get help** - I'll explain more about what went wrong

What would you like to do?
```

## Recovery Options

### Resume from Checkpoint

User says: "resume" or "continue where you left off"

Agent:
1. Finds most recent checkpoint
2. Validates completed steps still valid
3. Continues from `current_step`
4. Reports progress

### Skip Failed Step

User says: "skip that step" or "continue without it"

Agent:
1. Marks current step as skipped
2. Notes what was skipped in summary
3. Continues with next step
4. Warns if skip causes downstream issues

### Start Over

User says: "start over" or "try again from the beginning"

Agent:
1. Archives current checkpoint
2. Clears any partial progress
3. Begins operation fresh
4. Uses same inputs as before

## Common Error Scenarios

### Missing File

**Error:** Entity folder doesn't exist

**Response:**
```
I couldn't find a folder for "Acme Corp" in your entities.

Would you like me to create it? I'll set up the standard
folder structure with profile and roadmap templates.
```

### Stale Context

**Error:** STATE.md references files that no longer exist

**Response:**
```
Your session state mentions files that I can't find anymore:
- entities/OldClient/notes/meeting.md (deleted?)

This might be outdated. Would you like me to:
1. Clear the old context and start fresh
2. Help you find where those files went
```

### Format Error

**Error:** Markdown file has structural issues

**Response:**
```
The roadmap for Acme Corp has a formatting issue -
there's a section without a proper header.

I can:
1. Try to fix it automatically (I'll show you what I change)
2. Show you where the problem is so you can fix it
3. Work around it for now and fix it later

What would you prefer?
```

### Permission Denied

**Error:** Can't write to file or directory

**Response:**
```
I wasn't able to save changes to the Acme profile.
The file might be open in another program or locked.

Could you check if the file is open elsewhere and close it?
Let me know when ready and I'll try again.
```

## Recovery Best Practices

1. **Save early, save often** - Update checkpoint after each successful step
2. **Validate before proceeding** - Check prerequisites before starting
3. **Offer multiple paths** - Give user choices, not ultimatums
4. **Preserve user intent** - Remember what they were trying to do
5. **Learn from failures** - Document patterns for future prevention
