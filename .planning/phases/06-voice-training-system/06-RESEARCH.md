# Phase 6: Voice Training System - Research

**Researched:** 2026-01-25
**Domain:** LLM voice profiling and tone extraction
**Confidence:** HIGH

## Summary

Voice training systems extract writing style patterns from user-provided samples and encode them as reusable profiles that guide LLM output generation. The reference implementation in `.planning/my_voice_example/` provides a complete, proven pattern: markdown-based voice profiles with Core Voice DNA, content-type-specific patterns, anti-patterns, and quality checklists. This phase researches the extraction workflow, storage conventions, correction learning, and capability-aware training flows.

**Key findings:**
- Markdown is the optimal format for voice profiles (human-readable, AI-parseable, portable across LLM systems)
- Voice extraction requires 5-10 writing samples per content type for pattern recognition
- LLMs excel at extracting voice patterns when given structured extraction prompts with clear sections to populate
- Voice correction learning can be conversational (in-session) or async (append-only log file)
- Capability assessment is critical: terminal-only environments should generate handoff prompts for more capable harnesses (Claude Desktop, ChatGPT web)

**Primary recommendation:** Follow the reference implementation pattern exactly. Build the training protocol as an agent-guided workflow that performs capability assessment first, then either runs extraction directly (if MCP available) or generates a portable extraction prompt for the user to take to a more capable environment.

## Standard Stack

### Core Technologies

| Technology | Version | Purpose | Why Standard |
|-----------|---------|---------|--------------|
| Markdown | Standard | Voice profile format | Human-readable, AI-parseable, portable across LLM systems, no dependencies |
| MCP (Model Context Protocol) | Current | Optional file/email access | Enables automated sample collection from Gmail/files when available |
| Claude/GPT-4+ | Latest | Voice extraction LLM | Pattern recognition and style analysis capabilities |

### Voice Profile Format

**Location:** `~/.agent-pm/voice/` (primary) or `ops/voice/` (fallback)

**Structure:** Pure markdown following reference implementation:
- Core Voice DNA (sentence structure, punctuation, signature traits)
- Content-type specific patterns (emails, docs, chat, technical)
- Anti-patterns (what never to use)
- Quality checklist (verification before sending)

**No alternatives needed:** Markdown is universally accepted. JSON/YAML would add parsing overhead without benefits.

## Architecture Patterns

### Recommended Voice System Structure

```
~/.agent-pm/voice/           # User home (portable across projects)
  voice_profile.md           # Main voice profile

project/ops/voice/           # Project fallback (if home not found)
  voice_profile.md           # Project-specific voice
  CORRECTIONS.md             # Append-only correction log (optional)

templates/
  VOICE_TRAINING_EXERCISE.md # Training protocol template
```

### Pattern 1: Capability Assessment Flow

**What:** Check agent capabilities before starting voice training, route to appropriate extraction method.

**When to use:** At start of every voice training workflow.

**Example:**
```markdown
## Capability Assessment Protocol

When user requests voice training:

1. Check capabilities:
   - Do I have Gmail MCP? (can fetch emails directly)
   - Do I have file upload? (can accept drag-dropped documents)
   - Am I terminal-only? (text paste only)

2. Route based on capabilities:

   **If Gmail MCP or file upload available:**
   - Proceed with direct extraction in this environment
   - Guide user to provide samples via available method

   **If terminal-only (e.g., Claude Code without MCP):**
   - Generate portable extraction prompt
   - Instruct user to take prompt to Claude Desktop/ChatGPT web
   - User completes extraction there, brings profile back
   - Agent saves profile to appropriate location

3. Explain to user why routing matters:
   - Terminal copy-paste requires technical skills
   - Non-technical users need GUI-based sample collection
   - Setup guidance available if user wants MCP configuration
```

**Source:** Phase 6 CONTEXT.md locked decision on capability assessment.

### Pattern 2: Content-Type Selection

**What:** Let user choose which content types to train on (emails, technical docs, chat, reports).

**When to use:** During sample collection phase of training.

**Example:**
```markdown
## Content Type Selection

Present options:
1. Emails (client communication, business correspondence)
2. Technical Documentation (READMEs, guides, API docs)
3. Chat Messages (Slack, Teams, informal communication)
4. Reports/Proposals (formal documents, presentations)

User picks 1-3 types they care about most.

Request 5-10 samples per selected type:
- Enough variety for pattern detection
- Not overwhelming to collect
- Actual content user wrote (not AI-generated)
```

**Source:** Phase 6 CONTEXT.md decision on content types.

### Pattern 3: Voice Profile Storage with Fallback

**What:** Check home directory first, fall back to project directory if not found.

**When to use:** When loading voice profile for application to output.

**Example:**
```markdown
## Voice Profile Loading Order

1. Check: ~/.agent-pm/voice/voice_profile.md
   - If found: Load and use (personal voice follows user)

2. If not found, check: ops/voice/voice_profile.md
   - If found: Load and use (project-specific voice)

3. If neither found:
   - Proceed without voice application
   - Or suggest running voice training
```

**Source:** Phase 6 CONTEXT.md decision on storage locations.

### Pattern 4: Single Profile with Modifiers

**What:** One voice profile with tone calibration modifiers, not multiple named profiles.

**When to use:** When applying voice to different contexts (client emails vs internal docs).

**Example:**
```markdown
## Tone Calibration Modifiers

Base voice profile contains user's natural patterns.

Apply modifiers for context:
- Professional client: Slightly more structured, clear timelines
- Business partner/team: More direct, assume shared knowledge
- Family member client: Professional boundaries, slightly warmer
- Formal: Add structure, maintain casual warmth
- Casual: Full informal mode

Modifiers adjust application, not the core profile.
```

**Source:** Phase 6 CONTEXT.md decision referencing user's existing llm_implementation_guide.md pattern.

### Pattern 5: Correction Learning (Two Paths)

**What:** Learn from user corrections to improve voice profile accuracy.

**When to use:** After user edits AI-generated content before sending.

**Example:**
```markdown
## Correction Learning Paths

**Path 1: In-Session (Conversational)**
- User: "Here's what I changed: [paste edited version]"
- Agent: Analyze diff between generated vs corrected
- Agent: Update voice profile in real-time
- Agent: Confirm: "Updated voice profile to reflect [patterns changed]"

**Path 2: Async (Append-Only Log)**
- User pastes corrections to ops/voice/CORRECTIONS.md
- Format: AI output | User edit | What changed
- Agent processes log periodically or on request
- Agent extracts patterns, updates voice profile

**Reminder UX:**
After generating voice-applied content:
"Did you make edits before sending? Paste your edits so I can learn!"
- Show reminder but not every time (would be annoying)
- Make it clear WHERE to paste (conversation or file)
```

**Source:** Phase 6 CONTEXT.md decisions on correction learning paths.

### Anti-Patterns to Avoid

- **YAML frontmatter in voice profiles** - Adds complexity for non-technical users, markdown-only is cleaner
- **Multiple named profiles** - Confusing to manage, single profile with modifiers is simpler
- **Auto-apply voice to everything** - Claude's discretion on when to apply, user can always request
- **Force terminal paste for non-technical users** - Recognize limitations, route to capable harness
- **Skip correction learning** - Critical feedback loop, always offer learning from edits

## Don't Hand-Roll

Problems that have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Voice extraction algorithm | Custom pattern recognition | LLM-assisted extraction with structured prompts | Modern LLMs excel at style analysis when given clear section templates |
| Voice profile format | Custom schema/JSON | Markdown following reference implementation | Already proven, human-readable, portable |
| Sample collection UI | Custom file upload interface | MCP servers (Gmail, Drive) or handoff to capable environment | MCP infrastructure exists, handoff is simpler than building UI |
| Correction diff algorithm | Custom text diff logic | LLM-powered pattern extraction from before/after pairs | LLMs identify stylistic patterns better than rule-based diff |
| Content-type detection | Custom classifier | User selection during training | User knows their content types, no need to automate |

**Key insight:** Voice extraction is a pattern recognition problem perfectly suited for LLMs. Don't build custom NLP algorithms when you can use structured prompts to guide an LLM through analysis.

## Common Pitfalls

### Pitfall 1: Assuming All Environments Can Handle File Upload

**What goes wrong:** Training protocol requires file uploads but agent is running in terminal-only Claude Code without MCP.

**Why it happens:** Developers assume GUI capabilities in terminal environment.

**How to avoid:**
- Always perform capability assessment first
- Check for MCP availability before designing workflow
- Generate portable extraction prompts as fallback
- Guide user to capable environment when needed

**Warning signs:** User says "I can't upload files" or "I'm using Claude Code in terminal"

**Source:** Phase 6 CONTEXT.md critical decision on capability assessment and handoff.

### Pitfall 2: Over-Applying Voice to Inappropriate Content

**What goes wrong:** Voice profile applied to technical documentation, code comments, or internal notes where formal/neutral tone is better.

**Why it happens:** Automatic application without context awareness.

**How to avoid:**
- Content-type based auto-activation (Claude's discretion)
- Likely auto-apply: client emails, proposals, external communications
- Likely manual: internal notes, technical docs, code comments
- User can always explicitly request or suppress voice

**Warning signs:** User complains "This doc sounds too casual" or edits remove personality

**Source:** Phase 6 CONTEXT.md decision on voice activation strategy.

### Pitfall 3: Not Learning from Corrections

**What goes wrong:** User consistently edits AI output but voice profile never improves.

**Why it happens:** No mechanism to capture and learn from user corrections.

**How to avoid:**
- Always offer correction learning after voice-applied generation
- Provide two paths: conversational (in-session) or file-based (async)
- Make it clear WHERE and HOW to provide corrections
- Process corrections to extract patterns, update profile

**Warning signs:** User making same edits repeatedly, frustration with consistency

**Source:** Phase 6 CONTEXT.md critical decision on correction learning UX.

### Pitfall 4: Complex Profile Management for Non-Technical Users

**What goes wrong:** Voice profiles use YAML, JSON, or complex configuration requiring technical knowledge to edit.

**Why it happens:** Developer-first design instead of user-first.

**How to avoid:**
- Pure markdown format (no frontmatter)
- Human-readable structure matching reference implementation
- Clear section headers and examples
- User can edit profile file directly if needed

**Warning signs:** User asks "How do I edit my voice profile?" or doesn't understand format

**Source:** Phase 6 CONTEXT.md decision on file format, reference implementation analysis.

### Pitfall 5: Insufficient Training Samples

**What goes wrong:** Voice extraction based on 1-2 samples produces generic or inaccurate profile.

**Why it happens:** User wants quick result, skips adequate sample collection.

**How to avoid:**
- Request 5-10 samples per content type minimum
- Explain why: need variety for pattern detection
- Samples should be actual user-written content (not AI-generated)
- Guide user to find diverse examples (different recipients, different contexts)

**Warning signs:** Voice profile feels generic, user says "That doesn't sound like me"

**Source:** Phase 6 CONTEXT.md decision on sample requirements, LLM voice analysis best practices research.

## Code Examples

### Voice Extraction Prompt Structure

```markdown
# Voice Extraction Instructions

You are analyzing writing samples to extract a voice profile. Follow this structure exactly.

## Input
[User will provide 5-10 writing samples per content type]

## Output Format

Create a markdown voice profile with these sections:

### Core Voice DNA
- Sentence Structure: [Analyze patterns]
- Punctuation Style: [Identify habits]
- Signature Traits: [Extract unique markers]
- Hedging Language: [Document strategic softeners]
- Intensifiers: [Note casual amplifiers]
- Humor Style: [Describe approach]

### [Content Type] Patterns

For each content type user selected:

**DO:**
- [Specific patterns observed]
- [Formatting preferences]

**DON'T:**
- [Anti-patterns to avoid]
- [Phrases never used]

### Quality Checklist

Verification items before using this voice:
- [ ] [Specific checks based on patterns]

## Analysis Approach

1. Look for recurring patterns across samples
2. Note what makes this voice distinctive
3. Identify what this voice NEVER does (anti-patterns critical)
4. Extract specific phrases and structures
5. Document with concrete examples from samples
```

**Source:** Reference implementation llm_implementation_guide.md + LLM voice analysis research.

### Voice Application in Specialist Context

```markdown
# Applying Voice to Specialist Output

When specialist generates deliverable:

1. Check if voice should apply:
   - Is this client-facing? (emails, proposals, external docs)
   - Or internal? (notes, internal strategy, technical specs)

2. Load voice profile if applicable:
   - Read ~/.agent-pm/voice/voice_profile.md
   - Or ops/voice/voice_profile.md (fallback)

3. Apply appropriate modifier:
   - Professional client: More structured, clear timelines
   - Business partner: Direct, assume shared knowledge
   - Technical: May skip voice application entirely

4. Generate content following voice patterns:
   - Core Voice DNA traits
   - Content-type specific patterns
   - Avoid anti-patterns
   - Run quality checklist

5. Offer correction learning:
   - "Did you make edits? Paste so I can learn!"
   - Update voice profile based on corrections
```

**Source:** Integration of voice system with Phase 5 specialist pattern.

### Correction Processing Logic

```markdown
# Processing Voice Corrections

When user provides before/after edits:

## Input
- AI-generated version: [original output]
- User-corrected version: [what user actually sent]

## Analysis Steps

1. Identify what changed:
   - Removed phrases/patterns
   - Added phrases/patterns
   - Structural changes
   - Tone adjustments

2. Extract patterns:
   - "User always removes [X] and replaces with [Y]"
   - "User adds [specific phrase] in [context]"
   - "User shortens [type of sentence]"

3. Update voice profile:
   - Add to anti-patterns if user removed
   - Add to patterns if user consistently adds
   - Update examples with user's corrections

4. Confirm update:
   - "Updated voice profile to reflect: [list changes]"
   - No need to show full profile unless requested
```

**Source:** LLM self-correction and iterative improvement research, Phase 6 CONTEXT.md correction learning decisions.

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Custom style guides (PDF docs) | Markdown voice profiles with examples | 2024-2025 | LLMs process markdown better than prose style guides |
| Single monolithic prompt | Modular prompts per use case | 2025 | Reference implementation shows modular > monolithic |
| Manual voice application | LLM-assisted with correction learning | 2025-2026 | Iterative improvement from user feedback |
| Complex JSON schemas | Pure markdown, human-readable | 2025 | Non-technical users can edit directly |
| Multiple named profiles | Single profile with tone modifiers | 2026 | Simpler management, matches user's existing pattern |

**Deprecated/outdated:**
- YAML frontmatter for voice profiles - Adds unnecessary complexity for non-technical users
- "One size fits all" voice application - Context-aware application is now standard
- Custom NLP algorithms for style extraction - LLMs with structured prompts are superior

**Emerging:**
- MCP-based sample collection from Gmail/files - Reduces manual copy-paste
- Real-time correction learning during conversation - Faster profile improvement
- Voice profile portability across LLM systems - Same profile works in Claude, ChatGPT, etc.

## Open Questions

### Question 1: Frequency of Correction Reminders

**What we know:**
- Showing reminder after every voice-applied generation would be annoying
- Never showing reminder means users won't learn feature exists
- Phase 6 CONTEXT.md specifies this is Claude's discretion

**What's unclear:**
- Exact heuristic for when to show reminder
- How to track if user has already learned about correction feature

**Recommendation:**
- Show reminder first 3 times voice is applied (user learning phase)
- Then show every 5th time (occasional reinforcement)
- Always show if user explicitly requests voice application
- Track reminder count in ops/voice/.reminder_state (simple counter file)

### Question 2: Multi-User Voice Profiles in Shared Projects

**What we know:**
- Primary storage is ~/.agent-pm/voice/ (user home)
- Fallback is ops/voice/ (project level)
- Single user scenario is well-defined

**What's unclear:**
- How to handle team projects where multiple people contribute
- Should each team member have their own voice for their outputs?
- How to select which voice to use in multi-user context?

**Recommendation:**
- Phase 6 focuses on single-user scenario (per CONTEXT.md)
- Document limitation: "Voice training assumes single primary user per project"
- Multi-user voice selection is out of scope for this phase
- Could be addressed in future phase if needed

### Question 3: Voice Evolution Over Time

**What we know:**
- User's writing style may evolve
- Correction learning provides incremental updates
- No mechanism for wholesale profile refresh

**What's unclear:**
- When to suggest user re-run full voice training
- How to detect when accumulated corrections have degraded profile coherence

**Recommendation:**
- Simple approach: Date-stamp voice profiles (header: "Last trained: DATE")
- Suggest re-training if profile is >6 months old
- Provide "refresh voice training" command that keeps corrections but gathers new samples
- This is low priority - handle in execution if time permits

## Sources

### Primary (HIGH confidence)

- `.planning/my_voice_example/seth_voice_profile.md` - Reference implementation for voice profile structure
- `.planning/my_voice_example/llm_implementation_guide.md` - Modular prompt pattern and tone calibration
- `.planning/my_voice_example/quick_start_guide.md` - User-facing training workflow pattern
- `.planning/my_voice_example/before_after_examples.md` - Transformation examples showing voice application
- `.planning/phases/06-voice-training-system/CONTEXT.md` - Locked decisions and Claude's discretion areas
- `.planning/phases/05-specialist-pattern/05-CONTEXT.md` - Specialist system that voice will integrate with

### Secondary (MEDIUM confidence)

- [Scale: Using LLMs While Preserving Your Voice](https://scale.com/blog/using-llms-while-preserving-your-voice) - LLMs interpret tone as statistically recognizable patterns
- [Single Grain: How LLMs Interpret Brand Tone and Voice](https://www.singlegrain.com/branding-2/how-llms-interpret-brand-tone-and-voice/) - Compact explicit rules plus concrete examples
- [GitHub: Google Workspace MCP](https://github.com/aaronsb/google-workspace-mcp) - Gmail MCP with attachment support
- [Your Voice Profile](https://yourvoiceprofile.com/) - Portable markdown voice profiles
- [Markdown: The Best Text Format for AI](https://blog.bismart.com/en/markdown-ai-training) - Why markdown works for AI
- [Relevance AI: Few-shot prompting to mimic style](https://relevanceai.com/docs/example-use-cases/few-shot-prompting) - Example-based style learning

### Tertiary (LOW confidence)

- [Medium: AI Workshop on Voice](https://bendaviesromano.medium.com/ai-workshop-reflecting-on-how-you-write-how-ai-writes-and-tone-in-the-age-of-generated-text-e42207c066ea) - Strategic inefficiencies define voice
- [State of LLMs 2025](https://magazine.sebastianraschka.com/p/state-of-llms-2025) - Inference-time scaling trends
- [TACL: Automatically Correcting LLMs](https://direct.mit.edu/tacl/article/doi/10.1162/tacl_a_00660/120911/) - Self-correction frameworks
- [Medium: Training LLMs to Self-Correction via RL](https://medium.com/@devmallyakarar/training-language-models-to-self-correction-via-reinforcement-learning-a-deep-dive-into-score-with-ff85421b4186) - SCoRe iterative improvement

## Metadata

**Confidence breakdown:**
- Voice profile format: HIGH - Reference implementation proven, markdown is standard
- Extraction workflow: HIGH - Phase 6 CONTEXT.md provides complete locked decisions
- Correction learning: MEDIUM - Patterns known, specific reminder frequency is Claude's discretion
- MCP capabilities: MEDIUM - MCP servers exist and documented, but availability varies by user setup

**Research date:** 2026-01-25
**Valid until:** 60 days (stable domain - voice profiling patterns won't change rapidly)

---

**Ready for planning.** All research areas covered, locked decisions documented, Claude's discretion areas identified.
