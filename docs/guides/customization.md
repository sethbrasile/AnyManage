# Customization Guide

Agent PM is designed to adapt to your industry. Out of the box, it manages "clients" for a marketing agency. But you can change it to manage projects, products, patients, engagements, or anything else you track.

This guide walks through every customization point.

## Change Your Entity Type

The most important customization: what are you managing?

### Edit CONFIG.md

Open `CONFIG.md` in the project root. You'll see:

```markdown
# Configuration

## Entity Type

| Setting | Value |
|---------|-------|
| **Singular** | client |
| **Plural** | clients |
| **Display name** | Client |
```

Change these values to match your industry:

**For a software team managing projects:**
```markdown
| **Singular** | project |
| **Plural** | projects |
| **Display name** | Project |
```

**For healthcare managing patients:**
```markdown
| **Singular** | patient |
| **Plural** | patients |
| **Display name** | Patient |
```

**For consulting managing engagements:**
```markdown
| **Singular** | engagement |
| **Plural** | engagements |
| **Display name** | Engagement |
```

After changing CONFIG.md, the AI will use your terminology:
- "Add new project Alpha" instead of "Add new client Alpha"
- "Process notes for patient Smith" instead of "Process notes for client Smith"

## Customize Templates

Templates define what documents look like when you create new entities.

### Template Files

Located in `templates/`:

```
templates/
  ENTITY_PROFILE_TEMPLATE.md   <- Profile document
  ENTITY_ROADMAP_TEMPLATE.md   <- Task tracking
  VOICE_TRAINING_EXERCISE.md   <- Voice training workflow
```

### Modify the Profile Template

The profile template defines what information you track about each entity. Open `templates/ENTITY_PROFILE_TEMPLATE.md`:

```markdown
# [Entity Name] Profile

## Entity Information

| Field | Value |
|-------|-------|
| **Legal Name** | [Full legal entity name] |
| **Industry** | [Industry/vertical] |
| **Website** | [https://example.com] |
```

Change the sections to match what you need to track.

**Example: Software Project Profile**

```markdown
# [Entity Name] Profile

## Project Information

| Field | Value |
|-------|-------|
| **Project Code** | [Internal code] |
| **Status** | [Active/On-hold/Complete] |
| **Start Date** | [YYYY-MM-DD] |
| **Target Launch** | [YYYY-MM-DD] |

---

## Technical Stack

| Component | Technology |
|-----------|------------|
| **Frontend** | [React/Vue/etc.] |
| **Backend** | [Node/Python/etc.] |
| **Database** | [PostgreSQL/etc.] |

---

## Team

| Name | Role | Notes |
|------|------|-------|
| [Name] | [Role] | [Primary contact] |
```

**Example: Healthcare Patient Profile**

```markdown
# [Entity Name] Profile

## Patient Information

| Field | Value |
|-------|-------|
| **Patient ID** | [Medical record number] |
| **Date of Birth** | [YYYY-MM-DD] |
| **Primary Physician** | [Name] |

---

## Emergency Contact

| Name | Relationship | Phone |
|------|--------------|-------|
| [Name] | [Spouse/Parent/etc.] | [Number] |

---

## Notes

### Medical History

[Relevant background]

### Current Treatment

[Active care plan]
```

### Placeholder Convention

Use `[Placeholder Text]` format for fields users need to fill in:
- `[Entity Name]` - Will be replaced with actual name when created
- `[Contact Name]` - User fills this in later
- `[YYYY-MM-DD]` - Date format hint

The AI recognizes this format and knows these are meant to be replaced.

### Modify the Roadmap Template

The roadmap template defines how you track tasks and progress. Open `templates/ENTITY_ROADMAP_TEMPLATE.md`:

```markdown
# [Entity Name] Roadmap

## Active Work

- [ ] [Task description]

## Upcoming

- [ ] [Planned task]

## Completed

- [x] [Done task]
```

You might want different sections based on your workflow:

**Agile/Sprint-based:**
```markdown
# [Entity Name] Roadmap

## Current Sprint (Sprint 5)

- [ ] [User story or task]
- [-] [In progress item]

## Backlog

- [ ] [Future work]

## Done (Sprint 4)

- [x] [Completed item]
```

**Milestone-based:**
```markdown
# [Entity Name] Roadmap

## Phase 1: Discovery

- [x] Requirements gathering
- [x] Stakeholder interviews

## Phase 2: Development (Current)

- [-] Build core features
- [ ] Integration testing

## Phase 3: Launch

- [ ] User acceptance testing
- [ ] Go-live
```

## Add Domain Knowledge

Domain knowledge lives in `docs/domain/`. This is where you put industry-specific information the AI needs to do its job well.

### What Goes Here

- Industry terminology and definitions
- Best practices for your field
- Standard processes and workflows
- Compliance requirements
- Reference materials

### Example: Marketing Agency

```
docs/domain/
  services.md          <- Services you offer
  deliverables.md      <- Standard deliverable types
  client-lifecycle.md  <- How client relationships progress
```

**services.md:**
```markdown
# Agency Services

## Service Categories

### Digital Marketing
- SEO optimization
- Paid advertising (Google, Meta, LinkedIn)
- Content marketing

### Social Media Management
- Strategy development
- Content creation
- Community management

### Brand Development
- Brand strategy
- Visual identity
- Messaging frameworks
```

### Example: Software Team

```
docs/domain/
  tech-stack.md        <- Standard technologies
  deployment.md        <- How you deploy
  code-standards.md    <- Coding conventions
```

### Example: Healthcare

```
docs/domain/
  protocols.md         <- Clinical protocols
  compliance.md        <- HIPAA requirements
  terminology.md       <- Medical terms
```

The AI reads these files and applies that knowledge when helping you. If you describe your services in `services.md`, the AI can reference them when creating assessments or strategies.

## Add Agency/Team Preferences

Team-level preferences live in `ops/`. This is how your operation works, not specific to any client.

### What Goes Here

```
ops/
  playbooks/           <- Standard operating procedures
  voice/               <- Voice training profiles
  preferences.md       <- Team preferences and conventions
```

### Example: preferences.md

```markdown
# Team Preferences

## Communication Style

- Always use client's preferred name (check profile)
- Respond within 24 hours on weekdays
- Use plain English, avoid jargon

## Meeting Notes Format

When processing meeting notes, look for:
- Action items (assigned to specific people)
- Decisions made
- Questions to follow up on
- Next meeting date

## Deliverable Standards

- All client-facing documents need review before sending
- Use templates from templates/ folder
- Include date in filename for versioning
```

## Create Custom Skills

Skills are reusable workflows. Agent PM comes with basic skills (process-notes, get-status, weekly-review), but you can create your own.

### Skill Location

Skills go in `.github/skills/` (works across all agents) or `.claude/skills/` (Claude Code only).

### Skill Structure

Each skill is a folder with a SKILL.md file:

```
.github/skills/
  my-custom-skill/
    SKILL.md           <- Skill definition
    scripts/           <- Optional: helper scripts
    references/        <- Optional: reference materials
```

### Creating a Skill

See [SKILL_AUTHORING_GUIDE.md](../SKILL_AUTHORING_GUIDE.md) for the complete guide.

**Quick example - a "client-handoff" skill:**

```markdown
---
name: client-handoff
description: Prepare handoff documentation when transferring a client to another team member
triggers:
  - "hand off [entity] to [person]"
  - "transfer [entity]"
  - "client handoff for [entity]"
---

# Client Handoff Skill

## Purpose

Generate a comprehensive handoff document when transferring an entity to another team member.

## Instructions

When triggered:

1. **Read entity profile** - Load entities/[Entity]/ENTITY_PROFILE.md
2. **Read entity roadmap** - Load entities/[Entity]/ENTITY_ROADMAP.md
3. **Summarize active work** - List all uncompleted tasks
4. **Note key contacts** - Pull from profile
5. **Include recent context** - Check notes/ for last 3 interactions
6. **Generate handoff document** - Create entities/[Entity]/deliverables/HANDOFF_[DATE].md

## Output Format

```markdown
# Handoff: [Entity Name]

**Prepared for:** [Receiving team member]
**Date:** [Today]
**Prepared by:** [Current owner]

## Entity Summary

[One paragraph overview]

## Key Contacts

| Name | Role | Best for |
|------|------|----------|
[From profile]

## Active Work

[Uncompleted tasks from roadmap]

## Recent Context

[Summary of last 3 notes]

## Important Notes

[Anything critical the new owner should know]
```
```

## Industry Examples

Here's how to configure Agent PM for different industries:

### Marketing Agency (Default)

**CONFIG.md:**
```markdown
| **Singular** | client |
| **Plural** | clients |
```

**What to track:** Company info, key contacts, service agreements, campaign details, brand guidelines.

**Domain knowledge:** Services offered, deliverable types, industry terminology.

### Software Development Team

**CONFIG.md:**
```markdown
| **Singular** | project |
| **Plural** | projects |
```

**What to track:** Tech stack, milestones, team members, dependencies, deployment status.

**Domain knowledge:** Coding standards, deployment processes, tool preferences.

### Consulting Firm

**CONFIG.md:**
```markdown
| **Singular** | engagement |
| **Plural** | engagements |
```

**What to track:** Scope, deliverables, billable hours, stakeholders, timeline.

**Domain knowledge:** Service offerings, methodology, proposal templates.

### Healthcare Practice

**CONFIG.md:**
```markdown
| **Singular** | patient |
| **Plural** | patients |
```

**What to track:** Medical history, current treatment, appointments, insurance, emergency contacts.

**Domain knowledge:** Clinical protocols, compliance requirements (HIPAA), terminology.

**Note:** For healthcare, be careful about what information you store. This system is local-first and git-tracked, but may not meet all compliance requirements for protected health information. Consult with compliance before using for sensitive medical data.

### Personal Note-Taking

**CONFIG.md:**
```markdown
| **Singular** | topic |
| **Plural** | topics |
```

**What to track:** Ideas, research, references, connections to other topics.

**Domain knowledge:** Your areas of interest, how you like information organized.

## Step-by-Step: Changing from Clients to Projects

Here's the exact process to convert a fresh Agent PM install from "clients" to "projects":

1. **Edit CONFIG.md:**
   ```markdown
   | **Singular** | project |
   | **Plural** | projects |
   | **Display name** | Project |
   ```

2. **Update ENTITY_PROFILE_TEMPLATE.md:**
   - Change header from "Entity Information" to "Project Information"
   - Add project-specific fields (start date, target launch, tech stack)
   - Remove client-specific fields (industry, company size)

3. **Update ENTITY_ROADMAP_TEMPLATE.md:**
   - Add sprint or milestone sections if you use them
   - Adjust task categories to match your workflow

4. **Create domain knowledge:**
   - Add `docs/domain/tech-stack.md` with your standard technologies
   - Add `docs/domain/processes.md` with your development workflow

5. **Test it:**
   ```
   Add new project Alpha
   ```
   Should create `entities/Alpha/` with your customized templates.

That's it. The AI reads your configuration and templates, so it automatically uses your terminology and document structure.

---

**Related guides:**
- [Skill discovery](skill-discovery.md) - See all available workflows
- [Getting started](getting-started.md) - How the system works
- [SKILL_AUTHORING_GUIDE](../SKILL_AUTHORING_GUIDE.md) - Create custom workflows
