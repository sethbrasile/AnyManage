# Phase 06 Plan 02: Voice Extraction Protocol Summary

Voice training protocol and portable extraction prompt complete - full workflow from capability assessment through profile storage.

**One-liner:** Complete voice training protocol with capability assessment, content type selection (1-4 types), sample collection (5-10 per type), pattern extraction, profile review, dual-location storage, and portable handoff prompt for terminal-only environments.

**Phase:** 6 - Voice Training System
**Plan:** 02
**Status:** Complete
**Date:** 2026-01-25

---

## What Was Built

### Voice Training Protocol

**File:** `docs/protocols/voice-training.md` (1151 lines)

Complete workflow documentation covering:

**Trigger Detection:**
- Natural language: "Train my voice", "Voice training", "Learn my writing style"
- Slash command: `/train-voice`

**Capability Assessment:**
- Environment detection (full capability vs. terminal-only)
- MCP file upload availability check
- Routing logic (direct extraction vs. handoff prompt)

**Content Type Selection:**
- 4 content types: Emails, Technical Documentation, Chat Messages, Reports/Proposals
- User selects 1-4 types to train
- Flexible input parsing (numbers, names, natural language)
- Validation (minimum 1, maximum 4)

**Sample Collection:**
- 5-10 samples per content type
- Sample requirements and quality guidance
- Privacy handling (patterns not content)
- Validation (quantity, quality, user confirmation)

**Voice Extraction:**
- Pattern analysis across samples
- Cross-content type comparison
- Profile generation using VOICE_PROFILE_TEMPLATE.md structure
- Extraction quality standards

**Profile Review:**
- Display full profile before saving
- User approval workflow
- Iterative refinement support
- Change request handling

**Profile Storage:**
- Primary location: `~/.agent-pm/voice/voice_profile.md`
- Fallback location: `ops/voice/voice_profile.md`
- Storage priority logic with fallback
- CORRECTIONS.md creation alongside profile

**Handoff Protocol:**
- Terminal-only environment detection
- Portable prompt generation
- User instructions for external completion
- Profile validation on return

**Error Handling:**
- No samples available
- Insufficient samples (less than 5)
- Poor quality samples (AI-generated, templated)
- Storage write failures
- Incomplete extraction
- User abandonment

**Examples:**
- Full capability environment (end-to-end)
- Terminal-only handoff workflow
- Insufficient samples handling
- Profile review with changes requested

### Portable Extraction Prompt

**File:** `templates/VOICE_EXTRACTION_PROMPT.md` (383 lines)

Self-contained extraction prompt for terminal-only environments:

**Components:**
- Complete voice profile template structure
- Extraction guidelines (specific patterns, not generic)
- Sample placeholders for all 4 content types
- Quality standards for extraction
- Instructions for completing exercise
- Guidance for returning completed profile
- Tips for best results

**Key Features:**
- User can copy entire prompt to Claude Desktop or ChatGPT web
- All instructions included (no external dependencies)
- Output format matches VOICE_PROFILE_TEMPLATE.md
- Clear steps for completing and returning to Agent PM
- Examples of good vs. bad extraction

---

## Tasks Completed

| Task | Name | Commit | Files |
|------|------|--------|-------|
| 1 | Create voice training protocol | 49ede8a | docs/protocols/voice-training.md |
| 2 | Create portable extraction prompt template | d8aae2d | templates/VOICE_EXTRACTION_PROMPT.md |

---

## Key Files

**Created:**
- `docs/protocols/voice-training.md` - Complete voice training protocol (1151 lines)
- `templates/VOICE_EXTRACTION_PROMPT.md` - Portable extraction prompt (383 lines)

**Modified:**
- None

---

## Decisions Made

| Decision | Rationale | Impact |
|----------|-----------|--------|
| Capability assessment before training | Terminal-only environments can't easily upload files; need different workflow | Graceful degradation with handoff prompt |
| Content type selection (1-4 types) | Users should focus training on what they actually use; not all users need all types | Targeted training improves quality |
| Sample requirements: 5-10 per type | Balance between quality (need variety) and burden (10 is realistic) | Achievable for users, sufficient for extraction |
| Profile review before save | Bad profile worse than no profile; user must approve | Prevents poor quality profiles |
| Primary/fallback storage with priority | Home directory portable across projects, ops/ as fallback if primary fails | Portability by default, reliability with fallback |
| Handoff prompt for terminal environments | Can't require file upload capability; some users only have SSH | Universal accessibility |
| Iterative profile refinement | First extraction rarely perfect; support multiple rounds | Higher quality final profiles |
| CORRECTIONS.md created with profile | Learning infrastructure ready from start | Continuous improvement path |

---

## Technical Implementation

### Protocol Structure

Follows established protocol format:
1. **Trigger Detection** - Natural language and slash commands
2. **Capability Assessment** - Environment-specific routing
3. **Core Workflow** - Content selection → Sample collection → Extraction → Review → Storage
4. **Error Handling** - Common failure scenarios
5. **Examples** - Full interactions showing realistic use
6. **Integration** - Related protocols and workflows

### Capability Assessment Logic

```
Check MCP file upload:
  If available → Full capability environment
    → Proceed with direct extraction

  If not available → Terminal-only environment
    → Generate handoff prompt
    → User completes in capable environment
    → Returns profile for saving
```

### Content Type Selection

```
Present 4 options:
1. Emails
2. Technical Documentation
3. Chat Messages
4. Reports/Proposals

Parse user input:
- "1, 2" → [emails, technical]
- "Emails and chat" → [emails, chat]
- Natural language → Extract types

Validate:
- Minimum 1 type
- Maximum 4 types
- Confirm with user
```

### Sample Collection Workflow

```
For each selected content type:
  1. Present collection guidance
  2. User provides 5-10 samples
  3. Validate quantity (min 5)
  4. Validate quality (not AI/templated)
  5. User confirms sufficiency
  6. Proceed to next type

All types collected → Extract patterns
```

### Voice Extraction

```
Pattern analysis:
1. Analyze each content type separately
   - Sentence structure
   - Punctuation
   - Signature traits
   - Tone markers

2. Cross-content analysis
   - What stays consistent? → Core Voice DNA
   - What changes by context? → Relationship calibration
   - What does AI get wrong? → Anti-patterns

3. Generate profile using VOICE_PROFILE_TEMPLATE.md
   - Fill all sections with specific patterns
   - Include DO/DON'T examples
   - Create quality checklist

4. Present for review
```

### Storage Priority

```
Try primary:
  ~/.agent-pm/voice/voice_profile.md

If fails:
  ops/voice/voice_profile.md

Confirm location to user
Create CORRECTIONS.md alongside
```

### Handoff Protocol

```
Terminal environment detected:
1. Load templates/VOICE_EXTRACTION_PROMPT.md
2. Customize for user's selected types
3. Present complete prompt
4. User copies to capable environment
5. User completes extraction
6. User returns with profile
7. Agent validates structure
8. Agent saves to storage
```

---

## Dependencies

**Requires:**
- Plan 01 (Voice Training Infrastructure) - Storage directories, templates, INSTRUCTIONS.md integration

**Provides:**
- Complete voice training workflow
- Capability-aware training process
- Portable extraction for limited environments
- Profile storage with fallback

**Affects:**
- Plan 03 (Voice Application Protocol) - Will implement voice profile loading and content generation
- All content generation workflows - Voice training now available system-wide

---

## Key Links Verified

| From | To | Via | Pattern |
|------|----|----|---------|
| `docs/protocols/voice-training.md` | `templates/VOICE_PROFILE_TEMPLATE.md` | Profile structure reference | `VOICE_PROFILE_TEMPLATE` (2 refs) |
| `docs/protocols/voice-training.md` | `templates/VOICE_EXTRACTION_PROMPT.md` | Handoff prompt generation | `VOICE_EXTRACTION_PROMPT` (4 refs) |

All required links present and verified.

---

## What's Next

**Plan 03 - Voice Application Protocol:**
- Implement voice profile loading (primary → fallback)
- Content type detection from user requests
- Auto-activation when generating content
- Correction learning from user edits
- CORRECTIONS.md processing and profile updates
- Voice override handling ("more formal", "more casual")

---

## Validation

**Must-have truths verified:**
- ✓ User can initiate voice training with "Train my voice" or `/train-voice`
- ✓ Agent performs capability assessment before starting extraction
- ✓ Terminal-only environments generate handoff prompt (VOICE_EXTRACTION_PROMPT.md)
- ✓ User selects content types (1-4) before providing samples
- ✓ Voice profile displayed for review before saving

**Artifact checks:**
- ✓ `docs/protocols/voice-training.md` exists (1151 lines, min 200 required)
- ✓ `templates/VOICE_EXTRACTION_PROMPT.md` exists (383 lines, min 100 required)
- ✓ Protocol includes capability assessment section
- ✓ Protocol includes content type selection (4 types)
- ✓ Protocol includes sample collection (5-10 per type)
- ✓ Protocol includes profile review workflow
- ✓ Protocol includes storage with primary/fallback locations

**Key link checks:**
- ✓ Protocol references VOICE_PROFILE_TEMPLATE.md (2 occurrences)
- ✓ Protocol references VOICE_EXTRACTION_PROMPT.md (4 occurrences)
- ✓ Links use correct pattern matching

**Success criteria:**
1. ✓ User can invoke voice training with "Train my voice" or "/train-voice"
2. ✓ Agent performs capability assessment and routes appropriately
3. ✓ Terminal-only environments generate complete handoff prompt
4. ✓ Content type selection presents 4 options, accepts 1-4 selections
5. ✓ Sample requirements are 5-10 per content type
6. ✓ Profile is shown for review before saving
7. ✓ Storage uses home directory as primary, project as fallback

---

## Deviations from Plan

None - plan executed exactly as written.

---

## Metrics

- **Duration:** ~4 minutes
- **Tasks completed:** 2/2
- **Files created:** 2
- **Files modified:** 0
- **Lines added:** 1534 (1151 protocol + 383 prompt)
- **Commits:** 2 (one per task)

---

## Tags

`voice-training` `protocol` `extraction` `capability-assessment` `content-types` `handoff` `terminal` `portable`

---

## Context for Future Work

**Voice training system architecture:**
1. **Plan 01 (complete):** Infrastructure - storage, templates, documentation
2. **Plan 02 (this plan):** Extraction - training workflow, pattern extraction, profile creation
3. **Plan 03 (next):** Application - use profiles to generate content, learn from corrections

**Key design principles established:**

**Capability-Aware Workflow:**
- Full capability environments: Direct extraction
- Limited capability environments: Handoff prompt
- Graceful degradation ensures universal accessibility

**Content Type Focused:**
- User selects 1-4 types (not forced to do all)
- Training focused on user's actual needs
- Each type contributes patterns to unified profile

**Quality Gating:**
- Sample validation (5-10 per type, quality checks)
- Profile review before save
- Iterative refinement support
- User must approve final profile

**Storage Reliability:**
- Primary location for portability
- Fallback location for reliability
- Clear storage location confirmation
- CORRECTIONS.md created from start

**Handoff Protocol for Limited Environments:**
- Self-contained extraction prompt
- No external dependencies
- Complete instructions included
- Profile validation on return

**Reference Implementation Validation:**

The protocol structure and examples draw from production patterns:
- Proven capability assessment approach
- Realistic sample collection workflow
- Quality-focused extraction standards
- User-friendly review and approval process

This ensures the protocol will work well in practice for non-technical users.

---

## Next Session Notes

**When implementing Plan 03 (Voice Application):**

1. **Profile Loading:**
   - Check `~/.agent-pm/voice/voice_profile.md` first
   - Fall back to `ops/voice/voice_profile.md`
   - Parse profile structure (no YAML, pure markdown)

2. **Content Type Detection:**
   - "Write an email" → Use email patterns
   - "Draft a doc" → Use technical patterns
   - "Slack message" → Use chat patterns
   - "Create proposal" → Use reports patterns

3. **Correction Learning:**
   - In-session: User provides edited version
   - Async: User logs to CORRECTIONS.md
   - Agent processes corrections on request
   - Update profile with learned patterns

4. **Voice Override:**
   - "More formal than usual" → Adjust tone up
   - "More casual" → Adjust tone down
   - User can request context-specific variation

5. **Integration Points:**
   - Entity-specific content (emails to clients)
   - Specialist deliverables (strategy docs in user voice)
   - Note processing outputs (voice-aware summaries)
