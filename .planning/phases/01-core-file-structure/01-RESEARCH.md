# Phase 1: Core File Structure - Research

**Researched:** 2026-01-24
**Domain:** Markdown-based project management systems, file organization
**Confidence:** HIGH

## Summary

This phase establishes the foundational file structure for a markdown-based project management system targeting non-technical users. The research confirms that markdown-based systems are a well-established approach for human-readable, AI-parseable project management, with clear best practices for folder hierarchies, naming conventions, and task tracking syntax.

The reference implementation (ppmc) demonstrates a proven pattern: entity-centric folders with standardized subfolders, markdown templates for profiles and roadmaps, and a master dashboard using checkbox syntax for status tracking. This approach aligns with industry best practices for self-documenting file structures and separation of concerns.

Key insight: The strength of markdown-based systems lies in their simplicity and transparency. Files are human-readable text, require no proprietary software, work across all AI agents, and enable version control without technical knowledge. The challenge is creating structure that's intuitive enough for non-technical users while being consistent enough for automation.

**Primary recommendation:** Follow the proven ppmc pattern with semantic folder naming (entities/, templates/, ops/, .planning/), standardized entity subfolders, and minimal frontmatter. Prioritize clarity over cleverness—folder names should be instantly obvious to someone who has never seen the system before.

## Standard Stack

The established approach for markdown-based project management:

### Core
| Technology | Version | Purpose | Why Standard |
|------------|---------|---------|--------------|
| Markdown | CommonMark + GFM | All data storage | Universal format, human-readable, no dependencies |
| YAML frontmatter | 1.2 | Optional metadata | Widely supported, simple key-value pairs |
| Git | 2.x | Version control (invisible to user) | Industry standard, works across all platforms |
| Filesystem | Native OS | Storage backend | Zero setup, works everywhere |

### Supporting
| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| None | N/A | This phase requires no external libraries | Pure file system operations |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Markdown files | Database (SQLite, JSON) | Database adds complexity, requires tooling, not human-editable |
| Filesystem | CMS/Platform | Platform lock-in, requires hosting, not portable |
| Manual git | Automatic git wrapper | Manual git too complex for target users; automation required |

**Installation:**
No installation required—uses native filesystem and markdown text files.

## Architecture Patterns

### Recommended Project Structure

Based on reference implementation (ppmc) and industry best practices:

```
project-root/
├── entities/                    # Core data - all managed entities
│   └── [Entity Name]/          # One folder per entity (e.g., "Guardian 6")
│       ├── ENTITY_PROFILE.md   # Profile document (standard template)
│       ├── ENTITY_ROADMAP.md   # Roadmap document (standard template)
│       ├── assets/             # Entity-specific files
│       ├── notes/              # Meeting notes, communications
│       ├── planning/           # Strategy, research docs
│       ├── current_state/      # Audits, assessments
│       ├── execution/          # Campaign materials, deliverables
│       └── tracking/           # Reports, analytics
├── templates/                  # Reusable document templates
│   ├── ENTITY_PROFILE_TEMPLATE.md
│   ├── ENTITY_ROADMAP_TEMPLATE.md
│   └── [other templates]
├── ops/                        # Agency/team operations
│   ├── team_profiles/
│   └── processes/
├── .planning/                  # System planning (hidden from users)
│   └── phases/                 # Phase planning docs
├── CONFIG.md                   # System configuration (entity type)
└── ROADMAP.md                  # Master dashboard (all entities)
```

**Key design decisions from research:**

1. **Semantic naming**: Folder names communicate purpose instantly (entities/, templates/, ops/)
2. **Flat entity list**: All entities at same level under entities/ for simplicity
3. **Standard subfolders**: Every entity has the same subfolder structure for predictability
4. **Self-documenting**: No need to read documentation to understand structure
5. **Hidden complexity**: .planning/ prefix hides system internals from end users

### Pattern 1: Entity-Centric Organization

**What:** Each managed entity gets a dedicated folder with standardized subfolders

**When to use:** Always—this is the core organizational pattern

**Example:**
```
entities/
├── Guardian 6/
│   ├── ENTITY_PROFILE.md       # Who they are, what they do
│   ├── ENTITY_ROADMAP.md       # What we're doing for them
│   ├── assets/                 # Their files
│   ├── notes/                  # Communications with them
│   └── planning/               # Strategy for them
└── Bloom Natural/
    ├── ENTITY_PROFILE.md
    ├── ENTITY_ROADMAP.md
    └── [same subfolder structure]
```

**Why this works:**
- Separation of concerns: Each entity's data is isolated
- Scalability: Adding entities doesn't affect existing structure
- Clarity: Everything about an entity is in one place
- Predictability: Same structure = no guessing where files go

**Source:** Reference implementation at /Users/seth/Documents/GitHub/ppmc

### Pattern 2: Template-Based Document Creation

**What:** Master templates in templates/ folder, instantiated per entity

**When to use:** For any repeatable document type (profiles, roadmaps, reports)

**Example:**
```markdown
# templates/ENTITY_PROFILE_TEMPLATE.md

## Company Information
- **Legal Name**: [Full Company Name]
- **Industry**: [Industry/Vertical]
- **Company Size**: [# of employees]

[... rest of template with clear placeholders ...]
```

**Why this works:**
- Consistency: All profiles have same structure
- Onboarding: New users see what information to provide
- Automation: Templates enable programmatic entity creation
- Evolution: Update template, all future entities benefit

**Source:** Reference implementation templates/

### Pattern 3: Master Dashboard with Checkbox Tracking

**What:** Single ROADMAP.md file tracks all entities with checkbox status

**When to use:** Always—this is the top-level view of all work

**Example:**
```markdown
# Marketing Agency Project Roadmap

## Progress Tracking Convention
- `[ ]` = Todo (no timestamp needed)
- `[-]` = In Progress (add MM/DD/YY when started)
- `[x]` = Completed (add MM/DD/YY when finished)

## Active Clients
### High Priority Clients
- [-] [Guardian 6] - Planning & Scoping - 2026-01-12
- [-] [Bloom Natural] - e-commerce strategy - 2026-01-12
- [x] [Previous Client] - Campaign completed ✅ 2025-12-20

## Recently Completed
- [x] **[Client] Marketing Audit** - Identified 12 improvement areas ✅ 2025/12/20
```

**Why this works:**
- Single source of truth for all entity status
- GitHub/GitLab/most markdown renderers show interactive checkboxes
- Checkbox syntax is simple and universal: `[ ]`, `[-]`, `[x]`
- Timestamps show momentum and track duration
- Emoji ✅ provides visual confirmation of completion

**Source:** Reference implementation ROADMAP.md + [Markdown Task Lists and Checkboxes Guide](https://blog.markdowntools.com/posts/markdown-task-lists-and-checkboxes-complete-guide)

### Pattern 4: Minimal Frontmatter Strategy

**What:** Use YAML frontmatter sparingly—only when metadata adds clear value

**When to use:** For entity type configuration, not for every document

**Example:**
```yaml
# CONFIG.md frontmatter example
---
entity_type: client
entity_type_plural: clients
entity_profile_name: CLIENT_PROFILE
entity_roadmap_name: PROJECT_ROADMAP
---

# Agent PM Configuration

This system manages **clients** for a marketing agency.
```

**Why minimal frontmatter:**
- Human readability: Less YAML = more accessible to non-technical users
- Simplicity: Metadata in document body is often clearer
- Portability: Not all markdown tools handle frontmatter consistently
- Maintenance: Less structured data = less to break

**When frontmatter IS valuable:**
- Configuration files (entity type, system settings)
- Automated processing needs (template metadata)
- NOT for general documents like profiles or roadmaps

**Source:** [Best Practices for Frontmatter in Markdown](https://www.ssw.com.au/rules/best-practices-for-frontmatter-in-markdown)

## Anti-Patterns to Avoid

### Anti-Pattern 1: Over-Nesting Folders

**What:** Creating too many nested folder levels (e.g., entities/active/high-priority/2026/Q1/Client/)

**Why bad:**
- User confusion: Hard to navigate deep hierarchies
- Friction: Too many clicks to reach files
- Maintenance: Structure becomes brittle as categories change
- Scales poorly: Adding entities requires deciding on multiple categories

**Instead:** Keep entity folders flat (entities/[Name]/). Use tags or categories in markdown if needed.

**Source:** [Folder Structure Best Practices](https://compresto.app/blog/folder-structure-best-practices) - "Extreme depth creates navigation difficulty"

### Anti-Pattern 2: Inconsistent Naming Conventions

**What:** Mixing naming styles (ClientProfile.md, client_roadmap.md, project-notes.md)

**Why bad:**
- Cognitive load: Users must remember multiple conventions
- Sorting issues: Files don't group logically
- Automation difficulty: Hard to programmatically find files
- Unprofessional appearance: Signals lack of thoughtfulness

**Instead:** Choose ONE convention and document it:
- Recommendation: ALL_CAPS for core documents (ENTITY_PROFILE.md), lowercase-with-hyphens for others
- Rationale: All-caps stands out visually, indicates "important system file"

**Source:** [File Naming Conventions](https://resources.ascented.com/ascent-blog/tips-file-and-folder-naming-convention)

### Anti-Pattern 3: Using Spaces in Filenames

**What:** Creating entities like "Guardian 6/" or files like "Meeting Notes.md"

**Why bad:**
- Command-line issues: Spaces require quoting in terminal commands
- URL encoding: Spaces become %20 in URLs
- Cross-platform problems: Some systems handle spaces differently
- **CONFLICT:** Reference implementation uses spaces successfully!

**Resolution for this project:**
- Entity folder names: Allow spaces (matches reference implementation, more user-friendly)
- File names: Avoid spaces in filenames (use underscores or hyphens)
- Rationale: Folder names are human-facing (Guardian 6 is clearer than Guardian-6), but file operations work better without spaces

**Source:** [Folder Structure and Naming Conventions](https://worldbank.github.io/template/docs/folders-and-naming.html) - "Never use spaces in your project path and filenames"

### Anti-Pattern 4: Premature Optimization of Structure

**What:** Trying to design the "perfect" folder structure before using the system

**Why bad:**
- Paralysis: Spend time planning instead of doing
- Wrong abstractions: Structure optimized for imagined problems, not real ones
- User confusion: Complex structure users don't understand yet
- Wasted effort: Will need to refactor based on actual usage anyway

**Instead:** Start minimal, evolve based on real usage patterns. Phase 1 creates basic structure; future phases add only what's proven necessary.

**Source:** [How to Create a Manageable Folder Structure](https://www.extensis.com/blog/how-to-create-a-manageable-and-logical-folder-structure) - "Start slow. Focus on files you're working with now."

### Anti-Pattern 5: Hidden Dependencies in Templates

**What:** Templates that reference files or folders that don't exist yet

**Why bad:**
- Broken links: User creates entity, links are dead
- Confusion: "Where is this file supposed to be?"
- Fragile: Adding/removing folders breaks templates
- Poor UX: System feels incomplete or buggy

**Instead:**
- Templates should work standalone without assuming folder structure
- Links should be relative and only to required files
- Document any setup steps needed (create these folders first)

### Anti-Pattern 6: Database Thinking in File System

**What:** Trying to enforce rigid schemas or validation in markdown files

**Why bad:**
- Markdown strength is flexibility and human editability
- Validation breaks the "just edit the file" UX
- Users locked into specific structure even when inappropriate
- Defeats purpose of markdown vs database

**Instead:** Provide templates and conventions, not enforcement. Let users adapt structure to their needs while maintaining general patterns.

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Markdown parsing | Custom markdown parser | Native filesystem + text editors | Users edit files directly; no parsing needed in Phase 1 |
| Template system | Complex template engine | Simple file copying | Phase 1 is manual—just copy template files |
| Version control UI | Custom git wrapper | Native git (invisible to user) | Phase 1 doesn't automate git; that's future phases |
| Status tracking | Custom database | Checkbox markdown syntax | `[ ]` `[-]` `[x]` is universal and works everywhere |
| Search functionality | Custom search index | OS native search (Spotlight, Everything) | Markdown files are plaintext—OS search works perfectly |

**Key insight:** Phase 1 is about STRUCTURE, not TOOLING. The power is in the conventions, not the code. Users work directly with files and folders—the system is the structure itself.

## Common Pitfalls

### Pitfall 1: Forgetting the Target User (Non-Technical PMs)

**What goes wrong:** Design structure that makes sense to developers but confuses non-technical users

**Why it happens:** Researcher/developer assumes familiarity with conventions like `.hidden` folders, snake_case, or technical terminology

**How to avoid:**
- Use plain English for all folder names (entities/ not src/, clients/, or data/)
- Avoid technical jargon (ops/ instead of admin/ or config/)
- Make folder purpose obvious without reading docs
- Test naming with non-technical user: "What would you put in this folder?"

**Warning signs:**
- Folder names that need explanation
- Abbreviations or acronyms (e.g., proj/, mgmt/, etc/)
- Developer-centric terminology
- Structure that mirrors code organization rather than business logic

### Pitfall 2: Checkbox Syntax Variations

**What goes wrong:** Using non-standard checkbox syntax that doesn't render properly

**Why it happens:** Different markdown flavors have subtle differences; easy to get spacing wrong

**How to avoid:**
- Use EXACTLY this syntax: `- [ ]` `- [-]` `- [x]`
- Space after dash: `- ` not `-`
- Space inside brackets for unchecked: `[ ]` not `[]`
- No space before x: `[x]` not `[ x]` or `[x ]`
- In-progress uses dash: `[-]` not `[~]` or `[>]`

**Warning signs:**
- Checkboxes not rendering as interactive in GitHub/GitLab
- Inconsistent formatting across documents
- Mix of `*` and `-` for list markers
- Weird spacing that "looks right" but doesn't render

**Testing:** Paste sample into GitHub issue to verify rendering before standardizing

**Source:** [Markdown Task Lists Guide](https://blog.markdowntools.com/posts/markdown-task-lists-and-checkboxes-complete-guide)

### Pitfall 3: Template Proliferation

**What goes wrong:** Creating too many specialized templates, making system complex

**Why it happens:** Desire to have "perfect" template for every scenario; premature optimization

**How to avoid:**
- Start with 2-3 core templates (profile, roadmap, maybe notes)
- Add templates only when pattern is repeated 3+ times
- Favor flexible general templates over rigid specific ones
- Document when to use each template

**Warning signs:**
- templates/ folder has 10+ files
- Templates differ only slightly from each other
- Users ask "which template should I use?"
- Template names require explanation

### Pitfall 4: Folder Name Confusion

**What goes wrong:** Using similar folder names with unclear distinctions (e.g., plans/ vs planning/ vs strategy/)

**Why it happens:** Not thinking through what actually goes in each folder

**How to avoid:**
- Each folder must have distinct, obvious purpose
- Avoid synonyms (planning/ and strategy/ are too similar)
- Document folder purposes in README or comments
- Test with "Where would I put X?" questions

**Warning signs:**
- Two folders could logically hold the same file
- Folder purposes overlap significantly
- Need to consult documentation to decide where files go

### Pitfall 5: Reference Implementation Blind Spots

**What goes wrong:** Copying ppmc structure without understanding whether it's intentional design or organic evolution

**Why it happens:** Assuming everything in reference implementation is deliberate best practice

**How to avoid:**
- Distinguish between proven patterns and work-in-progress
- Verify folder usage (is it actually used or just created?)
- Check if naming is consistent throughout
- Ask: "Would this make sense to someone new to the system?"

**Warning signs from ppmc observation:**
- Some entity folders have different subfolder sets
- Some subfolders appear empty or rarely used
- Inconsistent file naming within entities

**Resolution:** Use ppmc as inspiration, but verify each pattern against research and first principles

## Code Examples

Verified patterns from reference implementation and standards:

### Creating Entity Folder Structure
```bash
# Standard entity creation pattern
mkdir -p "entities/[Entity Name]"
mkdir -p "entities/[Entity Name]/assets"
mkdir -p "entities/[Entity Name]/notes"
mkdir -p "entities/[Entity Name]/planning"
mkdir -p "entities/[Entity Name]/current_state"
mkdir -p "entities/[Entity Name]/execution"
mkdir -p "entities/[Entity Name]/tracking"
```

### Template File with Clear Placeholders
```markdown
# [Entity Name] - Entity Profile

## Company Information
- **Legal Name**: [Full Company Name]
- **Industry**: [Industry/Vertical]
- **Company Size**: [# of employees]
- **Founded**: [Year]
- **Headquarters**: [Location]
- **Website**: [URL]

## Business Goals (Annual)
1. [Goal 1] - Target: [Metric]
2. [Goal 2] - Target: [Metric]
3. [Goal 3] - Target: [Metric]

[... more sections ...]
```

**Template design principles:**
- Use `[Placeholder Text]` format—visually obvious what to replace
- Group related information in sections
- Use markdown tables for structured data
- Include example content in comments if helpful
- Keep templates focused—better multiple simple templates than one complex one

**Source:** Reference implementation templates/CLIENT_PROFILE_TEMPLATE.md

### Master Roadmap with Checkbox Tracking
```markdown
# Master Roadmap

## Progress Tracking Convention
- `[ ]` = Todo (no timestamp needed)
- `[-]` = In Progress (add MM/DD/YY when started)
- `[x]` = Completed (add MM/DD/YY when finished)

## Active Entities
### High Priority
- [-] [Entity Name 1] - Phase description - 2026-01-24
- [ ] [Entity Name 2] - Phase description

### Medium Priority
- [ ] [Entity Name 3] - Phase description

## Recently Completed
- [x] **[Entity Name 4] - Milestone** - Description ✅ 2026-01-20
```

**Checkbox best practices:**
- Always include legend at top explaining symbols
- Date format: YYYY-MM-DD for sorting (master roadmap) OR MM/DD/YY for brevity (entity roadmaps)
- Use checkmark emoji ✅ for visual confirmation of completion
- Group by priority/category for clarity
- Keep "Recently Completed" section to show progress

**Source:** Reference implementation ROADMAP.md

### Minimal Configuration File
```markdown
---
entity_type: client
entity_type_plural: clients
entity_profile_doc: CLIENT_PROFILE
entity_roadmap_doc: PROJECT_ROADMAP
---

# Agent PM Configuration

This system manages **clients** for a marketing agency.

The entity type is configurable—this could be used for projects, products, customers, or any manageable entity.

## Current Configuration

- **Entity Type (Singular):** client
- **Entity Type (Plural):** clients
- **Profile Document:** CLIENT_PROFILE.md
- **Roadmap Document:** PROJECT_ROADMAP.md

## Folder Structure

All clients are stored in the `clients/` folder (automatically named based on entity_type_plural).
```

**Configuration principles:**
- Frontmatter for machine-readable config
- Body for human-readable explanation
- Defaults that work without modification
- Clear documentation of what each setting does

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Database-first | Markdown-first | ~2020 | Rise of tools like Obsidian, Notion, Roam—markdown as database |
| Complex schemas | Flexible structure | Ongoing | Markdown strength is adaptability, not rigid structure |
| Proprietary formats | Open formats | Ongoing | Vendor lock-in avoidance; AI accessibility |
| Manual version control | Git-based | Last 5 years | Git for non-code content is now standard practice |
| Interactive UIs | File-based | Ongoing | "Database as folder" trend—MarkdownDB, plaintext accounting |

**Current trends (2026):**
- **Markdown as database:** Tools like MarkdownDB query markdown files like SQL
- **Local-first:** Data stays in user control, not cloud-locked
- **AI-native formats:** Markdown is ideal LLM training data format
- **Plaintext everything:** JSON, YAML, markdown over binary formats

**Source:** [MarkdownDB](https://markdowndb.com/) and [I'm Using a Markdown Database](https://emnudge.dev/notes/markdown-database/)

**Deprecated/outdated:**
- Complex project management platforms for simple use cases (Jira for solo PM)
- Proprietary note formats (Evernote .enex files)
- Rigid folder taxonomies (trying to build perfect categorization upfront)

## Open Questions

Things that couldn't be fully resolved:

1. **Entity Folder Naming Convention**
   - What we know: Reference implementation uses spaces ("Guardian 6")
   - What's unclear: Is this intentional or just organic naming?
   - Recommendation: Allow spaces in folder names for user-friendliness, but document that CLI operations may require quoting. Target users won't use CLI directly, so optimize for human readability.
   - Confidence: MEDIUM

2. **Required vs Optional Entity Subfolders**
   - What we know: ppmc has standard subfolders (assets/, notes/, planning/, etc.)
   - What's unclear: Should ALL entities have ALL subfolders, or create on-demand?
   - Recommendation: Create core subfolders (assets/, notes/) on entity creation; add others (planning/, execution/) when needed. Avoids empty folder clutter.
   - Confidence: MEDIUM—depends on future automation needs

3. **Master Roadmap Scalability**
   - What we know: Single ROADMAP.md works well for ~10-20 entities
   - What's unclear: At what scale does single-file roadmap become unwieldy?
   - Recommendation: Single file for Phase 1. If >50 entities, consider grouping (active/archived split) or per-category roadmaps. Cross that bridge when we get there.
   - Confidence: LOW—depends on actual usage patterns

4. **Frontmatter Validation**
   - What we know: Minimal frontmatter is best for human readability
   - What's unclear: Should we validate frontmatter schema or trust users?
   - Recommendation: No validation in Phase 1. Structure is self-documenting; validation adds complexity without clear benefit for target users.
   - Confidence: HIGH—validation can come in future automation phases if needed

## Sources

### Primary (HIGH confidence)
- Reference implementation: /Users/seth/Documents/GitHub/ppmc (examined directly)
  - Folder structure: entities/[Name]/ with standard subfolders
  - Templates: CLIENT_PROFILE_TEMPLATE.md, PROJECT_ROADMAP_TEMPLATE.md
  - Master roadmap: ROADMAP.md with checkbox tracking
  - Confirmed patterns: Entity-centric organization, template-based creation, checkbox status tracking

### Secondary (MEDIUM confidence)
- [Markdown Task Lists and Checkboxes Guide](https://blog.markdowntools.com/posts/markdown-task-lists-and-checkboxes-complete-guide) - Checkbox syntax standards
- [Folder Structure Best Practices](https://compresto.app/blog/folder-structure-best-practices) - Semantic naming, avoiding over-nesting
- [Folder Structure and Naming Conventions](https://worldbank.github.io/template/docs/folders-and-naming.html) - Date formats, naming conventions
- [Best Practices for Frontmatter in Markdown](https://www.ssw.com.au/rules/best-practices-for-frontmatter-in-markdown) - When and how to use YAML frontmatter
- [MarkdownDB](https://markdowndb.com/) - Markdown-as-database trend

### Tertiary (LOW confidence)
- Various web search results on markdown project management systems
- General folder organization principles from multiple sources
- Validated against reference implementation where possible

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - Markdown + filesystem is well-established, no dependencies needed
- Architecture: HIGH - Reference implementation provides proven patterns; research confirms best practices
- Pitfalls: MEDIUM - Some pitfalls are from research (checkbox syntax, naming), others inferred from reference implementation observation
- Templates: HIGH - Reference implementation templates are working examples

**Research date:** 2026-01-24
**Valid until:** 90+ days (file organization principles are stable; markdown standards evolve slowly)

**Notes for planner:**
- Phase 1 requires no code—pure file system structure
- All patterns verified against working reference implementation
- Target user is non-technical PM: optimize for clarity over technical elegance
- Structure IS the system in Phase 1; automation comes in later phases
