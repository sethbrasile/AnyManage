# Knowledge Distillation + Lightweight Index Design

> Design document for improving agent retrieval efficiency as the entity database scales, without introducing external dependencies.

## Problem Statement

As AnyManage scales (more entities, more notes, more knowledge accumulated over time), the agent's current retrieval strategy — read full source files on every query — creates compounding costs:

| Query | Current Approach | Lines Loaded (10 entities) | Lines Loaded (50 entities) |
|-------|-----------------|---------------------------|---------------------------|
| "Status of all clients" | Read every ENTITY_ROADMAP.md | ~1,000 | ~5,000 |
| "What do we know about Metro?" | Read profile + roadmap + knowledge | ~320 | ~320 |
| "Which clients care about SEO?" | Read ALL knowledge + profiles | ~3,200 | ~16,000 |
| Weekly review | Read all roadmaps + flag issues | ~1,000 | ~5,000 |

The bottleneck is **context window usage**, not file system speed. Every line loaded into context costs tokens and displaces space the agent needs for reasoning.

### Why Not Vector Search?

We evaluated grepai-style vector embeddings (Ollama + nomic-embed-text). While effective for semantic retrieval, the dependency chain (Ollama install → model download → background server → watcher process) conflicts with AnyManage's core promise: **clone, type `claude`, start working**. Our target users are non-technical PMs who shouldn't manage infrastructure.

### Proposed Alternative

Combine two complementary approaches:
- **Knowledge Distillation (Option C):** The agent produces better-organized output as a side effect of existing workflows, so future queries need less context
- **Lightweight Index (Option A):** Agent-maintained index files that accelerate cross-entity queries

Both are zero-dependency (just markdown files), maintained incrementally during normal workflows, and degrade gracefully if missing.

---

## Part 1: Knowledge Distillation

### 1.1 LEARNED_CONTEXT.md Restructuring

**Current structure:** Append-only dated entries. Grows without bound.

**Proposed structure:** Two-tier with a curated summary and a rolling detail window.

```markdown
# Learned Context: Metro Events Co

## Key Insights

<!-- Agent-maintained summary. Updated when significant new context is learned. -->

- **Decision Style:** David is analytical, data-driven; wants to understand "why" behind recommendations
- **Risk Profile:** Risk-averse on paid ads (burned on Facebook); budget firm at $8K/mo, may revisit Q3
- **Key Opportunity:** No competitor owns "boutique corporate events" positioning in Phoenix
- **Critical Weakness:** Website hurts corporate credibility; basic SEO never set up
- **Relationship Dynamic:** David = strategy/sales; Maria = operations (strong opinions, prefers checklists)
- **Growth Intent:** Considering Scottsdale expansion 2027; daughter may join business

## Recent Context

### 2026-01-22 - Website Planning

- David wants clean, modern but warm design
- Portfolio critical for corporate prospects
- Maria insists on easy backend
- Must work on mobile - David demos from phone
- Contact form needs event type + estimated budget

### 2026-01-20 - Strategy Development

- David wants to personally close corporate deals
- Budget firm at $8K/mo, may increase Q3
- Risk-averse on paid ads
- Trade show in April is first corporate visibility opportunity

---

<!-- Entries older than 90 days are compressed into Key Insights above. -->
<!-- Original details preserved in notes/archive/ and git history. -->
```

**Mechanics:**

- **Key Insights** is a curated ~10-20 line section of the most important things to know about the entity. Written in terse, scannable bullet format.
- **Recent Context** holds the last ~90 days of dated entries (same format as today).
- **Compression trigger:** During weekly review, the agent checks for entries older than 90 days. Important insights are promoted to Key Insights; routine entries are dropped from the file (but remain in git history and archived notes).
- **Maximum Key Insights size:** ~20 bullets. When adding a new insight that would exceed this, the agent replaces or merges the least-relevant existing bullet.

### 1.2 Entity Digest Files

New file: `entities/[Name]/DIGEST.md`

A concise, always-current snapshot that gives the agent what it needs for quick queries without loading full profile + roadmap + knowledge.

```markdown
# Metro Events Co - Digest

<!-- Agent-maintained. Updated after note processing, profile changes, roadmap changes. -->
<!-- Last refreshed: 2026-01-25 -->

## At a Glance

- **Type:** Event Planning | Phoenix, AZ | 8 employees
- **Engagement:** $8K/mo retainer since 2026-01-08
- **Primary Contact:** Maria Santos (operations) | David Park (strategy)
- **Current Phase:** Website Redesign + Corporate Market Entry

## Active Work

- [-] Website redesign (due 2026-02-01) — HARD DEADLINE
- [-] Corporate market entry strategy (ongoing)
- [ ] Corporate landing page (due 2026-03-15)
- [ ] Trade show preparation (2026-04-10)

## Upcoming Deadlines

| Date | Item | Urgency |
|------|------|---------|
| 2026-02-01 | Website redesign launch | HARD — peak season starts |
| 2026-03-15 | Corporate landing page | Before trade show |
| 2026-04-10 | Phoenix Business Expo | First corporate visibility |

## Key Context

- Risk-averse on paid ads; previous agency over-promised SEO results
- No competitor owns "boutique corporate events" in Phoenix
- David checks Google rankings weekly — SEO results matter to him
- Wedding season (May-Oct) = less responsive
- Budget may increase Q3 if results justify

## Recent Activity

- 2026-01-22: Website planning meeting — design direction agreed
- 2026-01-20: Strategy development completed (STRATEGY_PLAN)
- 2026-01-15: Assessment completed (AUDIT_FINDINGS)
- 2026-01-12: Competitive landscape analysis
- 2026-01-08: Entity onboarded

## Topics

SEO, website, corporate-events, competitive-landscape, lead-generation, trade-shows
```

**Design principles:**

- **~40-60 lines per entity.** Enough to answer most questions, small enough to load many at once.
- **Active Work** mirrors the current-phase tasks from ENTITY_ROADMAP.md — just the active items, not history.
- **Key Context** is drawn from LEARNED_CONTEXT.md Key Insights — the distilled version.
- **Topics** is a flat tag list for cross-entity search (see Part 2).
- **Recent Activity** is a 5-7 item log of significant events, not every tiny change.

**When is it updated?**

| Trigger | What Changes in DIGEST.md |
|---------|--------------------------|
| Note processing | Active Work, Recent Activity, Topics, Key Context (if new insight) |
| Profile update | At a Glance (if entity info changed), Key Context |
| Roadmap change | Active Work, Upcoming Deadlines |
| Specialist deliverable | Recent Activity, Topics |
| Weekly review | Full refresh from source files |

---

## Part 2: Lightweight Index

### 2.1 Cross-Entity Index

New file: `.index/CROSS_ENTITY_INDEX.md`

A single file the agent reads for any query that spans multiple entities.

```markdown
# Cross-Entity Index

<!-- Agent-maintained. Updated during note processing and weekly reviews. -->
<!-- Last refreshed: 2026-01-25 -->

## Entity Summary

| Entity | Phase | Next Deadline | Status | Active Tasks |
|--------|-------|---------------|--------|-------------|
| Metro Events Co | Website Redesign | 2026-02-01 | ⚠ Deadline near | 4 |
| Bright Path Consulting | Ongoing Retainer | 2026-02-15 | On track | 2 |
| Sunrise Digital | Onboarding | 2026-02-28 | New | 1 |

## Upcoming Deadlines (Next 30 Days)

| Date | Entity | Item | Urgency |
|------|--------|------|---------|
| 2026-02-01 | Metro Events Co | Website redesign launch | HARD |
| 2026-02-15 | Bright Path Consulting | Quarterly review | Normal |
| 2026-02-28 | Sunrise Digital | Onboarding complete | Normal |

## Recent Activity (Last 7 Days)

- 2026-01-24: Processed notes for Bright Path Consulting (3 tasks added)
- 2026-01-22: Metro Events Co — website planning meeting
- 2026-01-20: Metro Events Co — strategy plan completed

## Entities Needing Attention

- **Metro Events Co:** Website deadline in 7 days, 2 tasks not yet started
- **Sunrise Digital:** No notes processed since onboarding
```

**This replaces full-scan for:**
- "Status of all clients" → read this one file (~40 lines)
- "What's due this week?" → scan Upcoming Deadlines section
- "Which clients need attention?" → read Entities Needing Attention
- Weekly review initial scan → start here, deep-dive into flagged entities only

### 2.2 Topic Index

New file: `.index/TOPICS.md`

Enables cross-entity semantic queries without embeddings.

```markdown
# Topic Index

<!-- Agent-maintained. Topics extracted during note processing and specialist work. -->
<!-- Last refreshed: 2026-01-25 -->

## SEO
- **Metro Events Co:** Never properly set up; losing rankings to newer competitors; David checks weekly
- **Bright Path Consulting:** Strong organic presence; maintaining current rankings

## Website
- **Metro Events Co:** Critical weakness; redesign due 2026-02-01; must be mobile-friendly
- **Bright Path Consulting:** Completed 2025-11; monthly maintenance

## Pricing / Budget
- **Metro Events Co:** $8K/mo, firm; may increase Q3; risk-averse on paid ads
- **Bright Path Consulting:** $5K/mo, annual contract
- **Sunrise Digital:** $3K/mo, 6-month trial

## Competitive Landscape
- **Metro Events Co:** Desert Bloom (weddings), Phoenix Premier (corporate); gap in boutique corporate

## Lead Generation
- **Metro Events Co:** No lead capture currently; corporate landing page planned for March

## Content Strategy
- **Metro Events Co:** Thin content; competitors have 10x more indexed pages
- **Bright Path Consulting:** Blog producing 2 posts/week; performing well
```

**How topics are assigned:**

Topics are simple keyword categories, not embeddings. The agent assigns them based on content during note processing:

1. During note extraction, each task/fact/insight gets one or more topic tags
2. Tags are drawn from a soft vocabulary (not a rigid list) — the agent uses judgment
3. Common topics emerge naturally: SEO, website, pricing, competitive, content, social-media, lead-gen, etc.
4. When a topic has entries from 3+ entities, it's worth having in the index
5. Single-entity topics can live in that entity's DIGEST.md Topics field only

**This enables queries like:**
- "Which clients care about SEO?" → Read TOPICS.md SEO section (~5 lines instead of scanning all knowledge files)
- "Who has website work coming up?" → Read TOPICS.md Website section
- "Any clients with budget concerns?" → Read TOPICS.md Pricing section

---

## Part 3: Integration with Existing Workflows

### 3.1 Note Processing (Enhanced)

Current flow:
1. Identify note source → 2. Extract tasks/facts/calendar/follow-ups → 3. Apply to roadmap/profile → 4. Archive note → 5. Report

**Added steps (after step 3, before step 4):**

3a. **Update DIGEST.md** — Refresh Active Work and Recent Activity sections
3b. **Tag extracted items** — Assign topic tags to each extracted item
3c. **Update TOPICS.md** — Add/update entries for assigned topics
3d. **Update Key Insights** — If a genuinely new, important insight was learned, promote to LEARNED_CONTEXT.md Key Insights
3e. **Update CROSS_ENTITY_INDEX.md** — Refresh this entity's row in Entity Summary

**Cost of added steps:** Minimal. The agent is already in context with the entity's data. Writing a few lines to index files is negligible compared to the extraction work already happening.

### 3.2 Profile Building (Enhanced)

Current flow:
1. Parse fact → 2. Map to section → 3. Update profile → 4. Confirm

**Added step:**

4a. **Update DIGEST.md** — If the change affects At a Glance (entity info) or Key Context (significant fact), refresh those sections

### 3.3 Weekly Review (Enhanced)

Current flow:
1. Scan all roadmaps → 2. Extract completed/active/overdue → 3. Generate report

**New flow:**
1. **Read CROSS_ENTITY_INDEX.md** — Get overview without loading all roadmaps
2. **Flag entities needing attention** — Overdue deadlines, no recent activity, stale
3. **Deep-dive flagged entities only** — Read full roadmap/profile only for entities that need it
4. **Generate report** — Same format as today
5. **Refresh all indexes:**
   - Regenerate CROSS_ENTITY_INDEX.md from current state
   - Compress old LEARNED_CONTEXT.md entries (>90 days) across all entities
   - Refresh DIGEST.md for any entity that changed
   - Rebuild TOPICS.md from all current digests

Step 5 acts as the periodic "full rebuild" that keeps indexes fresh even if incremental updates missed something.

### 3.4 Specialist Work (Enhanced)

Current flow:
1. Load knowledge layers → 2. Produce deliverable → 3. Save to deliverables/ → 4. Capture learned context

**Added step:**

4a. **Update DIGEST.md** — Add to Recent Activity, update Topics
4b. **Update Key Insights** — Specialists often produce the most significant insights

### 3.5 Status Queries (Optimized)

**"Show me Acme's status"**
- Current: Read ENTITY_ROADMAP.md (~100 lines)
- New: Read DIGEST.md (~50 lines). If user asks follow-up questions, load full roadmap.

**"Status of all clients"**
- Current: Read all ENTITY_ROADMAP.md files (~100 lines each)
- New: Read CROSS_ENTITY_INDEX.md (~40 lines total)

**"Which clients mentioned pricing?"**
- Current: Grep all knowledge/profile files
- New: Read TOPICS.md Pricing section (~10 lines)

---

## Part 4: Rebuilding and Consistency

### 4.1 Full Rebuild Command

New user command: "Rebuild index" or "Refresh index"

**What it does:**
1. For each entity in `entities/`:
   a. Read ENTITY_PROFILE.md, ENTITY_ROADMAP.md, knowledge/LEARNED_CONTEXT.md
   b. Generate fresh DIGEST.md
   c. If LEARNED_CONTEXT.md has entries older than 90 days, run compression
2. Generate fresh CROSS_ENTITY_INDEX.md from all digests
3. Generate fresh TOPICS.md from all digests + knowledge files
4. Report: "Index rebuilt for N entities"

**When to trigger automatically:**
- Weekly review (always includes a full refresh)
- If DIGEST.md is missing for any entity during a query (rebuild just that entity)
- If CROSS_ENTITY_INDEX.md is missing (rebuild from digests, or from source files if no digests)

### 4.2 Consistency Guarantees

**Source files are always authoritative.** If there's ever a conflict between DIGEST.md and ENTITY_PROFILE.md, the profile wins. Digests and indexes are **derived views** that can be regenerated at any time.

**Stale index is better than no index.** If CROSS_ENTITY_INDEX.md is a week old, it still gives the agent a useful starting point. The weekly review refresh prevents unbounded staleness.

**Missing index falls back to current behavior.** If `.index/` doesn't exist or DIGEST.md is missing, the agent reads source files directly. This is exactly how the system works today — no regression.

### 4.3 Git Behavior

Index files follow the same invisible-git pattern as everything else:
- Committed automatically after updates
- Commit message: `chore(index): refresh entity indexes` or similar
- `.index/` and `DIGEST.md` files are tracked in git (useful for session resume)

---

## Part 5: Scaling Analysis

### Context Window Savings

| Scenario | Entities | Current (lines loaded) | With Distillation + Index | Reduction |
|----------|----------|----------------------|--------------------------|-----------|
| Status all | 10 | ~1,000 | ~40 | 25x |
| Status all | 50 | ~5,000 | ~80 | 62x |
| Cross-entity query | 10 | ~3,200 | ~20 | 160x |
| Cross-entity query | 50 | ~16,000 | ~30 | 533x |
| Weekly review (initial scan) | 10 | ~1,000 | ~40 | 25x |
| Weekly review (initial scan) | 50 | ~5,000 | ~80 | 62x |
| Single entity deep-dive | any | ~320 | ~50 (digest first) | 6x |

### Where It Doesn't Help

- **Entity onboarding:** Creating a new entity still writes the same files. (But it also creates a DIGEST.md — minimal overhead.)
- **Single-entity note processing:** The agent still reads the full note content. (But downstream updates to indexes are incremental.)
- **Specialist work:** Specialists still need full knowledge layers. (But DIGEST.md gives a fast summary to start with.)

These are acceptable — the expensive queries (cross-entity, weekly review, status-all) get the biggest improvements.

---

## Part 6: Implementation Plan

### Phase 1: DIGEST.md + LEARNED_CONTEXT.md Restructuring

1. Create `templates/DIGEST_TEMPLATE.md`
2. Create `templates/LEARNED_CONTEXT_TEMPLATE.md` (restructured with Key Insights)
3. Update note-processing protocol to generate/update DIGEST.md after processing
4. Update profile-building protocol to refresh DIGEST.md on significant changes
5. Migrate example entities (Metro Events, Bright Path, Sunrise Digital) to new format
6. Update entity-onboarding protocol to create initial DIGEST.md

### Phase 2: Cross-Entity Index

1. Create `.index/` directory
2. Create `templates/CROSS_ENTITY_INDEX_TEMPLATE.md`
3. Update weekly-review skill to read/write CROSS_ENTITY_INDEX.md
4. Update get-status skill to prefer CROSS_ENTITY_INDEX.md for multi-entity queries
5. Add "rebuild index" command to command-patterns protocol

### Phase 3: Topic Index

1. Create `templates/TOPICS_TEMPLATE.md`
2. Add topic tagging step to note-processing protocol
3. Update specialist coordination to tag topics from deliverables
4. Add topic-based query patterns to command-patterns protocol

### Phase 4: Compression and Maintenance

1. Add LEARNED_CONTEXT.md compression logic to weekly-review skill
2. Define Key Insights promotion criteria
3. Add "rebuild index" command implementation
4. Test graceful degradation (delete indexes, verify fallback behavior)

---

## Resolved Questions

1. **Should DIGEST.md be user-visible?** → **Yes**, visible with clear "auto-maintained" header. Users may find it useful as a quick reference card. The header warns that manual edits will be overwritten.

2. **How aggressive should LEARNED_CONTEXT compression be?** → **Relevance-based, not age-based.** The 90-day window is a soft heuristic, not a hard cutoff. During weekly review, the agent judges whether each entry is still actionable. Slow-moving engagements may retain 6-month-old entries if they're still relevant. The goal is keeping Key Insights current and concise, not mechanical deletion.

3. **Should topics be pre-defined or emergent?** → **Emergent.** Topics vary wildly by user and domain (a marketing agency's topics look nothing like a healthcare practice's). The agent creates topics naturally during note processing. Within a given user's domain, consistent naming converges through repetition — the agent will naturally reuse "seo" rather than inventing "search-optimization" each time. No fixed vocabulary needed.

4. **What about entities with very different scales?** → **Allow natural variation.** The template provides structure; the agent fills only what's relevant. A freshly-onboarded entity may have a 20-line digest; a mature entity with years of context may reach 50-60 lines. No hard cap — the template structure itself constrains bloat.

5. **Integration with specialist knowledge layers?** → **Yes**, specialists read DIGEST.md first for fast orientation, then load full knowledge layers for the actual work. Adds one read step but saves significant context when the specialist needs to understand the entity quickly before deep-diving.

---

*Design: knowledge-distillation + lightweight-index*
*Status: Approved — ready for implementation*
*Created: 2026-02-08*
*Questions resolved: 2026-02-09*
