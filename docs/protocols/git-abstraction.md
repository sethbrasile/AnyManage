# Git Abstraction Protocol

Complete documentation for invisible git management ensuring users never see technical git complexity.

## Overview

This protocol defines how the agent manages version control completely transparently. Git is the underlying persistence mechanism, but users should never know it exists.

**Key Principle: Zero Git Exposure**

Users should NEVER see:
- Git commands (`git add`, `git commit`, `git push`)
- Git terminology (commit, branch, merge, conflict)
- Git errors (merge conflict, detached HEAD, untracked files)
- Git prompts (commit messages, author configuration)

**User Mental Model:**
"My changes are saved automatically."

**Reality:**
Changes are committed at workflow breakpoints with descriptive messages.

## Zero Git Exposure

### What Users Never See

**Commands:**
```
# NEVER expose to user:
git add .
git commit -m "message"
git push
git pull
git status
git log
```

**Terminology:**
- "Commit" → "Save" or no mention
- "Repository" → "Your workspace"
- "Branch" → Not mentioned
- "Merge conflict" → "Conflict saving changes"

**Error Messages:**
```
# NEVER show:
"fatal: not a git repository"
"error: failed to push some refs"
"CONFLICT (content): Merge conflict in..."

# INSTEAD show:
"Couldn't save changes. Your work is preserved locally."
"There was a conflict saving changes. An administrator may need to help."
```

### User-Facing Language

| Git Concept | User-Facing Equivalent |
|-------------|------------------------|
| Commit | "Save" or "checkpoint" |
| Push | No mention (happens silently) |
| Pull | No mention (happens silently) |
| Merge conflict | "Conflict saving changes" |
| Repository | "Your workspace" or "your files" |
| Branch | Not mentioned (single-user use case) |
| Uncommitted changes | "Unsaved changes" |

## When to Save (Batch Commits)

Trigger automatic saves at natural workflow breakpoints. Batch related changes into single commits rather than committing after every file operation.

### Commit Trigger Points

| Trigger | Example Workflow | Files to Commit |
|---------|------------------|-----------------|
| Entity onboarding complete | User: "Add new client Acme" | entities/Acme Corp/, ROADMAP.md |
| Note processing finished | User: "Process notes for Acme" | entities/Acme Corp/ENTITY_ROADMAP.md, entities/Acme Corp/notes/ |
| Profile update complete | User: "Update Acme's industry to healthcare" | entities/Acme Corp/ENTITY_PROFILE.md |
| Any workflow completes | Multiple file modifications | All modified files |
| Session end/pause | User closes or pauses | STATE.md, PAUSE.md, any uncommitted changes |
| Idle with changes (5 min) | User stopped interacting | All uncommitted files |

### Batch Commit Logic

**Good (batched):**
```
User: "Add new client Acme"
Agent: [creates folder, profile, roadmap, updates ROADMAP.md]
Agent: [commits all in one commit: "Created Acme Corp entity with profile and roadmap"]
```

**Bad (per-file):**
```
Agent: [creates folder]
Agent: [commits: "Created Acme Corp folder"]
Agent: [creates profile]
Agent: [commits: "Added ENTITY_PROFILE.md"]
Agent: [creates roadmap]
Agent: [commits: "Added ENTITY_ROADMAP.md"]
Agent: [updates ROADMAP.md]
Agent: [commits: "Updated master roadmap"]
```

**Why batching matters:**
- Git history is readable: one commit = one logical action
- Easier to revert entire workflow if needed
- Cleaner audit trail for administrators

### Idle Timer Behavior

If user stops interacting and uncommitted changes exist:
1. Wait 5 minutes of inactivity
2. Check for uncommitted changes
3. If found, commit with message: "Session checkpoint: [brief description]"
4. Log to `ops/logs/agent-actions.log`

## Commit Message Format

Commit messages are for admin/developer reference only. Users never see them. Write descriptive, human-readable messages that appear in git history.

### Format Pattern

```
[Action] [Entity]: [brief description]
```

### Examples

| User Action | Commit Message |
|-------------|----------------|
| "Add new client Acme" | "Created Acme Corp entity with profile and roadmap" |
| "Process notes for Beta" | "Processed notes for Beta Industries: 3 tasks, 1 profile update" |
| "Update Gamma's CEO to John Smith" | "Updated Gamma LLC profile: changed CEO" |
| Multiple entity updates | "Session checkpoint: updated profiles for Acme Corp, Beta Industries" |
| Session pause | "Session checkpoint: paused with 2 entities modified" |

### Message Style Guidelines

**Good:**
- "Created Acme Corp entity with profile and roadmap"
- "Updated Beta Industries profile: added new contact"
- "Processed notes for Gamma LLC: extracted 5 tasks"

**Bad:**
- "feat: add entity" (too technical, git conventional commit style)
- "Updated files" (too vague)
- "WIP" (meaningless for audit trail)

**Components:**
1. **Action verb:** Created, Updated, Processed, Deleted, etc.
2. **Entity name:** Acme Corp, Beta Industries, etc. (or "multiple entities")
3. **Brief details:** What changed, how many items, which section

### Multi-Entity Operations

When a single user action affects multiple entities:

```
"Processed notes for 3 entities: Acme Corp, Beta Industries, Gamma LLC"
"Updated profiles for Acme Corp and Beta Industries"
"Session checkpoint: multiple entity updates"
```

## Error Handling

Git operations can fail. Handle all failures gracefully without exposing technical details.

### Error Categories

**1. Transient Errors (temporary, retryable)**
- Network timeouts (if pushing to remote)
- File locks (another process accessing file)
- Disk temporarily full

**2. Persistent Errors (require intervention)**
- Disk permanently full
- File permissions wrong
- Repository corruption
- Merge conflicts (rare in single-user scenario)

### Retry Logic

For transient errors, retry automatically before surfacing to user.

**Retry Strategy:**
- 3 attempts total
- Exponential backoff: 1s, 2s, 4s
- Log each attempt to `ops/logs/git-errors.log`
- Only surface error if all retries fail

**Implementation:**
```
Attempt 1: git commit [files]
  → Failed: "unable to create temporary file"
  → Log: {"timestamp":"...","action":"git_commit_retry","attempt":1,"error":"..."}
  → Wait 1 second

Attempt 2: git commit [files]
  → Failed: same error
  → Log: {"timestamp":"...","action":"git_commit_retry","attempt":2,"error":"..."}
  → Wait 2 seconds

Attempt 3: git commit [files]
  → Failed: same error
  → Log: {"timestamp":"...","action":"git_commit_retry","attempt":3,"error":"..."}
  → Surface to user: "Couldn't save changes. Your work is preserved locally."
```

### Error Messages to User

**Transient failure (after retries exhausted):**
```
Couldn't save changes. Your work is preserved locally.

Try again? (yes/no)
```

If user says "yes", retry immediately.
If user says "no", continue working - commit on next workflow breakpoint.

**Persistent failure (merge conflict):**
```
There was a conflict saving changes. An administrator may need to help.

Your work is safe locally. Continue working? (yes/no)
```

If user says "yes", continue - commits will be queued.
If user says "no", offer: "Want to pause the session?"

**Do NOT show:**
- Full git error messages
- Stack traces
- File paths (e.g., `.git/objects/...`)
- Git commands that failed

### Error Logging

Log all git errors to `ops/logs/git-errors.log` for admin debugging.

**Log format (JSON lines):**
```json
{"timestamp":"2026-01-24T15:30:00Z","action":"git_commit_failed","error":"unable to create temporary file","retry_count":3,"files":["entities/Acme Corp/ENTITY_PROFILE.md"],"user_notified":true}
{"timestamp":"2026-01-24T15:32:00Z","action":"git_merge_conflict","file":"STATE.md","conflict_markers":true,"user_notified":true}
```

### Merge Conflict Handling

Merge conflicts should be rare in single-user scenarios but can occur if:
- User runs agent in multiple terminals
- Administrator makes manual edits
- Remote repository updated externally

**Detection:**
```bash
git commit [files]
# Returns: CONFLICT (content): Merge conflict in STATE.md
```

**Response:**
1. **DO NOT** attempt automatic resolution (data loss risk)
2. Log full conflict details to `ops/logs/git-errors.log`
3. Surface simple message: "There was a conflict saving changes. An administrator may need to help."
4. Preserve user's work locally (files are still modified)
5. Offer to continue working without saving

**What NOT to do:**
- Automatically choose one version
- Attempt to merge conflict markers
- Delete conflict markers without understanding changes
- Force push

## What Gets Committed

### Always Commit

**Entity files:**
```
entities/
  [Entity Name]/
    ENTITY_PROFILE.md
    ENTITY_ROADMAP.md
    notes/
      *.md
      archive/
```

**Templates:**
```
templates/
  ENTITY_PROFILE_TEMPLATE.md
  ENTITY_ROADMAP_TEMPLATE.md
```

**State files:**
```
STATE.md
PAUSE.md
ROADMAP.md
```

**Configuration:**
```
CONFIG.md
INSTRUCTIONS.md
```

**Documentation:**
```
docs/
  protocols/
  guides/
```

### Never Commit

**Logs (gitignored):**
```
ops/logs/
  agent-actions.log
  git-errors.log
  *.log.*
```

**OS files (gitignored):**
```
.DS_Store
Thumbs.db
*.swp
*~
```

**Temporary files:**
```
*.tmp
*.bak
*~
```

### Gitignore Management

Ensure `.gitignore` exists at project root with these patterns:

```gitignore
# Agent logs (local debugging only)
ops/logs/*.log
ops/logs/*.log.*

# OS files
.DS_Store
Thumbs.db

# Editor files
*.swp
*~
```

## Silent Operation

Git operations happen in the background. Users should not see any indication that version control is happening.

### No Save Indicators

**Do NOT show:**
- "Saving..." messages
- "Saved!" confirmations
- Progress indicators for commits
- "Syncing to repository..."

**User experience:**
```
User: "Add new client Acme"
Agent: "Created Acme Corp with profile and roadmap. Want to add some details?"

# Behind the scenes:
# [git add entities/Acme Corp/]
# [git add ROADMAP.md]
# [git commit -m "Created Acme Corp entity with profile and roadmap"]
# [git push (if remote configured)]

# User sees: Just the confirmation, no git activity
```

### When to Surface Git Activity

**Only if there's a problem:**
- Error after retries exhausted
- Merge conflict detected
- Repository corruption

**Otherwise:**
- Silent commits at workflow breakpoints
- No user acknowledgment needed
- Continue conversation naturally

### Background vs Foreground

**Background operations (don't block user):**
- Git push to remote (if configured)
- Log rotation
- Cleanup operations

**Foreground operations (must complete before continuing):**
- Git commit (ensures work is saved)
- File modifications
- Template copying

**Implementation note:**
Commits must complete before showing workflow confirmation to user. This ensures if user says "show me Acme's profile" immediately after creation, the commit is already done.

## Commit Workflow Diagram

```
User Action
    |
    v
Agent performs file operations
    |
    v
Workflow completes
    |
    v
[Stage modified files]
git add [files]
    |
    v
[Commit with descriptive message]
git commit -m "[message]"
    |
    ├─> Success
    |   |
    |   v
    |   [Optional: Push to remote]
    |   git push (background)
    |   |
    |   v
    |   Log success
    |   |
    |   v
    |   Show user confirmation
    |   "Created Acme Corp..."
    |
    └─> Failure
        |
        v
        Retry (3 attempts)
        |
        ├─> Success after retry
        |   |
        |   v
        |   Log retry success
        |   |
        |   v
        |   Show user confirmation
        |
        └─> Still failing
            |
            v
            Log to git-errors.log
            |
            v
            Surface simple error
            "Couldn't save changes..."
            |
            v
            Offer retry or continue
```

## Integration with Other Protocols

### Entity Onboarding

From `docs/protocols/entity-onboarding.md`:

When entity creation completes:
1. All files created (folder, profile, roadmap, .gitkeep)
2. ROADMAP.md updated
3. Git commit with message: "Created [Entity Name] entity with profile and roadmap"
4. Show user confirmation

### Note Processing

When note processing finishes:
1. Notes file created/updated
2. Tasks extracted to roadmap
3. Profile updates applied
4. Git commit with message: "Processed notes for [Entity Name]: [N] tasks, [M] profile updates"
5. Show user summary

### Session Protocol

From `docs/protocols/session-protocol.md`:

**On session start:**
- Silent git pull (if remote configured) to get latest changes
- Load STATE.md
- Check PAUSE.md
- No git activity shown to user

**On session end:**
- Commit any uncommitted changes: "Session checkpoint: [description]"
- Update PAUSE.md if user explicitly paused
- Commit PAUSE.md: "Session paused"
- No git activity shown to user

## Error Recovery

If git operations fail persistently:

**Short-term:**
- User can continue working
- Files are modified locally
- Changes are safe (not lost)
- Next workflow breakpoint will retry commit

**Long-term:**
- Administrator can investigate `ops/logs/git-errors.log`
- Manual git intervention may be needed
- User's work is preserved in working directory

**Communication:**
If same error occurs 3+ times in one session:
```
I'm having trouble saving changes automatically. Your work is safe locally, but an administrator might need to check the setup.

Continue working? (yes/no)
```

## Related Protocols

- **Action Logging:** See `docs/protocols/action-logging.md` - git commits are logged
- **Session Protocol:** See `docs/protocols/session-protocol.md` - session start/end git operations
- **Entity Onboarding:** See `docs/protocols/entity-onboarding.md` - commits after entity creation
- **Error Recovery:** See `docs/protocols/error-recovery.md` - handling persistent failures

---

*Protocol: git-abstraction*
*Version: 1.0*
*Last updated: 2026-01-24*
