# Entity Onboarding Protocol

Complete documentation for creating and onboarding new entities.

## Overview

This protocol handles the creation of new entities through natural language commands or slash commands. The goal is minimal friction - users specify a name, and a complete folder structure with templates is created immediately.

**Key Principles:**
- Minimal creation first, enhancement after
- Normalized naming for consistency
- Immediate folder creation with no upfront questions
- Duplicate detection before creation
- Helpful next-step suggestions after creation

## Trigger Detection

The agent should recognize these patterns as entity creation requests:

### Natural Language Patterns

The agent should recognize the configured entity type from CONFIG.md. If `entity_type` is "client":

| Pattern | Example |
|---------|---------|
| "Add new [entity_type] [Name]" | "Add new client Acme Corp" |
| "Create [entity_type] [Name]" | "Create client Beta Industries" |
| "New [entity_type] [Name]" | "New client Gamma LLC" |
| "Create new [Name]" | "Create new Delta Partners" |
| "Add [Name]" | "Add Epsilon Group" |

For other entity types (project, patient, product), the same patterns apply:
- "Add new project Alpha" (when entity_type is "project")
- "Add new patient Smith" (when entity_type is "patient")

### Slash Command

```
/new [Name]
```

Example: `/new Acme Corp`

### Entity Name Extraction

Extract the entity name from the user's input:

- **"Add new client Acme Corp"** → Entity name: "Acme Corp"
- **"Create entity Beta Industries"** → Entity name: "Beta Industries"
- **"/new Gamma LLC"** → Entity name: "Gamma LLC"

Preserve multi-word names and handle various formats (all caps, lowercase, mixed case).

## Name Normalization

Before creating the entity folder, normalize the entity name to title case for consistency.

### Normalization Rules

| Input Format | Normalized Output | Rule |
|--------------|-------------------|------|
| "ACME CORP" | "Acme Corp" | Convert all caps to title case |
| "acme corp" | "Acme Corp" | Convert all lowercase to title case |
| "AcMe CoRp" | "Acme Corp" | Standardize mixed case to title case |
| "acme-corp" | "Acme-Corp" | Preserve hyphens, capitalize words |
| "o'reilly media" | "O'Reilly Media" | Handle apostrophes correctly |

### Special Cases

**Acronyms:** Preserve common acronyms when detected
- "IBM" → "IBM" (stay uppercase)
- "AT&T Solutions" → "AT&T Solutions"

**Hyphens:** Capitalize both parts
- "beta-gamma" → "Beta-Gamma"

**Apostrophes:** Capitalize after apostrophes
- "o'brien" → "O'Brien"

**Multiple spaces:** Collapse to single space
- "Acme  Corp" → "Acme Corp"

### Folder Name = Normalized Name

The entity folder is named exactly as the normalized entity name:

```
entities/Acme Corp/
entities/Beta Industries/
entities/O'Reilly Media/
```

## Duplicate Detection

Before creating a new entity, check if a similar entity already exists.

### Check Mechanism

1. List all existing entities in `entities/` folder
2. Compare the normalized entity name against existing names
3. Perform both exact match and fuzzy match (>80% similarity)

### Exact Match

If an entity with the exact same name already exists:

**Agent response:**
```
Entity 'Acme Corp' already exists. Did you mean to update it instead?
```

**Wait for user clarification:**
- "Update it" → Continue with profile/note updates
- "Create anyway" → Ask for different name: "What should I call this one?"
- "Show me Acme" → Display existing entity profile

### Similar Match (Fuzzy)

If a similar name exists (>80% similarity):

**Examples:**
- User wants "Acme Corp" but "Acme Corporation" exists
- User wants "Beta" but "Beta Industries" exists

**Agent response:**
```
Found similar entity 'Acme Corporation'. Create new 'Acme Corp' anyway or use existing?
```

**Wait for user clarification:**
- "Create anyway" → Proceed with new entity
- "Use existing" → Work with the existing entity
- "Show both" → Display both names and let user choose

### No Match

If no similar entities exist, proceed directly to folder creation.

## Folder Creation

Create the complete entity folder structure with templates.

### Standard Folder Structure

```
entities/[Normalized Name]/
  ENTITY_PROFILE.md
  ENTITY_ROADMAP.md
  DIGEST.md
  notes/
    .gitkeep
  notes/archive/
    .gitkeep
  knowledge/
    .gitkeep
  deliverables/
    .gitkeep
  internal/
    .gitkeep
```

### Creation Steps

**Step 1: Create main folder**
```bash
mkdir -p "entities/[Name]"
```

**Step 2: Create subfolders**
```bash
mkdir -p "entities/[Name]/notes"
mkdir -p "entities/[Name]/notes/archive"
mkdir -p "entities/[Name]/knowledge"
mkdir -p "entities/[Name]/deliverables"
mkdir -p "entities/[Name]/internal"
```

**Step 3: Copy and populate ENTITY_PROFILE.md**
- Copy from `templates/ENTITY_PROFILE_TEMPLATE.md`
- Replace `[Entity Name]` placeholder with actual entity name
- Update "Profile created" date to current date
- Save to `entities/[Name]/ENTITY_PROFILE.md`

**Step 4: Copy and populate ENTITY_ROADMAP.md**
- Copy from `templates/ENTITY_ROADMAP_TEMPLATE.md`
- Replace `[Entity Name]` placeholder with actual entity name
- Update "Created" date to current date
- Save to `entities/[Name]/ENTITY_ROADMAP.md`

**Step 5: Create initial DIGEST.md**
- Copy from `templates/DIGEST_TEMPLATE.md`
- Replace `[Entity Name]` placeholder with actual entity name
- Replace `[Date]` with current date
- Fill in what's known (may be minimal — just entity name and "Onboarding" phase)
- Leave unknown sections with template placeholders
- Save to `entities/[Name]/DIGEST.md`

The initial digest will be sparse — that's expected. It gets populated as the user adds profile details, processes notes, and works with the entity. The first meaningful refresh typically happens after note processing or profile building.

**Step 6: Create .gitkeep files**
- Create empty `entities/[Name]/notes/.gitkeep`
- Create empty `entities/[Name]/notes/archive/.gitkeep`
- Create empty `entities/[Name]/knowledge/.gitkeep`
- Create empty `entities/[Name]/deliverables/.gitkeep`
- Create empty `entities/[Name]/internal/.gitkeep`

**Folder purposes:**
- `notes/` - Meeting notes, communications, and other entity-related notes
- `notes/archive/` - Processed notes that have been triaged
- `knowledge/` - Learned context captured by specialists during their work
- `deliverables/` - Specialist work products (assessments, strategies, reports)
- `internal/` - Internal-only content not shared with entity (pricing, margins, internal notes)

The `internal/` folder and any files prefixed with `INTERNAL_` are automatically excluded from client-facing deliverables by specialists.

### Timestamp Format

Use consistent date format throughout:
```
*Created: 2026-01-24*
*Last updated: 2026-01-24*
```

## Master Roadmap Update

After creating the entity folder, add the new entity to the master `ROADMAP.md`.

### Insertion Location

Add to the **Medium Priority** section by default (unless user specified a priority).

### Format

Use checkbox syntax with entity name and brief status:

```markdown
## Medium Priority

- [ ] Acme Corp - Just onboarded
- [ ] Beta Industries - Profile setup in progress
```

### Existing Entities

If the section already has entities, append the new one to the end of the list.

## Confirmation

After successful creation, confirm to the user and suggest next steps.

### Confirmation Message Template

```
Created [Entity Name] with profile and roadmap.
```

### Next Step Suggestions

Immediately follow confirmation with helpful suggestions:

```
Want to add some details? Process notes? Or type 'help' to see what else you can do.
```

**Alternative suggestions based on context:**
- "Add some background about Acme Corp?"
- "Have meeting notes to process?"
- "Want to set up their profile now?"

**Do NOT:**
- Ask questions before creating (no "What industry are they in?")
- Require information upfront (create first, enhance after)
- Wait for user to ask what to do next (proactively suggest)

### Example Flow

**User input:**
```
Add new client Acme Corp
```

**Agent actions:**
1. Normalize name: "Acme Corp"
2. Check for duplicates: None found
3. Create folder structure
4. Copy templates with name replacement
5. Add to ROADMAP.md
6. Confirm and suggest

**Agent response:**
```
Created Acme Corp with profile and roadmap.

Want to add some details about them? Or I can process meeting notes if you have any.
```

## Error Handling

Handle common failure scenarios gracefully.

### Folder Creation Fails

**Possible causes:**
- Permissions issue
- Disk space
- Invalid characters in name

**Agent response:**
```
Couldn't create the entity folder. Can you try again, or check if the name has special characters?
```

### Template Not Found

**Fallback:** Use minimal inline template

**Minimal ENTITY_PROFILE.md:**
```markdown
# [Entity Name] Profile

## Entity Information

| Field | Value |
|-------|-------|
| **Legal Name** | [Full legal entity name] |
| **Industry** | [Industry/vertical] |
| **Website** | [https://example.com] |

---

## Key Contacts

| Name | Role | Email | Notes |
|------|------|-------|-------|
| [Contact Name] | [Title/Role] | [email@example.com] | [Primary contact] |

---

## Notes

### Background Context

[Important background information and context]

---

*Profile created: [Date]*
*Last updated: [Date]*
```

**Minimal ENTITY_ROADMAP.md:**
```markdown
# [Entity Name] Roadmap

## Active Work

- [ ] [Task description]

## Upcoming

- [ ] [Planned task]

## Completed

- [x] [Done task]

---

*Created: [Date]*
*Last updated: [Date]*
```

**Agent note:** Log template missing to `ops/logs/` for admin review, but don't expose to user.

### Duplicate Found After Creation Starts

**Rare edge case:** Another session creates same entity simultaneously

**Agent response:**
```
Looks like 'Acme Corp' was just created. Using the existing one instead.
```

Then continue as if using existing entity.

## Example Interactions

### Example 1: Basic Creation

**User:**
```
Add new client Acme Corp
```

**Agent:**
```
Created Acme Corp with profile and roadmap.

Want to add some details about them? Or I can process meeting notes if you have any.
```

### Example 2: Name Normalization

**User:**
```
Create entity BETA INDUSTRIES
```

**Agent:**
```
Created Beta Industries with profile and roadmap.

Want to add some background about Beta Industries?
```

### Example 3: Exact Duplicate

**User:**
```
Add new client Acme Corp
```

**Agent (Acme Corp already exists):**
```
Entity 'Acme Corp' already exists. Did you mean to update it instead?
```

**User:**
```
Yes, update it
```

**Agent:**
```
Got it. What would you like to update about Acme Corp?
```

### Example 4: Similar Match

**User:**
```
New client Acme
```

**Agent (Acme Corp already exists):**
```
Found similar entity 'Acme Corp'. Create new 'Acme' anyway or use existing?
```

**User:**
```
Use existing
```

**Agent:**
```
Working with Acme Corp. What do you need?
```

### Example 5: Slash Command

**User:**
```
/new Gamma LLC
```

**Agent:**
```
Created Gamma LLC with profile and roadmap.

Have any notes to process or details to add?
```

### Example 6: Special Characters

**User:**
```
Add O'Reilly Media
```

**Agent:**
```
Created O'Reilly Media with profile and roadmap.

Want to set up their profile or process some notes?
```

## Integration with Other Workflows

### After Onboarding → Profile Building

User can immediately enhance the entity profile:
```
User: Add new client Acme Corp
Agent: Created Acme Corp with profile and roadmap. Want to add some details?
User: Yes, they're a healthcare tech company with 200 employees
Agent: Added to profile. Anything else?
```

### After Onboarding → Note Processing

User can process notes right away:
```
User: Create entity Beta Industries
Agent: Created Beta Industries with profile and roadmap. Have meeting notes to process?
User: Yes, process notes for Beta
Agent: [Reads notes from entities/Beta Industries/notes/ or prompts for input]
```

### After Onboarding → Status Check

User can verify creation:
```
User: Add new client Gamma
Agent: Created Gamma with profile and roadmap. Want to see the profile?
User: Show Gamma's status
Agent: [Displays ENTITY_ROADMAP.md content]
```

## Commit Behavior

Entity creation triggers an automatic git commit (invisible to user).

### Commit Message Format

```
feat(entity): onboard [Entity Name]

- Created entity folder structure
- Added profile and roadmap from templates
- Added to master ROADMAP.md
```

### Commit Timing

Commit immediately after successful creation, before showing confirmation to user.

### Commit Failure Handling

If git commit fails:
- Log error to `ops/logs/error.log`
- DO NOT expose git terminology to user
- Files are still created (user can work with them)
- Retry commit on next operation

**User never sees:** "Git commit failed" or "Repository error"

**User might see (only if persistent):** "Couldn't save changes automatically. Your work is safe, but let me know if you see this again."

## Related Protocols

- **Profile Building:** See `docs/protocols/profile-building.md` (planned for 03-02)
- **Note Processing:** See `docs/protocols/note-processing.md` (planned for 03-03)
- **Session Protocol:** See `docs/protocols/session-protocol.md`
- **Command Patterns:** See `docs/protocols/command-patterns.md`

---

*Protocol: entity-onboarding*
*Version: 1.0*
*Last updated: 2026-01-24*
