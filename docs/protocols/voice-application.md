# Voice Application Protocol

Complete documentation for applying the user's voice to generated content.

## Overview

The voice application protocol defines when and how to apply the user's trained voice profile to generated content. Voice is applied automatically for appropriate content types while respecting user override preferences and tone calibration modifiers.

**Key Principles:**
- Voice auto-applies to content types included in training
- User can override or suppress voice application
- Tone calibration modifiers adjust formality for context
- Correction learning improves voice accuracy over time
- Profile loading follows storage priority (home → project)

## Quick Reference

| Aspect | Pattern |
|--------|---------|
| **Profile Location** | `~/.agent-pm/voice/voice_profile.md` (primary), `ops/voice/voice_profile.md` (fallback) |
| **Auto-Activation** | Content type in profile → Voice applies automatically |
| **Suppression** | "without my voice", "in a neutral tone", "standard format" |
| **Tone Modifiers** | `professional_client`, `business_partner`, `formal`, `casual`, `family_client` |
| **Correction Path** | In-session (offer after generation) or async (`CORRECTIONS.md`) |
| **Reminder Frequency** | First 3 generations, then every 5th |

## Profile Loading

Before generating any content, the agent checks for a voice profile.

### Loading Priority

**Step 1: Check primary location**
```
~/.agent-pm/voice/voice_profile.md
```

If file exists and is valid → Load this profile

**Step 2: Check fallback location**
```
ops/voice/voice_profile.md
```

If primary doesn't exist, check fallback. If exists and valid → Load this profile

**Step 3: No profile found**

If neither location has a valid profile:
- Voice system is NOT active
- Generate content without voice application
- DO NOT mention voice to user unless explicitly requested
- User can initiate voice training if desired

### Profile Validation

A valid voice profile must contain:
- `# Voice Profile` header
- At least one content type section (Email, Technical, Chat, Reports)
- Core Voice DNA section
- At least 3 patterns or anti-patterns

**Invalid profile handling:**
```
Profile exists but is corrupted or incomplete:
- Log warning to ops/logs/voice-application.json
- DO NOT use corrupted profile
- Fall back to neutral generation
- DO NOT expose error to user (graceful degradation)
```

### Profile Age Check

Check file modification date:
- If profile older than 6 months → Consider suggesting refresh
- If profile older than 1 year → Suggest refresh after generation
- Aging check is informational only (still use old profile)

**Age reminder format:**
```
[After generating content with voice]

Your voice profile was last updated 8 months ago. Want to refresh it with recent writing samples?
```

## Auto-Activation Rules

Voice applies automatically based on content type detection.

### Content Type Detection

| User Request | Detected Type | Auto-Apply Voice? |
|--------------|---------------|-------------------|
| "Write an email to [person]" | Email | Yes (if Email in profile) |
| "Draft message to [person]" | Email | Yes (if Email in profile) |
| "Create technical doc about [topic]" | Technical Documentation | Yes (if Technical in profile) |
| "Document [process]" | Technical Documentation | Yes (if Technical in profile) |
| "Write Slack message to [person]" | Chat | Yes (if Chat in profile) |
| "Send quick message about [topic]" | Chat | Yes (if Chat in profile) |
| "Create proposal for [client]" | Reports/Proposals | Yes (if Reports in profile) |
| "Draft report on [topic]" | Reports/Proposals | Yes (if Reports in profile) |

### Manual-Only Content Types

Voice does NOT auto-apply for:
- Code snippets (user's writing voice not relevant)
- System-generated logs or reports
- Templates (should be fill-in-the-blank neutral)
- Entity profiles (factual, not conversational)
- Internal checklists or SOPs

User can explicitly request voice for these: "Write [content] in my voice"

### Suppression Detection

Voice does NOT apply when user explicitly suppresses:

| Suppression Pattern | Example |
|---------------------|---------|
| "without my voice" | "Write email to client without my voice" |
| "in a neutral tone" | "Create proposal in a neutral tone" |
| "standard format" | "Draft message in standard format" |
| "formal only" | "Write email formal only" (suppresses personal voice) |
| "generic" | "Create generic response template" |

When suppression detected:
- Generate content without voice application
- DO NOT apply any voice patterns
- Use neutral, professional tone
- DO NOT offer correction learning after

## Tone Calibration Modifiers

Users can adjust tone for specific contexts using modifiers.

### Five Standard Modifiers

**1. professional_client (Default for client-facing)**

Use when: Writing to clients, formal business communications, proposals

Calibration:
- Increase formality 1-2 levels from base voice
- Maintain core voice DNA (sentence structure, signature traits stay)
- Remove casual markers (contractions stay if in base voice, but no slang)
- Professional openings and closings

**2. business_partner**

Use when: Writing to peers, vendors, partners, colleagues at other companies

Calibration:
- Keep base voice formality
- Slightly more collaborative tone
- Professional but not distant
- Equal-relationship framing

**3. formal**

Use when: Legal, compliance, board communications, high-stakes situations

Calibration:
- Maximum formality
- Remove all casual markers (including contractions)
- Expand signature traits to full formal equivalents
- Conservative word choice

**4. casual**

Use when: Team Slack, internal quick messages, friendly check-ins

Calibration:
- Decrease formality 1-2 levels from base voice
- Embrace casual markers from base voice
- Shorter sentences OK
- Contractions, conversational flow

**5. family_client**

Use when: Long-term trusted clients, close business relationships

Calibration:
- Warmth from base voice emphasized
- Professional but familiar
- Personal touches OK (from base voice patterns)
- Slightly more casual than professional_client

### Modifier Detection

| User Request | Detected Modifier |
|--------------|-------------------|
| "Write email to client [name]" | professional_client (default) |
| "Write to our partner [company]" | business_partner |
| "Draft formal response to [topic]" | formal |
| "Slack message to team about [topic]" | casual |
| "Email to [long-term client]" | professional_client (can be overridden) |
| "Write more formally than usual" | formal |
| "Keep it casual" | casual |

### Explicit Modifier Override

Users can explicitly specify modifier:
```
"Write email to client but keep it casual"
"Write Slack message but use business_partner tone"
"Draft proposal with family_client warmth"
```

Explicit override always takes precedence over defaults.

### No Modifier Specified

When user requests content without modifier:
- Use base voice from profile without calibration
- Apply patterns as extracted
- No formality adjustment

## Application Process

The full workflow for applying voice to generated content.

### Step 1: Load Profile

1. Check primary location (`~/.agent-pm/voice/voice_profile.md`)
2. If not found, check fallback (`ops/voice/voice_profile.md`)
3. If neither found, skip voice application (generate neutral content)
4. If found, validate profile structure
5. If invalid, skip voice application (generate neutral content)

### Step 2: Determine Auto-Apply

1. Detect content type from user request
2. Check if content type trained in profile
3. Check for suppression patterns
4. Determine if voice should apply:
   - Content type in profile + No suppression = Auto-apply
   - Content type not in profile = No auto-apply
   - Suppression detected = No auto-apply
   - Explicit "in my voice" request = Apply regardless

### Step 3: Select Modifier

1. Detect modifier from user request
2. If no modifier detected:
   - Client-facing content → Default to `professional_client`
   - Internal/team content → Use base voice
   - Ambiguous → Use base voice
3. If modifier explicitly specified → Use that modifier
4. Load modifier calibration rules

### Step 4: Generate Content

1. Load relevant sections from voice profile:
   - Core Voice DNA (always)
   - Content type specific patterns
   - Anti-patterns (what NOT to do)
   - Quality checklist
2. Apply modifier calibration if specified
3. Generate content following voice patterns
4. Apply quality checklist verification
5. Return generated content

### Step 5: Offer Correction Learning

After generation, determine if correction should be offered:

**Correction counter logic:**
```
If generation count ≤ 3:
  Offer correction after this generation

Else if generation count % 5 == 0:
  Offer correction after this generation

Else:
  Skip correction offer (but user can manually correct)
```

**Correction offer format:**
```
[Generated content above]

---

Does this match your voice? If not, share your edited version and I'll learn from it.
```

If user shares edited version → Follow correction learning protocol

If user says "looks good" or continues without edit → No action needed

## Quality Verification

Before returning generated content, verify against voice profile quality checklist.

### Verification Checklist

From voice profile's "Quality Checklist" section:

1. **Core Voice DNA check:**
   - Sentence structure matches patterns?
   - Punctuation style consistent?
   - Signature traits present?

2. **Content type patterns check:**
   - Opening matches profile examples?
   - Body structure follows DO patterns?
   - Closing matches profile examples?
   - None of the DON'T patterns present?

3. **Anti-patterns check:**
   - AI-isms avoided? (check profile anti-patterns list)
   - Generic phrases replaced with specific voice?
   - Tone matches expected calibration?

4. **Modifier calibration check:**
   - If modifier used, formality adjusted correctly?
   - Core voice DNA still present?
   - Calibration applied without losing personality?

### Quality Failure Handling

If verification fails:
1. DO NOT expose failure to user
2. Regenerate content with stricter pattern adherence
3. Re-verify against checklist
4. If second attempt fails, log to `ops/logs/voice-application.json`
5. Return best attempt (don't block on perfection)

## Error Handling

Handle common failure scenarios gracefully.

### Profile Not Found

**Scenario:** No profile exists at either location

**Agent behavior:**
- Generate content without voice application
- Use neutral professional tone
- DO NOT mention voice unless user explicitly requests it
- DO NOT suggest training unless user asks about voice

**If user explicitly requests voice:**
```
User: "Write email in my voice"
Agent: "I don't have a voice profile yet. Want to train my voice? (5-step process, ~15 minutes)"
```

### Corrupted Profile

**Scenario:** Profile exists but is malformed or incomplete

**Agent behavior:**
- Detect corruption during validation
- Log to `ops/logs/voice-application.json`:
  ```json
  {"timestamp": "2026-01-25T16:00:00Z", "event": "profile_corrupted", "location": "~/.agent-pm/voice/voice_profile.md", "issue": "Missing Core Voice DNA section"}
  ```
- Fall back to neutral generation (no voice)
- DO NOT expose error to user
- User can retrain voice if they notice issues

### Content Type Not Trained

**Scenario:** User requests voice for content type not in profile

**Example:**
```
User: "Write Slack message in my voice"
[Profile only has Email and Technical Documentation]
```

**Agent behavior:**
- Voice does NOT auto-apply (type not trained)
- Generate neutral content for that type
- After generation, optionally suggest expanding profile:
  ```
  I don't have Chat training in your voice profile yet. Want to add it? (5-10 chat samples needed)
  ```

### Modifier Conflict

**Scenario:** User requests conflicting modifiers

**Example:**
```
User: "Write formal casual email to client"
```

**Agent behavior:**
1. Detect conflict (formal vs casual)
2. Ask for clarification:
   ```
   I'm seeing conflicting tone requests (formal + casual). Which should I use?
   1. Formal (conservative, professional)
   2. Casual (relaxed, conversational)
   ```
3. Wait for user selection
4. Apply selected modifier

### Storage Write Failure

**Scenario:** Correction learning fails to save updated profile

**Agent behavior:**
1. Attempt primary location write
2. If fails, attempt fallback location write
3. If both fail:
   ```
   I learned the correction but couldn't save the updated profile (permission issue?).

   Your correction: [summary]

   I'll apply this learning in this session. Want to troubleshoot the storage issue?
   ```
4. Apply learning to in-memory profile for current session
5. Log error for debugging

## Integration

How voice application integrates with other protocols.

### With Voice Training Protocol

Voice training creates the profile → Voice application uses it

**Sequence:**
1. User completes voice training (`docs/protocols/voice-training.md`)
2. Profile saved to `~/.agent-pm/voice/voice_profile.md`
3. Voice application loads profile and applies to content
4. Correction learning updates profile over time

### With Correction Learning Protocol

Voice application offers corrections → Correction learning improves profile

**Sequence:**
1. Voice application generates content
2. Offers correction learning (first 3, then every 5th)
3. User provides edited version
4. Correction learning protocol extracts patterns (`docs/protocols/correction-learning.md`)
5. Profile updated with learned patterns
6. Next generation uses improved profile

### With Specialist Coordination

Specialists use voice for client-facing deliverables

**Sequence:**
1. User invokes specialist: "Develop strategy for Acme Corp"
2. Specialist loads voice profile during startup
3. Specialist generates deliverable sections with voice applied
4. Deliverable saved to `entities/Acme Corp/deliverables/`
5. Correction learning available for specialist outputs

See: `docs/protocols/specialist-coordination.md` (Voice Integration section)

### With Entity Workflows

Entity-specific content generation uses voice automatically

**Examples:**
- "Email Acme Corp about project status" → Voice applies
- "Draft proposal for Beta Industries" → Voice applies with professional_client
- "Slack message to team about Gamma" → Voice applies with casual

## Examples

Full application examples showing realistic workflows.

### Example 1: Auto-Applied Email

**User:**
```
Write email to Sarah at Acme Corp about the Q1 results
```

**Agent (internal process):**
```
1. Load profile: ~/.agent-pm/voice/voice_profile.md found
2. Detect content type: Email
3. Check profile: Email patterns exist
4. Auto-apply: Yes
5. Detect modifier: "to Sarah at Acme Corp" → professional_client
6. Generate with voice + professional_client calibration
7. Verify against quality checklist
8. Return content
9. Check correction counter: 2nd generation → Offer correction
```

**Agent (to user):**
```
Subject: Q1 Results Overview

Hi Sarah,

Wanted to share the Q1 results with you. We came in at 23% growth over Q4, which puts us ahead of our conservative projections.

The breakdown:
- Revenue: $2.3M (+18% YoY)
- Customer acquisition: 47 new accounts
- Retention: 94% (up from 91%)

Happy to walk through the details on our call Thursday. Let me know if you want the full deck beforehand.

Best,
[Your name]

---

Does this match your voice? If not, share your edited version and I'll learn from it.
```

### Example 2: Suppressed Voice

**User:**
```
Write email template for new client onboarding (generic, not my voice)
```

**Agent (internal process):**
```
1. Load profile: Found
2. Detect content type: Email
3. Detect suppression: "generic, not my voice"
4. Auto-apply: No (suppression detected)
5. Generate neutral template
6. Return without voice patterns
7. Skip correction offer (suppression = no learning)
```

**Agent (to user):**
```
Subject: Welcome to [Company Name]

Dear [Client Name],

Thank you for choosing [Company Name]. We're excited to begin working together.

Next steps:
1. Review and sign the attached agreement
2. Schedule kickoff call (link below)
3. Provide access to [relevant systems]

We've assigned [Team Member] as your primary contact. They'll reach out within 24 hours to coordinate the kickoff.

Looking forward to a successful partnership.

Best regards,
[Your Name]
[Title]
```

### Example 3: Modifier Adjustment

**User:**
```
Write Slack message to team about the Acme project delay, but keep it professional
```

**Agent (internal process):**
```
1. Load profile: Found
2. Detect content type: Chat (Slack)
3. Detect modifier override: "keep it professional" → business_partner (not casual)
4. Auto-apply: Yes (Chat in profile)
5. Generate with voice + business_partner calibration
6. Return content
7. Check correction counter: 7th generation → Offer correction
```

**Agent (to user):**
```
Hey team - heads up that the Acme implementation is pushing back two weeks. Their infrastructure team needs more time for the security review (totally reasonable given their compliance requirements).

Updated timeline:
- Security review: Jan 30 → Feb 13
- UAT: Feb 6 → Feb 20
- Go-live: Feb 13 → Feb 27

This doesn't affect the Beta or Gamma schedules. Let me know if you have questions.

---

Does this match your voice? If not, share your edited version and I'll learn from it.
```

### Example 4: Content Type Not Trained

**User:**
```
Write Slack message to engineering about the API bug
```

**Agent (internal process):**
```
1. Load profile: Found
2. Check profile: Only Email and Technical Documentation trained
3. Detect content type: Chat
4. Chat NOT in profile → No auto-apply
5. Generate neutral content
6. Suggest expanding profile after generation
```

**Agent (to user):**
```
Hey engineering - spotted an issue with the /users API endpoint. It's returning 500s when the email field is missing (should be 400 bad request).

Steps to reproduce:
1. POST /users without email field
2. Observe 500 response

Expected: 400 with validation error
Actual: 500 internal server error

Can someone take a look?

---

I don't have Chat/Slack training in your voice profile yet. Want to add it? (Would need 5-10 Slack message samples)
```

### Example 5: Explicit Voice Request

**User:**
```
Create ENTITY_PROFILE.md template in my voice
```

**Agent (internal process):**
```
1. Load profile: Found
2. Detect content type: Template (normally manual-only)
3. Detect explicit request: "in my voice"
4. Override auto-apply rules → Apply voice
5. No modifier (templates are instructional)
6. Generate with base voice patterns
7. Return content
```

**Agent (to user):**
```
# Entity Profile: [Entity Name]

Quick reference for everything we know about [Entity Name].

## Basic Info

**Type:** [Client / Partner / Internal Team]
**Industry:** [Industry]
**Relationship Start:** [Date]
**Primary Contact:** [Name, Role]

## What They Do

[Brief description of their business, 2-3 sentences]

## Our Work Together

**Current Projects:**
- [Project 1]
- [Project 2]

**Completed Work:**
- [Past project 1] (Completed: [Date])

## Key Contacts

| Name | Role | Best For |
|------|------|----------|
| [Name] | [Title] | [What to contact about] |

## Communication Preferences

**Response Time:** [Typical response window]
**Preferred Channel:** [Email / Slack / Phone / etc]
**Meeting Cadence:** [Frequency and format]

## Important Dates

- [Event]: [Date]
- [Milestone]: [Date]

## Notes

[Anything else important to remember]

---

*Last updated: [Date]*
```

## Related Protocols

- **Voice Training:** See `docs/protocols/voice-training.md`
- **Correction Learning:** See `docs/protocols/correction-learning.md`
- **Specialist Coordination:** See `docs/protocols/specialist-coordination.md` (Voice Integration section)

---

*Protocol: voice-application*
*Version: 1.0*
*Last updated: 2026-01-25*
