# Reference Example: Digital Marketing Agency

This example shows what AnyManage looks like in action with realistic fake data. Study it before setting up your own system.

## What This Example Demonstrates

A small digital marketing agency using AnyManage to manage three clients. Each client is at a different stage, so you can see what the system looks like at various points in your workflow.

## The Three Clients

### 1. Sunrise Digital (Just Onboarded)

**Stage:** Minimal - fresh from "Add new client Sunrise Digital"

This is what your system looks like right after adding a new client:
- Profile has basic info filled in
- Roadmap has one initial project
- No processed notes yet
- No specialist work yet

**Why look at this:** See the starting point before you do anything else.

### 2. Bright Path Consulting (Notes Processed)

**Stage:** Intermediate - has processed meeting notes

This client has gone through a few meetings. Their files show:
- Profile populated with extracted facts (budget, goals, pain points)
- Roadmap with tasks extracted from notes
- Knowledge folder has learned context
- One archived processed note

**Why look at this:** See what happens after "Process notes for Bright Path"

### 3. Metro Events Co (Full Workflow)

**Stage:** Complete - has specialist deliverables

This client has the full experience:
- Complete profile with extensive details
- Roadmap with multiple projects at various stages
- Assessment completed (AUDIT_FINDINGS document)
- Strategy developed (STRATEGIC_PLAN document)
- Rich knowledge context from specialist work

**Why look at this:** See what assessment and strategy specialists produce.

## How to Explore

**Suggested order:**

1. Start with `entities/Sunrise Digital/` - see the baseline
2. Compare to `entities/Bright Path Consulting/` - notice what changed
3. Study `entities/Metro Events Co/deliverables/` - read the specialist outputs

**Key files to examine:**

| File | What It Shows |
|------|---------------|
| `entities/[Name]/ENTITY_PROFILE.md` | Client information (compare across all three) |
| `entities/[Name]/ENTITY_ROADMAP.md` | Work tracking (notice task progression) |
| `entities/[Name]/knowledge/LEARNED_CONTEXT.md` | What the system learned |
| `entities/[Name]/deliverables/*.md` | Specialist outputs (Metro only) |

## How This Maps to Your Usage

| When You... | You Get Results Like... |
|-------------|------------------------|
| "Add new client [Name]" | Sunrise Digital's structure |
| "Process notes for [Name]" | Bright Path's populated profile |
| "Run assessment for [Name]" | Metro's AUDIT_FINDINGS document |
| "Develop strategy for [Name]" | Metro's STRATEGIC_PLAN document |

## This Is Fake Data

All client names, contacts, and details are fictional. The files demonstrate realistic content structure, not real businesses.

---

*This example is read-only. To create your own setup, clone the main repo and start fresh.*
