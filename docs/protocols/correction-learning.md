# Correction Learning Protocol

Complete documentation for learning from user corrections to improve voice accuracy.

## Overview

The correction learning protocol defines how the system learns from user edits to voice-generated content. Users can provide corrections in-session (conversational) or asynchronously (via CORRECTIONS.md file), and the system extracts patterns to improve the voice profile over time.

**Key Principles:**
- Two correction paths: in-session and async file-based
- Reminder frequency: first 3 generations, then every 5th
- Pattern extraction focuses on what changed, not what stayed
- Profile updates are additive (new patterns, anti-patterns, examples)
- Corrections improve voice accuracy without requiring retraining

## Quick Reference

| Aspect | Pattern |
|--------|---------|
| **In-Session Path** | User provides edited version after generation |
| **Async Path** | User logs corrections to `CORRECTIONS.md` file |
| **Reminder Frequency** | Generations 1-3, then every 5th (5, 10, 15, ...) |
| **Pattern Extraction** | Compare original vs edited, identify systematic changes |
| **Confidence Threshold** | Need 2+ examples to add new pattern |
| **Profile Updates** | Add anti-patterns, refine patterns, add examples |
| **Update Command** | `/process-corrections` or "Process my voice corrections" |

## Two Correction Paths

Users can provide corrections in two ways depending on their workflow.

### Path 1: In-Session (Conversational)

**When to use:** User sees generated content and immediately notices issues

**Workflow:**
1. Agent generates content with voice applied
2. Agent offers correction (if counter matches frequency)
3. User shares edited version
4. Agent extracts patterns immediately
5. Agent updates profile
6. Agent confirms learning

**Example:**
```
Agent: [Generates email with voice]

Does this match your voice? If not, share your edited version and I'll learn from it.

User: Here's how I'd actually write it:
[Pastes edited version]

Agent: Got it. I noticed:
- You simplified "We wanted to reach out" → "Wanted to share"
- You removed "hope you're doing well"
- You kept shorter paragraphs

Updated voice profile with these patterns.
```

**Advantages:**
- Immediate learning in context
- Conversational and low-friction
- User gets instant feedback

**Disadvantages:**
- Requires active session
- Can interrupt workflow if user is busy

### Path 2: Async (File-Based)

**When to use:** User wants to log corrections without interrupting session

**Workflow:**
1. User generates content (with or without agent)
2. User edits content offline
3. User logs correction to CORRECTIONS.md
4. Later, user requests: "Process my voice corrections"
5. Agent reads CORRECTIONS.md
6. Agent extracts patterns from all logged corrections
7. Agent updates profile
8. Agent confirms learning
9. Agent archives processed corrections

**Example CORRECTIONS.md entry:**
```markdown
## 2026-01-25 - Email to client

**Original:**
Subject: Checking In

Hi Sarah,

I hope this email finds you well. I wanted to reach out to see if you had any questions about the Q1 results we shared last week.

Please don't hesitate to let me know if you need any clarification.

Best regards,
Seth

**How I'd Actually Write It:**
Subject: Q1 Results - Questions?

Hi Sarah,

Any questions on the Q1 results from last week? Happy to walk through anything.

Best,
Seth

**What to learn:**
- Subject lines: More direct, less formal
- Openings: Skip "hope this finds you well"
- Body: Skip "wanted to reach out", just get to the point
- Closings: "Best" not "Best regards"
```

**Advantages:**
- No session interruption
- User can batch corrections
- User controls when learning happens
- More thoughtful edits possible

**Disadvantages:**
- Requires manual logging
- Delayed feedback loop

## Reminder Frequency

The agent offers correction learning at strategic intervals.

### Frequency Rules

**Generations 1-3: Always offer**
- 1st generation → Offer correction
- 2nd generation → Offer correction
- 3rd generation → Offer correction

Rationale: Early voice application needs most improvement. User should review first few outputs carefully.

**Generation 4+: Every 5th**
- 4th generation → Skip
- 5th generation → Offer correction
- 6th-9th generation → Skip
- 10th generation → Offer correction
- 11th-14th generation → Skip
- 15th generation → Offer correction
- Continue pattern...

Rationale: Voice improves over time. Don't be annoying, but maintain feedback loop.

### Counter Tracking

**Where to track:** In-memory session variable (not persisted to disk)

```
generation_count = 0

On each voice-applied generation:
  generation_count += 1

  if generation_count <= 3:
    offer_correction = true
  elif generation_count % 5 == 0:
    offer_correction = true
  else:
    offer_correction = false
```

**Counter reset:** Each new session starts at 0 (fresh counter)

Rationale: Session boundaries are natural reset points. User might be in different context.

### Manual Correction Override

User can ALWAYS provide correction manually, regardless of frequency:

```
User: "That email doesn't sound like me. Here's how I'd write it..."

Agent: [Process correction immediately]
```

Reminder frequency only controls when agent offers, not when user can provide.

## Pattern Extraction

The core learning process: analyzing what changed and why.

### Step 1: Alignment

Compare original (voice-generated) vs edited (user's actual version)

**Sentence-level alignment:**
1. Break both versions into sentences
2. Find corresponding sentences
3. Identify added, removed, and modified sentences

**Word-level alignment (for modified sentences):**
1. Compare word-by-word
2. Identify substitutions, additions, deletions
3. Look for systematic patterns

### Step 2: Change Categorization

Classify changes into categories:

| Category | What to Look For | Example |
|----------|------------------|---------|
| **Word choice** | Specific word substitutions | "utilize" → "use" |
| **Phrase patterns** | Multi-word phrase changes | "I wanted to reach out" → "Wanted to share" |
| **Sentence structure** | Length, complexity changes | Long sentence split into two |
| **Punctuation** | Comma usage, em-dashes, etc. | Added em-dash for emphasis |
| **Formality** | Formal ↔ casual shifts | "Best regards" → "Best" |
| **Openings** | Email/message opening changes | Removed "Hope you're well" |
| **Closings** | Email/message closing changes | Added personal sign-off |
| **Tone markers** | Warmth, directness, humor | Added "Happy to help!" |

### Step 3: Pattern Confidence Assessment

**Single example (confidence: low)**
- Don't add to profile yet
- Note as potential pattern
- Wait for confirmation in another correction

**2+ examples (confidence: medium)**
- Add to profile as pattern
- Mark as "emerging pattern" (needs more data)

**3+ examples (confidence: high)**
- Add to profile as established pattern
- Use in quality verification checklist

**Anti-pattern detection:**
If user REMOVES something AI generated 2+ times → That's an anti-pattern

Example:
```
Correction 1: Removed "I hope this email finds you well"
Correction 2: Removed "Hope you're doing well"
→ Anti-pattern: Avoid health-checking openings
```

### Step 4: Context Sensitivity

Consider when the pattern applies:

**Content type specific:**
- Email pattern that doesn't apply to Slack
- Technical writing pattern that doesn't apply to casual chat

**Modifier specific:**
- Formal context change vs casual context change
- Professional_client pattern vs family_client pattern

**Universal:**
- Core voice DNA changes (apply across all contexts)
- Signature traits refinement

### Step 5: Extraction Quality Check

Before adding pattern to profile, verify:

1. **Specificity:** Is the pattern specific enough to be useful?
   - Good: "Replace 'utilize' with 'use'"
   - Bad: "Be more casual"

2. **Actionability:** Can AI apply this pattern?
   - Good: "Remove 'I hope this email finds you well' opening"
   - Bad: "Sound more like yourself"

3. **Consistency:** Does this contradict existing patterns?
   - If yes → User clarification needed
   - If no → Proceed with addition

4. **Scope:** Which section of voice profile should this update?
   - Core Voice DNA (universal patterns)
   - Content type specific (email, technical, chat, reports)
   - Anti-patterns (what NOT to do)

## Profile Update Process

How to modify the voice profile based on extracted patterns.

### Update Types

**1. Add Anti-Pattern**

When user consistently REMOVES something:

```markdown
## Anti-Patterns (What AI Gets Wrong)

**Health-checking openings:**
- ❌ "I hope this email finds you well"
- ❌ "Hope you're doing well"
- ❌ "I trust this email finds you in good health"
→ Just start with the content. No health checks.

[Add new anti-pattern]

**Verbose transitions:**
- ❌ "I wanted to reach out to..."
- ❌ "I just wanted to follow up on..."
→ Get to the point: "Following up on..." or just state the topic.
```

**2. Refine Existing Pattern**

When user correction clarifies or expands existing pattern:

```markdown
## Email Patterns

**Subject Lines:**
DO:
- Direct and specific: "Q1 Results - Questions?"
- Action-oriented: "Review: Updated Timeline"
[Add new example based on correction]
- Status updates: "Project X - Delayed 2 Weeks"

DON'T:
- Generic: "Checking In"
- Vague: "Quick Question"
[Add new counter-example from correction]
- Passive: "Regarding Our Previous Conversation"
```

**3. Add New Pattern**

When correction reveals new systematic preference (2+ examples):

```markdown
## Chat Patterns

**Openings:**
DO:
- Direct: "Hey team - update on Project X"
- Contextual: "Quick heads up about the delay"
[Add new pattern from corrections]
- Personal: "Morning! Wanted to share..." (for close team only)

DON'T:
- Formal: "Good morning everyone, I wanted to inform you..."
```

**4. Add Example**

When correction provides good concrete example:

```markdown
## Technical Documentation Patterns

**Code Examples:**
DO:
- Inline comments for non-obvious logic
- Full example before explanation
[Add new example from correction]
- Error cases shown explicitly

Example from user:
"""
// Check authentication before processing
if (!user.isAuthenticated()) {
  throw new UnauthorizedException("Login required");
}
"""
```

### Update Location Strategy

Map correction type to profile section:

| Correction Type | Profile Section |
|----------------|-----------------|
| Word choice (universal) | Core Voice DNA |
| Sentence structure | Core Voice DNA |
| Punctuation style | Core Voice DNA |
| Email opening/closing | Email Patterns (DO/DON'T) |
| Technical writing style | Technical Patterns (DO/DON'T) |
| Chat tone/structure | Chat Patterns (DO/DON'T) |
| Report formality | Reports Patterns (DO/DON'T) |
| Formality calibration | Relationship Calibration |
| AI-isms removed | Anti-Patterns |

### Update Workflow

**In-Session Correction:**
1. Extract patterns immediately
2. Update voice profile in storage
3. Confirm to user what was learned
4. Continue session with updated profile

**Async Correction (CORRECTIONS.md):**
1. Read all entries in CORRECTIONS.md
2. Extract patterns from each entry
3. Consolidate patterns (combine similar ones)
4. Update voice profile in storage
5. Confirm to user: summary of what was learned
6. Archive processed corrections

### Archival Process

After processing CORRECTIONS.md:

**Step 1: Add header to processed entries**
```markdown
<!-- PROCESSED: 2026-01-25 -->
## 2026-01-25 - Email to client
[Original content...]
<!-- /PROCESSED -->
```

**Step 2: Summarize learning**
Add summary at end of CORRECTIONS.md:
```markdown
---

## Processing History

**2026-01-25:** Processed 3 corrections
- Added anti-pattern: Health-checking openings
- Refined email subject line patterns
- Added example: Direct status updates
```

**User can clear processed entries manually when file gets large**

## Error Handling

Handle common failure scenarios gracefully.

### Unclear Correction

**Scenario:** User provides correction but change is ambiguous

**Example:**
```
User: "Make it sound more like me"
```

**Agent response:**
```
Could you share your edited version? I learn best from concrete examples rather than general guidance.

You can paste the edited content or log it to CORRECTIONS.md.
```

**Don't proceed with unclear corrections.** Specific examples are required for pattern extraction.

### Conflicting Corrections

**Scenario:** New correction contradicts existing pattern or previous correction

**Example:**
```
Previous correction: Removed "I hope you're well"
New correction: Added "I hope you're well"
```

**Agent response:**
```
I'm seeing conflicting corrections:
- Jan 23: You removed "I hope you're well" opening
- Jan 25: You added "I hope you're well" opening

Context matters! Which is accurate?
1. Remove health-check openings (stick with Jan 23)
2. Add health-check for formal clients only (context-specific)
3. Add health-check for all emails now (preference changed)
```

**Wait for user clarification before updating profile.**

### Insufficient Examples

**Scenario:** Pattern observed once (not enough for confidence)

**Agent response:**
```
Noted: You prefer "Best" over "Best regards" for closings.

I'll watch for this pattern. If I see it again, I'll add it to your voice profile.
```

**Track as potential pattern, don't add to profile yet.**

### Write Failure

**Scenario:** Can't save updated profile to storage

**Agent behavior:**
1. Attempt primary location write (`~/.anymanage/voice/voice_profile.md`)
2. If fails, attempt fallback location (`ops/voice/voice_profile.md`)
3. If both fail:
   ```
   I learned from your correction but couldn't save the updated profile (permission issue?).

   What I learned:
   - [Pattern 1]
   - [Pattern 2]

   I'll apply this in the current session. Want to troubleshoot the storage issue?
   ```
4. Apply learning to in-memory profile for session
5. Log error to `ops/logs/voice-correction.json`

### Empty CORRECTIONS.md

**Scenario:** User requests processing but file is empty or doesn't exist

**Agent response:**
```
No corrections logged yet. CORRECTIONS.md is empty.

You can log corrections there anytime, or provide them directly in conversation.
```

**Don't create empty file.** Wait for user to add corrections.

## Integration

How correction learning integrates with other protocols.

### With Voice Application Protocol

Voice application offers corrections → Correction learning processes them

**Sequence:**
1. Voice application generates content (`docs/protocols/voice-application.md`)
2. Voice application offers correction (based on frequency)
3. User provides edited version
4. Correction learning protocol (this protocol) extracts patterns
5. Profile updated
6. Next voice application uses improved profile

### With Voice Training Protocol

Voice training creates initial profile → Correction learning refines it

**Sequence:**
1. User completes voice training (`docs/protocols/voice-training.md`)
2. Initial profile created from samples
3. User generates content with voice applied
4. Correction learning improves profile over time
5. Profile becomes more accurate without retraining

### With Specialist Coordination

Specialists generate voice-applied content → Users can correct specialist outputs

**Sequence:**
1. Specialist generates deliverable with voice applied
2. User reviews deliverable, notices voice issues
3. User logs corrections to CORRECTIONS.md
4. Later, user processes corrections: "/process-corrections"
5. Voice profile improved for future specialist outputs

See: `docs/protocols/specialist-coordination.md` (Voice Integration section)

## Commands

Explicit commands for correction learning.

| Command | Action |
|---------|--------|
| `/process-corrections` | Process entries in CORRECTIONS.md |
| "Process my voice corrections" | Process entries in CORRECTIONS.md |
| "Learn from this correction" | Process in-session correction immediately |
| "Update my voice profile" | Process in-session correction immediately |

Natural language variations all work:
- "Process corrections I logged"
- "Update voice based on my edits"
- "Learn from my changes"

## Examples

Full correction learning examples showing realistic workflows.

### Example 1: In-Session Correction

**User:**
```
Write email to client about project delay
```

**Agent:**
```
[Loads voice profile, generates email with professional_client modifier]

Subject: Project Timeline Update

Dear Sarah,

I hope this email finds you well. I wanted to reach out to inform you that we're experiencing a delay with the Acme implementation project.

The revised timeline is as follows:
- Security review completion: February 13 (previously January 30)
- User acceptance testing: February 20 (previously February 6)
- Production deployment: February 27 (previously February 13)

Please don't hesitate to reach out if you have any questions or concerns. We're committed to delivering a quality solution.

Best regards,
Seth

---

Does this match your voice? If not, share your edited version and I'll learn from it.
```

**User:**
```
Here's how I'd actually write it:

Subject: Acme Project - Delayed 2 Weeks

Hi Sarah,

Heads up that the Acme implementation is pushing back two weeks. Their security team needs more time for the compliance review.

Updated timeline:
- Security review: Jan 30 → Feb 13
- UAT: Feb 6 → Feb 20
- Go-live: Feb 13 → Feb 27

Let me know if you have questions. Happy to walk through the details on our Thursday call.

Best,
Seth
```

**Agent:**
```
Got it. I noticed several patterns:

**Subject line:**
- Changed: "Project Timeline Update" → "Acme Project - Delayed 2 Weeks"
- Pattern: More direct and specific, includes entity name and impact

**Opening:**
- Removed: "I hope this email finds you well"
- Removed: "I wanted to reach out to inform you that"
- Changed: "Dear Sarah" → "Hi Sarah"
- Pattern: Skip formalities, get to the point with casual greeting

**Body:**
- Changed: "we're experiencing a delay" → "pushing back two weeks"
- Added: Reason for delay ("Their security team needs...")
- Changed: List format from full sentences to arrow notation (→)
- Pattern: More direct language, explain why, use concise formatting

**Closing:**
- Removed: "Please don't hesitate to reach out"
- Removed: "We're committed to delivering a quality solution"
- Added: "Happy to walk through details on our Thursday call"
- Changed: "Best regards" → "Best"
- Pattern: Remove boilerplate, add specific next step, shorter sign-off

Updated voice profile:
- Added anti-pattern: Health-checking openings
- Added anti-pattern: "I wanted to reach out to..." phrasing
- Added email pattern: Direct subject lines with entity + impact
- Refined email closings: "Best" for professional_client modifier
- Added example: Status update email (this one)

Your voice profile is now more accurate for client emails.
```

### Example 2: Async Processing

**User creates CORRECTIONS.md:**

```markdown
# Voice Corrections

Log your edits here. Agent will process periodically to improve voice profile.

## 2026-01-25 - Slack message to team

**Original:**
Hey team - I wanted to give you all an update on the progress we've made with the Beta Industries project. We've completed the initial assessment phase and we're now moving forward into the strategy development phase. I'll be scheduling a meeting soon to discuss our findings.

**How I'd Write It:**
Update on Beta: finished assessment, starting strategy next. Will schedule sync to review findings.

**What to learn:**
- Slack: Way more concise
- Drop "I wanted to", "we've", "we're" - just state facts
- No need for full sentences in Slack

---

## 2026-01-25 - Technical doc intro

**Original:**
This document provides a comprehensive overview of the authentication system architecture. It is intended to help developers understand how the system works and how to integrate with it.

**How I'd Write It:**
# Authentication System

This doc covers how auth works and how to integrate.

**What to learn:**
- Tech docs: Skip "This document provides..."
- Skip "comprehensive", "intended to help"
- Short, direct intro
- Header is enough context, don't repeat it

---
```

**User (later):**
```
Process my voice corrections
```

**Agent:**
```
Processing corrections from CORRECTIONS.md...

Found 2 corrections to process:
1. Slack message (Jan 25)
2. Technical doc intro (Jan 25)

Patterns extracted:

**From Slack correction:**
- Extreme brevity for Slack (cut ~80% of words)
- Remove: "I wanted to", "we've", "we're"
- Fragment sentences OK in Slack context
- Pattern: Statement over explanation

**From Technical doc correction:**
- Skip meta-introductions ("This document provides...")
- Remove qualifiers: "comprehensive", "intended to help"
- Trust header to provide context
- Short, direct introduction

Updated voice profile:
- Added Chat anti-patterns: "I wanted to", verb contractions in status updates
- Refined Chat patterns: Fragment sentences for brevity
- Added Technical anti-pattern: Meta-introductions
- Added Technical anti-pattern: Qualifier overuse
- Added example: Slack status update (extreme brevity)
- Added example: Technical doc intro (direct style)

Marked corrections as processed in CORRECTIONS.md.

Your voice profile is now more accurate for Slack and technical documentation.
```

**Agent updates CORRECTIONS.md:**

```markdown
# Voice Corrections

Log your edits here. Agent will process periodically to improve voice profile.

<!-- PROCESSED: 2026-01-25 -->
## 2026-01-25 - Slack message to team
[content...]

## 2026-01-25 - Technical doc intro
[content...]
<!-- /PROCESSED -->

---

## Processing History

**2026-01-25:** Processed 2 corrections
- Added Chat anti-patterns: "I wanted to", verb contractions
- Refined Chat patterns: Fragment sentences for Slack brevity
- Added Technical anti-patterns: Meta-introductions, qualifier overuse
- Added examples: Slack status update, technical doc intro
```

### Example 3: Conflicting Correction Resolution

**User:**
```
Write email to our long-term client John at Acme Corp about the project
```

**Agent:**
```
[Generates email with professional_client modifier]

Subject: Project Update

Hi John,

Wanted to share a quick update on the project. We're on track for the Feb 27 launch.

Let me know if you have questions.

Best,
Seth

---

Does this match your voice? If not, share your edited version and I'll learn from it.
```

**User:**
```
Actually, for John I'd write:

Hi John,

Hope you're doing well! Wanted to share a quick update on the project. We're on track for the Feb 27 launch.

Let me know if you have any questions at all.

Best,
Seth
```

**Agent:**
```
Interesting - I'm seeing a conflict with previous learning:

**Previous correction (Jan 23):**
You removed "Hope you're doing well" from client emails

**This correction (Jan 25):**
You added "Hope you're doing well!" for John at Acme Corp

Context might matter here! Which is accurate?

1. Remove health-checks for all clients (stick with Jan 23 pattern)
2. Add health-checks for long-term/warm clients only (context-specific)
3. Add health-checks for all clients now (my preference changed)
```

**User:**
```
Option 2 - for long-term clients where we have a warm relationship, I do use the health check. For new or formal clients, I skip it.
```

**Agent:**
```
Perfect - that's a relationship calibration pattern.

Updated voice profile:

**Relationship Calibration:**

Family_client modifier (warm, long-term relationships):
- DO include warmth markers: "Hope you're doing well!"
- DO use "any questions at all" (emphasizes availability)
- Relationship cues: Long-term client, mentioned by first name only

Professional_client modifier (standard client communication):
- DON'T include health-checks
- DO use "Let me know if you have questions" (available but not overly warm)
- Relationship cues: Default for client emails

This gives you context-aware voice application based on relationship warmth.
```

## Related Protocols

- **Voice Application:** See `docs/protocols/voice-application.md`
- **Voice Training:** See `docs/protocols/voice-training.md`
- **Specialist Coordination:** See `docs/protocols/specialist-coordination.md` (Voice Integration section)

---

*Protocol: correction-learning*
*Version: 1.0*
*Last updated: 2026-01-25*
