# Profile Building Protocol

Complete documentation for incrementally updating entity profiles with natural language commands.

## Overview

This protocol handles adding and updating information in entity profiles through conversational requests. Users can add facts, update details, or refine information without needing to manually edit markdown files.

**Key Principles:**
- Smart placement based on fact type
- Silent updates with post-confirmation
- Replace conflicting information (new is authoritative)
- Mixed format support (tables, bullets, prose)
- No pre-confirmation prompts

## Trigger Detection

The agent should recognize these patterns as profile update requests:

### Natural Language Patterns

| Pattern | Example |
|---------|---------|
| "Add [fact] to [entity]'s profile" | "Add CEO is John Smith to Acme's profile" |
| "Update [entity]'s [field] to [value]" | "Update Acme's revenue to $5M" |
| "Add [fact] to [entity]" | "Add Sarah Chen as CFO to Beta Industries" |
| "[Entity] has [fact]" | "Acme Corp has 200 employees" |
| "Note that [entity] [fact]" | "Note that Beta prefers email communication" |

### Multiple Facts

Users may include multiple facts in one request:

```
Add to Acme Corp profile:
- CEO is John Smith
- Founded in 2015
- 200 employees
- Based in San Francisco
```

Process each fact individually, report placement for each.

### Entity Name Extraction

Extract the entity name using the same normalization rules as entity onboarding:
- Convert to title case
- Preserve hyphens, apostrophes, acronyms
- Match against existing entities/ folder names

If entity not found, suggest creating it first.

## Smart Placement

Map fact types to appropriate ENTITY_PROFILE.md sections. The goal is contextually intelligent placement without user needing to specify sections.

### Section Mapping

| Fact Type | Target Section | Format |
|-----------|----------------|--------|
| Contact information | Key Contacts | Table row |
| Name, role, email, phone | Key Contacts | Table row |
| Company size (employees) | Entity Information | Table cell |
| Revenue, funding | Entity Information | Table cell |
| Industry, vertical | Entity Information | Table cell |
| Founded, headquarters, website | Entity Information | Table cell |
| Annual goals, targets | Business Goals > Annual Goals | Table row |
| Current priorities | Business Goals > Current Priorities | Numbered list |
| Success metrics, KPIs | Business Goals > Success Metrics | Bullet list |
| Services provided | Our Relationship > Services Provided | Bullet list |
| Contract details | Our Relationship > Engagement Details | Table cell |
| Meeting cadence | Communication Preferences > Meeting Cadence | Table row |
| Preferred channels | Communication Preferences > Preferred Channels | Table row |
| Deadlines, key dates | Communication Preferences > Key Dates | Table row |
| Background information | Notes > Background Context | Prose paragraph |
| Working style, preferences | Notes > Working Style Preferences | Prose paragraph |
| General notes, reminders | Notes > Things to Remember | Bullet list |

### Ambiguous Facts

If fact type is unclear, use **Notes > Things to Remember** as the catch-all section.

**Examples of ambiguous facts:**
- "Acme likes detailed reports" → Could be Working Style or Things to Remember
- "Beta mentioned they're expanding" → Background or Things to Remember
- "John Smith is important" → Needs clarification (is he a contact? founder? decision maker?)

**When truly ambiguous:**
Ask user: "Should I add that to [section A] or [section B]?"

### Section Creation

If the target section doesn't exist in the profile, create it using the template structure.

**Example:** If "Business Goals > Annual Goals" section is missing:

```markdown
## Business Goals

### Annual Goals

| Goal | Target Metric | Timeline | Status |
|------|--------------|----------|--------|
| [New fact here] | [If available] | [If available] | [ ] Not started |
```

## Update Mechanics

Handle different format types based on section structure.

### Table Updates

**Adding new row:**
1. Parse existing table to identify columns
2. Extract relevant fields from user's fact
3. Fill available columns, use "[Unknown]" or "[Not specified]" for missing fields
4. Append row to table

**Example:** User says "Add Sarah Chen as CFO to Acme profile"

Before:
```markdown
| Name | Role | Email | Phone | Notes |
|------|------|-------|-------|-------|
| John Smith | CEO | john@acme.com | 555-0100 | Primary contact |
```

After:
```markdown
| Name | Role | Email | Phone | Notes |
|------|------|-------|-------|-------|
| John Smith | CEO | john@acme.com | 555-0100 | Primary contact |
| Sarah Chen | CFO | [Email not provided] | [Not specified] | Added 2026-01-24 |
```

**Updating existing row:**

If a contact already exists (match by name), update their information:

User says: "Update John Smith's email to jsmith@acme.com"

Before:
```markdown
| Name | Role | Email | Phone | Notes |
|------|------|-------|-------|-------|
| John Smith | CEO | john@acme.com | 555-0100 | Primary contact |
```

After:
```markdown
| Name | Role | Email | Phone | Notes |
|------|------|-------|-------|-------|
| John Smith | CEO | jsmith@acme.com | 555-0100 | Primary contact |
```

**Updating table cell (Entity Information):**

User says: "Acme Corp has 200 employees"

Before:
```markdown
| Field | Value |
|-------|-------|
| **Company Size** | [Number of employees] |
```

After:
```markdown
| Field | Value |
|-------|-------|
| **Company Size** | 200 employees |
```

### Bullet List Updates

**Adding new bullet:**
Simply append to the end of the list.

User says: "Add to Things to Remember: Acme prefers morning meetings"

Before:
```markdown
### Things to Remember

- Important detail to keep in mind
- Preference or requirement to remember
```

After:
```markdown
### Things to Remember

- Important detail to keep in mind
- Preference or requirement to remember
- Acme prefers morning meetings
```

**Removing placeholder bullets:**
If list still contains template placeholders like "[Important detail to keep in mind]", remove them when adding first real content.

### Numbered List Updates

**Adding to numbered list:**
Append with next number.

User says: "Add priority: Complete Q2 security audit"

Before:
```markdown
### Current Priorities

1. Launch new product line
2. Hire senior engineer
```

After:
```markdown
### Current Priorities

1. Launch new product line
2. Hire senior engineer
3. Complete Q2 security audit
```

### Prose Section Updates

**Adding to prose sections:**
Append new paragraph after existing content.

User says: "Note that Acme was referred by Beta Industries"

Before:
```markdown
### Background Context

Acme Corp was founded in 2015 by John Smith. They started as a small consulting firm and have grown to 200 employees.
```

After:
```markdown
### Background Context

Acme Corp was founded in 2015 by John Smith. They started as a small consulting firm and have grown to 200 employees.

Referred by Beta Industries. Strong relationship with their CEO who has worked with John Smith previously.
```

**Replacing placeholder prose:**
If section contains only template placeholder text like "[Important background information...]", replace it entirely with new content.

## Conflicting Information

When new information conflicts with existing information, replace old with new.

**Rationale:** User said "update", so the new information is authoritative.

### Replacement Strategy

**Identify conflict:**
- Same field in table (e.g., CEO name changed)
- Same metric (e.g., employee count updated)
- Contradictory statement (e.g., "prefers email" vs "prefers Slack")

**Replace silently:**
- Update old value with new value
- No history preservation in profile (profile shows current state)
- Confirm what was changed in response

**Example:** User says "Update Acme's CEO to Jane Doe"

Before:
```markdown
| Field | Value |
|-------|-------|
| **CEO** | John Smith |
```

After:
```markdown
| Field | Value |
|-------|-------|
| **CEO** | Jane Doe |
```

Confirmation: "Updated CEO to Jane Doe (was John Smith)"

### No History Tracking

Profile shows current state only. For historical tracking, users can check git history (invisible to them, but available for admin audit).

## Confirmation

After making updates, confirm what was added and where.

### Confirmation Message Template

```
Added to [Section Name]: [Brief summary of what was added]
```

### Multiple Updates

If multiple facts were added, list each:

```
Updated Acme Corp profile:
- Added to Key Contacts: Sarah Chen (CFO)
- Updated Entity Information: Company size to 200 employees
- Added to Things to Remember: Prefers morning meetings
```

### Silent Updates

**Do NOT:**
- Ask permission before making changes ("Should I add this to Key Contacts?")
- Confirm before updating ("Are you sure you want to change the CEO?")
- Show the user what you're about to do

**DO:**
- Make the update immediately
- Confirm after it's done
- Show what changed

**Rationale:** Per CONTEXT.md, users want efficient updates without unnecessary confirmation prompts. They can review changes in the profile if needed.

## Digest Update

After making profile changes, refresh the entity's `DIGEST.md` if the change affects information shown in the digest.

### When to Update

| Change Type | Digest Section Affected |
|-------------|------------------------|
| Entity info (industry, size, location, website) | At a Glance |
| Key contacts | At a Glance (if primary contact changed) |
| Business goals or priorities | Key Context (if significant shift) |
| Communication preferences | Key Context (if it changes how we work with them) |
| Background context, things to remember | Key Context (if significant) |

### When to Skip

Routine fact additions (adding a phone number, updating an email address) don't need a digest refresh. Only update when the change is meaningful enough to appear in the entity's summary view.

If `DIGEST.md` doesn't exist for the entity, create one from current state using `templates/DIGEST_TEMPLATE.md`.

Update the `<!-- Last refreshed: -->` date in the digest header when changes are made.

## Error Handling

Handle common failure scenarios gracefully.

### Entity Not Found

**User says:** "Add CEO John Smith to Acme profile"

**But:** No entity named "Acme" or similar exists

**Agent response:**
```
No entity named 'Acme' found. Create it first with 'Add new client Acme'?
```

If user confirms, create entity then return to ask for the fact again, or process it immediately if still in context.

### Profile File Corrupted

**Rare case:** ENTITY_PROFILE.md exists but is malformed (can't be parsed)

**Agent action:**
1. Log error to `ops/logs/error.log`
2. Offer to recreate from template

**Agent response:**
```
Acme Corp's profile seems corrupted. Want me to recreate it from the template? (Your existing data might be lost)
```

Wait for user confirmation before recreating.

### Ambiguous Fact Type

**User says:** "Add John Smith to Acme"

**Unclear:** Is John Smith a contact? The founder? An employee? A decision maker?

**Agent response:**
```
Where should I add John Smith? As a contact (with role)? Or as background information?
```

**Better approach for users:**
Suggest they be more specific: "Try 'Add John Smith as CEO to Acme' or 'Add John Smith as contact to Acme'"

## Example Interactions

### Example 1: Adding Contact Information

**User:**
```
Add Sarah Chen as CFO to Acme Corp profile
```

**Agent actions:**
1. Find entity: Acme Corp
2. Open ENTITY_PROFILE.md
3. Locate Key Contacts table
4. Add row with name "Sarah Chen", role "CFO"
5. Fill other columns with placeholders
6. Save file
7. Silent git commit

**Agent response:**
```
Added to Key Contacts: Sarah Chen (CFO)
```

### Example 2: Updating Company Information

**User:**
```
Update Acme's revenue to $5M annually
```

**Agent actions:**
1. Find entity: Acme Corp
2. Open ENTITY_PROFILE.md
3. Locate Entity Information > Revenue Range table cell
4. Update value from "[e.g., $1M-$5M annually]" to "$5M annually"
5. Save file
6. Silent git commit

**Agent response:**
```
Updated Entity Information: Revenue Range to $5M annually
```

### Example 3: Multiple Facts at Once

**User:**
```
Add to Beta Industries profile:
- CEO is Michael Torres
- Founded in 2018
- 150 employees
- Based in Austin, Texas
```

**Agent actions:**
1. Find entity: Beta Industries
2. Process each fact:
   - CEO → Entity Information table or Key Contacts (decide based on context)
   - Founded → Entity Information table
   - 150 employees → Entity Information table
   - Based in Austin → Entity Information table (Headquarters)
3. Save file
4. Silent git commit

**Agent response:**
```
Updated Beta Industries profile:
- Added to Key Contacts: Michael Torres (CEO)
- Updated Entity Information: Founded to 2018
- Updated Entity Information: Company Size to 150 employees
- Updated Entity Information: Headquarters to Austin, Texas
```

### Example 4: Adding Background Context

**User:**
```
Note that Acme was referred by Beta Industries and they have a strong relationship
```

**Agent actions:**
1. Find entity: Acme Corp
2. Open ENTITY_PROFILE.md
3. Locate Notes > Background Context
4. Append new paragraph
5. Save file
6. Silent git commit

**Agent response:**
```
Added to Background Context: Referral source and relationship with Beta Industries
```

### Example 5: Replacing Conflicting Information

**User:**
```
Update Acme's CEO to Jane Doe
```

**Current profile has:** CEO is John Smith

**Agent actions:**
1. Find entity: Acme Corp
2. Open ENTITY_PROFILE.md
3. Locate CEO field (could be in Entity Information or Key Contacts)
4. Replace "John Smith" with "Jane Doe"
5. Save file
6. Silent git commit

**Agent response:**
```
Updated Entity Information: CEO to Jane Doe (was John Smith)
```

### Example 6: Creating Missing Section

**User:**
```
Add annual goal: Increase revenue by 25% in Q2
```

**Current profile:** Business Goals section doesn't exist

**Agent actions:**
1. Find entity: [Entity name from context or explicit mention]
2. Open ENTITY_PROFILE.md
3. Section missing → create "Business Goals" with "Annual Goals" subsection
4. Add table with proper headers
5. Add goal as first row
6. Save file
7. Silent git commit

**Agent response:**
```
Created Business Goals section and added: Increase revenue by 25% in Q2
```

## Integration with Other Workflows

### After Entity Onboarding → Profile Building

User creates entity, then immediately adds details:

```
User: Add new client Acme Corp
Agent: Created Acme Corp with profile and roadmap. Want to add some details?
User: Yes, CEO is John Smith and they have 200 employees
Agent: Updated Acme Corp profile:
       - Added to Key Contacts: John Smith (CEO)
       - Updated Entity Information: Company Size to 200 employees
```

### Note Processing → Profile Building

When processing notes, extracted profile facts use this protocol:

```
User: Process notes for Acme Corp
Agent: [Reads notes, extracts facts]
Agent: Processed notes for Acme Corp:
       - Tasks added to roadmap (3)
       - Profile updated:
         - Added to Key Contacts: Sarah Chen (CFO)
         - Updated Entity Information: Revenue to $5M
       - Follow-ups (1)
```

See `docs/protocols/note-processing.md` for full note processing protocol.

### Profile Review → Updates

User can request to see profile, then make updates:

```
User: Show Acme's profile
Agent: [Displays ENTITY_PROFILE.md content]
User: Update the industry to Healthcare Technology
Agent: Updated Entity Information: Industry to Healthcare Technology
```

## Commit Behavior

Profile updates trigger automatic git commits (invisible to user).

### Commit Message Format

```
feat(profile): update [Entity Name] profile

- [What was added/updated]
- [What was added/updated]
```

**Example:**
```
feat(profile): update Acme Corp profile

- Added contact: Sarah Chen (CFO)
- Updated company size to 200 employees
```

### Commit Timing

Commit immediately after successful update, before showing confirmation to user.

### Batched Updates

If user provides multiple facts in one request, make all updates then commit once with all changes listed.

### Commit Failure Handling

If git commit fails:
- Log error to `ops/logs/error.log`
- DO NOT expose git terminology to user
- Profile is still updated (user can work with it)
- Retry commit on next operation

**User never sees:** "Git commit failed" or "Repository error"

**User might see (only if persistent):** "Couldn't save changes automatically. Your updates are in the profile, but let me know if you see this again."

## Related Protocols

- **Entity Onboarding:** See `docs/protocols/entity-onboarding.md`
- **Note Processing:** See `docs/protocols/note-processing.md` (planned for 03-03)
- **Session Protocol:** See `docs/protocols/session-protocol.md`
- **Command Patterns:** See `docs/protocols/command-patterns.md`

---

*Protocol: profile-building*
*Version: 1.0*
*Last updated: 2026-01-24*
