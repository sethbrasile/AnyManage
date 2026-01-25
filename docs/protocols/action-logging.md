# Action Logging Protocol

Complete documentation for agent action logging providing traceability, debugging support, and audit trails.

## Overview

This protocol defines how all agent actions are logged for debugging, compliance, and traceability purposes.

**Purpose:**
Logging provides:
- **Traceability:** What did the agent do, and when?
- **Debugging:** Reconstruct agent behavior when troubleshooting
- **Audit trail:** Compliance and accountability (who changed what, when?)
- **Specialist tracking:** Foundation for Phase 5 subagent/specialist logging (ENTY-06)

**Key Principle:**
Logs are developer/admin tooling, NOT user-facing. Users should never be directed to read logs.

## Log Location

All logs are stored locally and gitignored - they stay on the machine for debugging, not shared in the repository.

### Directory Structure

```
ops/
  logs/
    .gitkeep               <- Ensures directory exists
    agent-actions.log      <- All agent workflow actions
    git-errors.log         <- Git operation failures
```

### Gitignore Configuration

Ensure `.gitignore` contains:
```gitignore
# Agent logs (local debugging only)
ops/logs/*.log
ops/logs/*.log.*
```

This prevents logs from being committed to git while preserving directory structure (via `.gitkeep`).

## Log Format

Use structured JSON logging - one JSON object per line for parseability.

### Format Specification

Each log entry is a single-line JSON object:

```json
{"timestamp":"ISO-8601","action":"action_type","entity":"Entity Name","details":{...}}
```

**Required fields:**
- `timestamp`: ISO-8601 format (e.g., `2026-01-24T15:30:00Z`)
- `action`: Action type from standard action types table

**Optional fields:**
- `entity`: Entity name if action is entity-specific
- `details`: Object with action-specific details
- `user`: User identifier (for future multi-user scenarios)
- `session_id`: Session identifier (for correlating actions)

### Why JSON Lines?

**Advantages:**
- Machine-parseable: Easy to process with tools
- Grep-friendly: Each line is complete, can filter by field
- Append-friendly: No need to parse entire file to add entry
- Streaming-friendly: Can tail -f and process line by line

**Example processing:**
```bash
# All entity creations
grep "entity_created" ops/logs/agent-actions.log

# All actions for specific entity
grep "Acme Corp" ops/logs/agent-actions.log

# Actions in specific time range
grep "2026-01-24T15:" ops/logs/agent-actions.log

# Parse JSON for structured analysis
cat ops/logs/agent-actions.log | jq 'select(.action == "entity_created")'
```

## Action Types

Standardized action types ensure consistency and parseability.

### Standard Action Types Table

| Action Type | When Logged | Details Captured | Example |
|-------------|-------------|------------------|---------|
| `entity_created` | After entity onboarding | entity name, type, files created | `{"entity":"Acme Corp","details":{"type":"client","files":["ENTITY_PROFILE.md","ENTITY_ROADMAP.md"]}}` |
| `entity_deleted` | After entity deletion | entity name, files removed | `{"entity":"Beta Inc","details":{"files_removed":5}}` |
| `profile_updated` | After profile modification | entity, section, change type | `{"entity":"Acme Corp","details":{"section":"Key Contacts","change":"added row"}}` |
| `roadmap_updated` | After roadmap modification | entity, tasks added/modified | `{"entity":"Acme Corp","details":{"tasks_added":3,"tasks_completed":1}}` |
| `notes_processed` | After note processing | entity, extraction counts, source | `{"entity":"Acme Corp","details":{"tasks_added":3,"profile_updates":1,"source":"inline"}}` |
| `git_commit` | After successful commit | message, file count | `{"details":{"message":"Created Acme Corp entity","files_count":4}}` |
| `git_error` | After git failure | error type, message, retry count | `{"details":{"error":"merge conflict","message":"CONFLICT in STATE.md","retry_count":3}}` |
| `session_start` | On session initialization | loaded context files | `{"details":{"state_loaded":true,"pause_found":false}}` |
| `session_end` | On pause/end | uncommitted changes count | `{"details":{"uncommitted_files":2}}` |
| `specialist_invoked` | Subagent/specialist started (Phase 5) | specialist type, entity, task | `{"specialist":"assessment-specialist","entity":"Acme Corp","task":"quarterly review"}` |
| `specialist_completed` | Subagent/specialist finished (Phase 5) | specialist type, deliverable | `{"specialist":"assessment-specialist","deliverable":"AUDIT_FINDINGS.md"}` |

### Action-Specific Details

**entity_created:**
```json
{
  "timestamp": "2026-01-24T15:30:00Z",
  "action": "entity_created",
  "entity": "Acme Corp",
  "details": {
    "type": "client",
    "files": ["ENTITY_PROFILE.md", "ENTITY_ROADMAP.md"],
    "added_to_roadmap": true
  }
}
```

**notes_processed:**
```json
{
  "timestamp": "2026-01-24T15:35:00Z",
  "action": "notes_processed",
  "entity": "Acme Corp",
  "details": {
    "source": "inline",
    "tasks_added": 3,
    "profile_updates": 1,
    "notes_file": "notes/2026-01-24-meeting.md"
  }
}
```

**git_error:**
```json
{
  "timestamp": "2026-01-24T15:40:00Z",
  "action": "git_error",
  "details": {
    "error_type": "merge_conflict",
    "message": "CONFLICT (content): Merge conflict in STATE.md",
    "retry_count": 3,
    "files": ["STATE.md"],
    "user_notified": true
  }
}
```

**profile_updated:**
```json
{
  "timestamp": "2026-01-24T15:45:00Z",
  "action": "profile_updated",
  "entity": "Beta Industries",
  "details": {
    "section": "Entity Information",
    "field": "Industry",
    "old_value": "[Industry/vertical]",
    "new_value": "Healthcare Technology"
  }
}
```

## Logging in Workflows

Document when to log during agent workflows.

### Entity Onboarding Flow

```
1. User: "Add new client Acme Corp"
2. Agent: Normalizes name to "Acme Corp"
3. Agent: Checks for duplicates (none found)
4. Agent: Creates folder structure
5. Agent: Copies templates
6. Agent: Updates ROADMAP.md
7. LOG: {"timestamp":"...","action":"entity_created","entity":"Acme Corp","details":{...}}
8. Agent: Git commit
9. LOG: {"timestamp":"...","action":"git_commit","details":{"message":"Created Acme Corp entity..."}}
10. Agent: Shows confirmation to user
```

### Note Processing Flow

```
1. User: "Process notes for Beta Industries"
2. Agent: Reads notes from entities/Beta Industries/notes/
3. Agent: Extracts tasks and profile updates
4. Agent: Updates ENTITY_ROADMAP.md
5. Agent: Updates ENTITY_PROFILE.md
6. LOG: {"timestamp":"...","action":"notes_processed","entity":"Beta Industries","details":{"tasks_added":5,"profile_updates":2}}
7. Agent: Git commit
8. LOG: {"timestamp":"...","action":"git_commit","details":{...}}
9. Agent: Shows summary to user
```

### Session Start Flow

```
1. Agent: Initializes session
2. Agent: Reads STATE.md
3. Agent: Checks for PAUSE.md
4. LOG: {"timestamp":"...","action":"session_start","details":{"state_loaded":true,"pause_found":true}}
5. Agent: Silent ready (no greeting)
```

### Error Scenario Flow

```
1. Agent: Attempts git commit
2. Git: Returns error (merge conflict)
3. LOG: {"timestamp":"...","action":"git_error","details":{"error_type":"merge_conflict",...}}
4. Agent: Retries (3 attempts)
5. LOG: (each retry attempt)
6. Agent: Surfaces error to user
7. LOG: {"timestamp":"...","action":"git_error","details":{"user_notified":true}}
```

## Log Rotation

Prevent logs from growing unbounded.

### Rotation Parameters

- **Maximum file size:** 10 MB
- **Retention period:** 14 days
- **Rotation method:** Rename and create new

### Rotation Logic

When `agent-actions.log` reaches 10 MB or is 14 days old:

```bash
# Rename current log
mv ops/logs/agent-actions.log ops/logs/agent-actions.log.1

# If .1 exists, shift to .2, etc.
mv ops/logs/agent-actions.log.1 ops/logs/agent-actions.log.2

# Create new empty log
touch ops/logs/agent-actions.log
```

### Rotation Trigger

**Option A: Agent checks on session start**
```
1. Session initializes
2. Check ops/logs/agent-actions.log size
3. If > 10 MB, rotate
4. If age > 14 days, rotate
5. Continue session
```

**Option B: Cron job (admin responsibility)**
```bash
# /etc/cron.daily/agent-pm-log-rotate
#!/bin/bash
LOG_DIR="/path/to/project/ops/logs"
MAX_SIZE=$((10 * 1024 * 1024))  # 10 MB
RETENTION_DAYS=14

# Size-based rotation
if [ -f "$LOG_DIR/agent-actions.log" ]; then
  SIZE=$(stat -f%z "$LOG_DIR/agent-actions.log")
  if [ $SIZE -gt $MAX_SIZE ]; then
    # Rotate logic here
  fi
fi

# Age-based cleanup
find "$LOG_DIR" -name "*.log.*" -mtime +$RETENTION_DAYS -delete
```

**Recommendation:** Agent performs basic rotation on session start. Cron is optional for high-volume environments.

## Viewing Logs

Logs are for admin/developer use - provide guidance for reading them.

### Direct Viewing

**View all logs:**
```bash
cat ops/logs/agent-actions.log
```

**View most recent logs:**
```bash
tail -f ops/logs/agent-actions.log
```

**View specific action type:**
```bash
grep "entity_created" ops/logs/agent-actions.log
```

**View specific entity:**
```bash
grep "Acme Corp" ops/logs/agent-actions.log
```

### Structured Parsing

**Using jq (JSON processor):**

```bash
# All entity creations with details
cat ops/logs/agent-actions.log | jq 'select(.action == "entity_created")'

# Count actions by type
cat ops/logs/agent-actions.log | jq -r '.action' | sort | uniq -c

# Actions for specific entity
cat ops/logs/agent-actions.log | jq 'select(.entity == "Acme Corp")'

# Actions in time range
cat ops/logs/agent-actions.log | jq 'select(.timestamp >= "2026-01-24T15:00:00Z" and .timestamp <= "2026-01-24T16:00:00Z")'

# Git errors only
cat ops/logs/agent-actions.log | jq 'select(.action == "git_error")'
```

### Log Analysis Examples

**Debugging: "What happened to Acme Corp yesterday?"**
```bash
grep "Acme Corp" ops/logs/agent-actions.log | grep "2026-01-23"
```

**Audit: "What entities were created this week?"**
```bash
grep "entity_created" ops/logs/agent-actions.log | jq 'select(.timestamp >= "2026-01-20")'
```

**Troubleshooting: "Why did the last commit fail?"**
```bash
grep "git_error" ops/logs/git-errors.log | tail -1
```

**Performance: "How many notes were processed today?"**
```bash
grep "notes_processed" ops/logs/agent-actions.log | grep "2026-01-24" | wc -l
```

## Subagent Logging (ENTY-06)

Establish logging pattern for Phase 5 specialist/subagent implementation.

### Specialist Action Types

When specialists/subagents are implemented in Phase 5, log their invocations and completions.

**Specialist invoked:**
```json
{
  "timestamp": "2026-01-24T15:50:00Z",
  "action": "specialist_invoked",
  "specialist": "assessment-specialist",
  "entity": "Acme Corp",
  "details": {
    "task": "quarterly review",
    "input_files": ["ENTITY_PROFILE.md", "ENTITY_ROADMAP.md"],
    "requested_by": "main-agent"
  }
}
```

**Specialist completed:**
```json
{
  "timestamp": "2026-01-24T15:55:00Z",
  "action": "specialist_completed",
  "specialist": "assessment-specialist",
  "entity": "Acme Corp",
  "details": {
    "deliverable": "AUDIT_FINDINGS.md",
    "duration_seconds": 300,
    "findings_count": 5,
    "success": true
  }
}
```

**Specialist error:**
```json
{
  "timestamp": "2026-01-24T15:52:00Z",
  "action": "specialist_error",
  "specialist": "assessment-specialist",
  "entity": "Acme Corp",
  "details": {
    "error": "Missing required field: revenue",
    "recovery": "prompted user for input",
    "recovered": true
  }
}
```

### Specialist Types

Document expected specialist types for future reference:

| Specialist Type | Purpose | Deliverables |
|----------------|---------|--------------|
| `assessment-specialist` | Quarterly reviews, audits | `AUDIT_FINDINGS.md` |
| `strategy-specialist` | Strategic planning, roadmap analysis | `STRATEGY_RECOMMENDATIONS.md` |
| `communication-specialist` | Client communications, reports | `CLIENT_REPORT.md` |
| `research-specialist` | Background research, data gathering | `RESEARCH_SUMMARY.md` |

### Traceability Chain

Logging enables traceability from user request → main agent → specialist → deliverable:

```
1. LOG: {"action":"session_start",...}
2. LOG: {"action":"notes_processed","entity":"Acme Corp",...}
3. LOG: {"action":"specialist_invoked","specialist":"assessment-specialist",...}
4. LOG: {"action":"specialist_completed","deliverable":"AUDIT_FINDINGS.md",...}
5. LOG: {"action":"git_commit","details":{"message":"Assessment complete for Acme Corp"}}
```

Admin can reconstruct: "User processed notes → triggered assessment → specialist created findings → committed to repo"

## Log Security

Logs may contain sensitive information - handle appropriately.

### What Gets Logged

**Safe to log:**
- Entity names
- Action types
- File names
- Timestamps
- Counts and metrics

**Potentially sensitive (log with care):**
- Profile field values (industry, contact info)
- Task descriptions (may reference confidential projects)
- Note content snippets

**Never log:**
- Passwords or API keys
- Full credit card numbers
- SSNs or other PII
- Full document contents

### Sanitization Guidelines

When logging details that might contain sensitive data:

**Good:**
```json
{"action":"profile_updated","details":{"section":"Key Contacts","change":"added row"}}
```

**Bad:**
```json
{"action":"profile_updated","details":{"section":"Key Contacts","new_contact":"john.smith@acme.com, CEO, 555-1234"}}
```

**Good:**
```json
{"action":"notes_processed","details":{"tasks_added":3,"profile_updates":1}}
```

**Bad:**
```json
{"action":"notes_processed","details":{"task_text":"Negotiate $500k contract with Acme for Project Phoenix"}}
```

### Access Control

Logs are stored locally on the machine. Access control is OS-level:

```bash
# Restrict log directory to user only
chmod 700 ops/logs/

# Restrict log files to user only
chmod 600 ops/logs/*.log
```

For shared environments, ensure proper file permissions.

## Integration with Other Protocols

### Git Abstraction Protocol

From `docs/protocols/git-abstraction.md`:

Git operations are logged for traceability:
- `git_commit` logged after successful commits
- `git_error` logged after failures
- Retry attempts logged in `git-errors.log`

### Session Protocol

From `docs/protocols/session-protocol.md`:

Session lifecycle is logged:
- `session_start` when agent initializes
- `session_end` when agent pauses or exits
- State file loading logged in details

### Error Recovery Protocol

From `docs/protocols/error-recovery.md`:

Error scenarios are logged for debugging:
- Git errors with full details
- Recovery attempts
- User notifications

## Implementation Guidelines

### Logging Function

Create a centralized logging function for consistency:

```python
import json
from datetime import datetime

def log_action(action: str, entity: str = None, details: dict = None):
    """Log agent action to ops/logs/agent-actions.log"""
    entry = {
        "timestamp": datetime.utcnow().isoformat() + "Z",
        "action": action
    }
    if entity:
        entry["entity"] = entity
    if details:
        entry["details"] = details

    with open("ops/logs/agent-actions.log", "a") as f:
        f.write(json.dumps(entry) + "\n")
```

**Usage:**
```python
# Entity created
log_action("entity_created", entity="Acme Corp", details={
    "type": "client",
    "files": ["ENTITY_PROFILE.md", "ENTITY_ROADMAP.md"]
})

# Git commit
log_action("git_commit", details={
    "message": "Created Acme Corp entity",
    "files_count": 4
})

# Session start
log_action("session_start", details={
    "state_loaded": True,
    "pause_found": False
})
```

### Error Handling in Logging

If logging itself fails, fail gracefully:

```python
def log_action(action: str, entity: str = None, details: dict = None):
    try:
        # Logging logic here
        pass
    except Exception as e:
        # Don't crash agent if logging fails
        # Could fall back to stderr
        import sys
        print(f"Warning: Failed to log action {action}: {e}", file=sys.stderr)
```

## Related Protocols

- **Git Abstraction:** See `docs/protocols/git-abstraction.md` - git operations are logged
- **Error Recovery:** See `docs/protocols/error-recovery.md` - errors logged for debugging
- **Session Protocol:** See `docs/protocols/session-protocol.md` - session lifecycle logged
- **Entity Onboarding:** See `docs/protocols/entity-onboarding.md` - entity creation logged

---

*Protocol: action-logging*
*Version: 1.0*
*Last updated: 2026-01-24*
