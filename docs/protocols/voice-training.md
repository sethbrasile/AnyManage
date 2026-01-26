# Voice Training Protocol

Complete documentation for training the system to write in a user's personal voice and tone.

## Overview

This protocol guides the complete voice training workflow from initiation through profile creation. Users can train the system to write emails, documents, chat messages, and reports in their natural style.

**Key Principles:**
- Capability assessment determines environment routing
- Content type selection focuses training on user needs
- Sample validation ensures quality input
- Profile review before saving prevents bad profiles
- Graceful degradation for terminal-only environments
- Privacy-conscious sample handling

**Reference:** Voice profile structure adapted from production usage (proven comprehensive coverage).

## Trigger Detection

The agent should recognize these patterns as voice training requests:

### Natural Language Patterns

| Pattern | Example |
|---------|---------|
| "Train my voice" | "Train my voice" |
| "Voice training" | "Start voice training" |
| "Help me train my voice" | "Help me train my voice" |
| "Learn my writing style" | "Learn my writing style" |
| "Create my voice profile" | "Create my voice profile" |

### Slash Command

```
/train-voice
```

Example: `/train-voice`

## Capability Assessment

Before starting training, assess environment capabilities to route appropriately.

### Step 1: Check MCP Availability

Check if file upload is available (MCP with file reading):

```
Try to detect:
- Can agent read user-provided files?
- Can agent access file system for uploads?
- Is this a terminal-only environment?
```

**Detection method:**
- Claude Desktop, ChatGPT web, API with file tools → Full capability
- Terminal-only (SSH, basic CLI) → Limited capability

### Step 2: Route Based on Capabilities

**Full Capability Environment:**
- User can upload files or paste content
- Agent can extract voice patterns directly
- Proceed with content type selection

**Limited Capability Environment:**
- User cannot upload files easily
- Generate handoff prompt (see Handoff Prompt section)
- User takes prompt to capable environment
- User returns with completed profile

### Handoff Prompt Generation

For terminal-only environments:

**Agent response:**
```
This environment can't process file uploads easily. I'll generate a complete extraction prompt you can use in Claude Desktop or ChatGPT web.

Here's what to do:
1. Copy the prompt below
2. Open Claude Desktop or ChatGPT web
3. Paste the prompt and follow instructions
4. Copy the resulting profile back here
5. I'll save it for you

Ready for the prompt?
```

**Generate using:** `templates/VOICE_EXTRACTION_PROMPT.md`

See "Handoff Protocol" section for complete process.

## Content Type Selection

Present 4 content types and allow user to select 1-4 types to train.

### Step 1: Present Options

**Agent response:**
```
Which types of writing should I learn?

Select 1-4 types you use most often:

1. [ ] Emails - Client communication, business correspondence
2. [ ] Technical Documentation - READMEs, guides, process docs
3. [ ] Chat Messages - Slack, Teams, informal communication
4. [ ] Reports/Proposals - Formal documents, presentations

Example: "1, 2, 4" or "Emails and chat"
```

### Step 2: Parse Selection

Accept multiple formats:
- Numbers: "1, 2, 4"
- Names: "Emails and chat"
- Checkboxes: User types "x" in brackets
- Natural language: "I want to train emails and technical docs"

**Normalization:**
- Extract 1-4 selections
- Map to canonical types: emails, technical, chat, reports
- Confirm with user before proceeding

**Validation:**
- Minimum: 1 type selected
- Maximum: 4 types selected
- If invalid: Ask user to select 1-4 types

### Step 3: Confirm Selection

**Agent response:**
```
Training voice for:
- Emails
- Technical Documentation
- Chat Messages

Next: Gather 5-10 writing samples for each type.
```

## Sample Collection

Guide user to provide 5-10 samples per selected content type.

### Sample Requirements

**For each content type:**
- Minimum: 5 samples
- Recommended: 7-10 samples
- Quality over quantity

**Good samples:**
- Written by the user (not AI-generated)
- Natural voice (not heavily edited)
- Variety of contexts (different recipients, topics, lengths)
- Recent (current voice, not from years ago)

**Avoid:**
- AI-generated content
- Heavily templated responses
- Very old writing (voice may have evolved)
- Formal writing that doesn't reflect natural style

### Sample Collection Process

**Agent guidance per content type:**

```
For Emails:
Provide 5-10 emails you wrote that sound like YOU.

Good sources:
- Sent folder (recent emails to clients)
- Various recipients (formal and casual)
- Different lengths (short replies, long updates)

You can:
- Paste email text directly (remove signatures/footers)
- Upload .txt files with email content
- Reference saved files

Ready to paste your first email sample?
```

**For Technical Documentation:**
```
For Technical Documentation:
Provide 5-10 examples of docs you wrote.

Good sources:
- READMEs you authored
- Process documentation
- Technical guides
- How-to articles

You can:
- Paste markdown/text directly
- Upload .md or .txt files
- Reference documentation files

Ready to paste your first doc sample?
```

**For Chat Messages:**
```
For Chat Messages:
Provide 5-10 examples of your chat communication.

Good sources:
- Slack/Teams messages (work context)
- Casual work conversations
- Both short and longer messages

You can:
- Paste message threads
- Upload .txt files with conversations

Ready to paste your first chat sample?
```

**For Reports/Proposals:**
```
For Reports/Proposals:
Provide 5-10 examples of formal documents you wrote.

Good sources:
- Proposals you authored
- Status reports
- Presentation content
- Executive summaries

You can:
- Paste document sections
- Upload .docx, .pdf, .txt files
- Reference document files

Ready to paste your first report sample?
```

### Sample Validation

Before accepting samples:

**Check 1: Sufficient quantity**
- Minimum 5 samples per type
- If less: Request more samples

**Check 2: Sample quality**
- Not empty
- Not gibberish
- Reasonable length (>50 characters)

**Check 3: User confirmation**
```
Collected for [Content Type]:
- 7 samples (345 words average)

Looks good? Or add more samples?
```

### Privacy Handling

**Before extraction:**
```
Note: I'll analyze patterns (tone, structure, style) but won't memorize specific content like names, emails, or sensitive details.

Your samples stay private. Proceed?
```

User confirms before extraction begins.

## Voice Extraction

Extract voice patterns from collected samples and generate profile.

### Extraction Process

**Step 1: Analyze patterns across samples**

For each content type:
- Sentence structure patterns (length, complexity, fragments)
- Punctuation style (contractions, dashes, questions)
- Signature traits (recurring phrases, patterns)
- Hedging language (how uncertainty is expressed)
- Intensifiers (casual amplifiers)
- Humor style (or absence)
- Tone calibration by context

**Step 2: Cross-content analysis**

Compare patterns across content types:
- What stays consistent? (Core Voice DNA)
- What changes by context? (Relationship calibration)
- What does AI typically get wrong? (Anti-patterns)

**Step 3: Generate profile**

Use `templates/VOICE_PROFILE_TEMPLATE.md` as structure:

```markdown
# [User Name] - Voice Profile

## Overview
[Brief summary of voice characteristics]

## CORE VOICE DNA
[Patterns that stay consistent across all content types]

### Sentence Structure
[Extracted patterns]

### Punctuation Style
[Extracted patterns]

### Signature Traits
[Extracted recurring elements]

### Hedging Language
[How user expresses uncertainty]

### Intensifiers
[Casual amplifiers user employs]

### Humor Style
[Humor patterns or absence]

## EMAIL PATTERNS
[Patterns specific to emails - openings, body, closings]

## TECHNICAL COMMUNICATION
[Patterns for docs and technical writing]

## CHAT/INFORMAL PATTERNS
[Patterns for casual communication]

## RELATIONSHIP CALIBRATION
[How voice adjusts by recipient relationship]

## WHAT AI TYPICALLY DOES WRONG
[Common AI mistakes vs. user's actual style]

## THINGS [USER] NEVER SAY
[Phrases user would never use]

## QUALITY CHECKLIST
[Verification items for AI-generated content]
```

### Extraction Quality

**Good extraction includes:**
- Specific examples for each pattern
- DO/DON'T format for clarity
- Concrete anti-patterns (not generic)
- Actionable quality checklist
- Relationship-aware calibration

**Poor extraction includes:**
- Generic descriptions ("casual tone")
- No specific examples
- Vague anti-patterns
- Missing content type sections
- No quality checklist

## Profile Review

Show extracted profile to user for review before saving.

### Review Presentation

**Agent response:**
```
Voice profile extracted. Please review before I save it:

---

[Display full profile here]

---

Does this sound like you?

Options:
1. Approve and save
2. Request specific changes
3. Add more samples and re-extract
4. Start over
```

### Handling Feedback

**If user approves:**
- Proceed to Profile Storage

**If user requests changes:**
```
User: "I don't use that much formal language"

Agent: I'll adjust the formality level. Re-extracting patterns with more casual tone...

[Re-extract with user feedback incorporated]
[Present revised profile]
```

**If user wants more samples:**
```
User: "Let me add more chat samples"

Agent: Go ahead. Paste additional chat samples and I'll re-run extraction.
```

### Iterative Refinement

Support multiple rounds of review:
1. Extract from samples
2. Show profile
3. User provides feedback
4. Re-extract with feedback
5. Repeat until approved

## Profile Storage

Save approved profile to storage location with fallback handling.

### Storage Priority

**Primary location:**
```
~/.anymanage/voice/voice_profile.md
```

**Fallback location:**
```
ops/voice/voice_profile.md
```

### Storage Logic

**Step 1: Try primary location**

```
Check if ~/.anymanage/ exists:
- If yes: Save to ~/.anymanage/voice/voice_profile.md
- If no: Create directory, then save
```

**Step 2: Fallback if needed**

If primary location fails (permissions, path issues):
```
Fall back to: ops/voice/voice_profile.md
Note: This is project-specific (not portable across projects)
```

### Storage Workflow

1. Prepare profile content (add metadata footer)
2. Try primary location
3. If fails, try fallback location
4. Confirm save location to user
5. Create CORRECTIONS.md placeholder

**Profile metadata footer:**
```markdown
---

*Last trained: 2026-01-25*
*Profile version: 1.0*
```

### CORRECTIONS.md Creation

Create correction learning file alongside profile:

**Primary:**
```
~/.anymanage/voice/CORRECTIONS.md
```

**Fallback:**
```
ops/voice/CORRECTIONS.md
```

**Initial content:**
```markdown
# Voice Corrections

When the system writes in your voice but gets something wrong, log it here.

Format:
- **What AI wrote:** [paste the wrong version]
- **What you'd actually write:** [paste your corrected version]
- **Date:** [date]

The system will learn from these corrections to improve your voice profile.

---

## Corrections Log

[Empty - ready for user corrections]
```

### Success Confirmation

**Agent response:**
```
Voice profile saved!

Location: ~/.anymanage/voice/voice_profile.md

Your voice is now active. The system will automatically use it when:
- Writing emails
- Creating technical documentation
- Drafting chat messages
- Generating reports

To improve over time:
- Edit content the system generates
- Log corrections in ~/.anymanage/voice/CORRECTIONS.md
- Periodically tell me: "Review my voice corrections"

Try it: "Write an email to [recipient] about [topic]"
```

## Voice Application

How the system uses stored voice profiles (full protocol in Plan 03).

### Auto-Activation

Voice profile automatically activates when:
- User requests content generation ("Write an email...")
- Content type matches trained types
- Profile exists in storage location

**Loading order:**
1. Check ~/.anymanage/voice/voice_profile.md
2. If not found, check ops/voice/voice_profile.md
3. If not found, use default agent voice

### Content Type Detection

Agent detects content type from user request:
- "Write an email" → emails pattern
- "Draft a technical doc" → technical pattern
- "Create a Slack message" → chat pattern
- "Generate a proposal" → reports pattern

### Voice Override

User can override voice:
```
User: "Write an email to client, but more formal than my usual style"
Agent: [Uses voice profile but increases formality]
```

## Correction Learning

System improves voice profile from user corrections (full protocol in Plan 03).

### In-Session Corrections

**User provides correction:**
```
User: "Here's what I changed: [paste edited version]"

Agent: Got it. I adjusted:
- Reduced formality in openings
- Changed "I believe" to "I think"

Updated voice profile with correction.
```

### Async Corrections (CORRECTIONS.md)

User logs corrections independently:
```
User adds to ~/.anymanage/voice/CORRECTIONS.md:

## Corrections Log

### 2026-01-25

**What AI wrote:** "I hope this message finds you well."
**What I'd actually write:** "Hey - quick update:"
**Date:** 2026-01-25
```

**User triggers learning:**
```
User: "Review my voice corrections"

Agent: [Reads CORRECTIONS.md]
Agent: Found 3 corrections. Updating voice profile...
Agent: - Eliminated formal email openings
       - Added casual greeting patterns
       - Updated tone calibration

Voice profile updated. Corrections archived.
```

## Handoff Protocol

For terminal-only environments, generate portable extraction prompt.

### When to Use Handoff

**Trigger handoff when:**
- Environment can't easily upload files
- User is in SSH/terminal session
- File reading MCP not available

**Agent detects limitations:**
```
Agent: This environment can't process file uploads easily.

I'll generate a complete extraction prompt you can use in Claude Desktop or ChatGPT web.
```

### Handoff Prompt Generation

**Step 1: Load template**

Read `templates/VOICE_EXTRACTION_PROMPT.md`

**Step 2: Customize for user**

Replace placeholders:
- `[CONTENT_TYPES]` → User's selected types
- `[INSTRUCTIONS]` → Type-specific sample guidance

**Step 3: Present to user**

```
Copy this prompt and use it in Claude Desktop or ChatGPT web:

---

[Full extraction prompt from template]

---

After completing the exercise:
1. Copy the generated profile
2. Return here and paste it
3. I'll save it to your voice storage

Ready to copy?
```

### Receiving Completed Profile

**User returns with profile:**
```
User: "Here's the profile: [paste]"

Agent: [Validate profile structure]
Agent: [Check for required sections]
Agent: [Save to storage location]

Agent: Voice profile saved!

Location: ~/.anymanage/voice/voice_profile.md

Your voice is now active.
```

**Validation before saving:**
- Check for required sections (Core Voice DNA, patterns, anti-patterns)
- Verify reasonable content (not template placeholders)
- Confirm with user if sections missing

## Error Handling

Handle common failure scenarios gracefully.

### User Has No Samples

**User says:** "I don't have any writing samples"

**Agent response:**
```
No worries. Here are some options:

1. Use samples from your email sent folder
2. Find recent Slack/Teams messages
3. Check docs you've written (READMEs, guides)
4. Start with just 5 samples and refine later

What type of writing do you do most often? I'll help you find samples.
```

### Insufficient Samples

**User provides 2 samples when 5 required**

**Agent response:**
```
I need at least 5 samples per content type for good extraction.

You've provided: 2 samples

Want to:
1. Add 3 more samples (recommended)
2. Select fewer content types
3. Skip this type and focus on others
```

### Poor Quality Samples

**Agent detects AI-generated or templated samples**

**Agent response:**
```
These samples look templated or AI-generated. For best results, I need samples YOU actually wrote in your natural voice.

Try:
- Recent sent emails (not templates)
- Docs you authored (not copied)
- Casual messages you wrote

Want to provide different samples?
```

### Storage Write Failure

**Both primary and fallback locations fail**

**Agent behavior:**
1. Display profile in chat (user can copy manually)
2. Provide manual save instructions
3. Do NOT expose technical error

**Agent response:**
```
Couldn't save to voice storage automatically.

Here's your complete profile - please save it manually:

[Display full profile]

Save to: ~/.anymanage/voice/voice_profile.md

Or paste it back to me later and I'll retry saving.
```

### Incomplete Extraction

**Agent can't extract meaningful patterns from samples**

**Agent response:**
```
Having trouble extracting consistent patterns from these samples.

This could mean:
- Samples are too varied in style
- Not enough samples per type
- Samples from different time periods

Want to:
1. Add more samples
2. Focus on one writing style
3. Start with just one content type
```

### User Abandonment

**User starts training but doesn't complete**

**No persistent state needed** - Training is session-based

If user returns:
```
User: "Let's finish voice training"

Agent: We can start fresh. Which content types do you want to train?
```

## Example Interactions

### Example 1: Full Capability Environment

**User:**
```
Train my voice
```

**Agent:**
```
I'll help you train your voice.

Which types of writing should I learn?

Select 1-4 types you use most often:

1. [ ] Emails - Client communication, business correspondence
2. [ ] Technical Documentation - READMEs, guides, process docs
3. [ ] Chat Messages - Slack, Teams, informal communication
4. [ ] Reports/Proposals - Formal documents, presentations

Example: "1, 2" or "Emails and chat"
```

**User:**
```
1 and 3 - emails and chat
```

**Agent:**
```
Training voice for:
- Emails
- Chat Messages

For Emails:
Provide 5-10 emails you wrote that sound like YOU.

Ready to paste your first email sample?
```

**User:**
```
[Pastes 7 email samples]
```

**Agent:**
```
Collected for Emails:
- 7 samples (280 words average)

For Chat Messages:
Provide 5-10 examples of your chat communication.

Ready to paste your first chat sample?
```

**User:**
```
[Pastes 6 chat samples]
```

**Agent:**
```
Collected for Chat:
- 6 samples (45 words average)

Note: I'll analyze patterns (tone, structure, style) but won't memorize specific content like names or sensitive details.

Proceed with extraction?
```

**User:**
```
Yes
```

**Agent:**
```
Extracting voice patterns...

Voice profile extracted. Please review before I save it:

---

# [User Name] - Voice Profile

## Overview
Direct, casual communication style with minimal formalities. Short sentences, contractions, dashes for emphasis. Friendly but efficient.

## CORE VOICE DNA

### Sentence Structure
- **Length patterns:** Mix of short (5-10 words) and medium (15-20 words). Rarely exceed 25 words.
- **Transitions:** Avoids formal transitions. Starts directly: "Here's the update" not "I wanted to provide you with an update"
- **Paragraph style:** 2-4 sentences max. One idea per paragraph.

### Punctuation Style
- **Contractions:** Liberal use - "I'm", "we're", "here's", "that's"
- **Dashes/parentheses:** Uses dashes for asides - like this - not parentheses
- **Question style:** Direct questions without preamble: "Can you send that by Friday?"

[... rest of profile ...]

---

Does this sound like you?

Options:
1. Approve and save
2. Request specific changes
3. Add more samples and re-extract
```

**User:**
```
Approve and save
```

**Agent:**
```
Voice profile saved!

Location: ~/.anymanage/voice/voice_profile.md

Your voice is now active. The system will automatically use it when writing emails and chat messages.

To improve over time:
- Edit content the system generates
- Log corrections in ~/.anymanage/voice/CORRECTIONS.md

Try it: "Write an email to [recipient] about [topic]"
```

### Example 2: Terminal-Only Environment (Handoff)

**User:**
```
/train-voice
```

**Agent:**
```
This environment can't process file uploads easily. I'll generate a complete extraction prompt you can use in Claude Desktop or ChatGPT web.

Here's what to do:
1. Copy the prompt below
2. Open Claude Desktop or ChatGPT web
3. Paste the prompt and follow instructions
4. Copy the resulting profile back here
5. I'll save it for you

Ready for the prompt?
```

**User:**
```
Yes
```

**Agent:**
```
Copy this entire prompt and use it in Claude Desktop or ChatGPT web:

---

[Full extraction prompt from VOICE_EXTRACTION_PROMPT.md]

---

After completing the exercise:
1. Copy the generated profile
2. Return here and paste it
3. I'll save it to your voice storage
```

**User (returns later):**
```
Here's the profile:

# Jane Smith - Voice Profile

## Overview
[... full profile pasted ...]
```

**Agent:**
```
[Validates profile structure]

Voice profile looks good. Saving now...

Voice profile saved!

Location: ~/.anymanage/voice/voice_profile.md

Your voice is now active.
```

### Example 3: Insufficient Samples

**User:**
```
Train my voice
```

**Agent:**
```
Which types of writing should I learn?

Select 1-4 types you use most often:

1. [ ] Emails - Client communication, business correspondence
2. [ ] Technical Documentation - READMEs, guides, process docs
3. [ ] Chat Messages - Slack, Teams, informal communication
4. [ ] Reports/Proposals - Formal documents, presentations
```

**User:**
```
Emails
```

**Agent:**
```
Training voice for:
- Emails

For Emails:
Provide 5-10 emails you wrote that sound like YOU.

Ready to paste your first email sample?
```

**User:**
```
[Pastes 2 email samples]
```

**Agent:**
```
I need at least 5 samples per content type for good extraction.

You've provided: 2 samples

Want to:
1. Add 3 more samples (recommended)
2. Skip emails and choose different type
3. Continue with limited samples (may be less accurate)
```

**User:**
```
Add more samples

[Pastes 4 more email samples]
```

**Agent:**
```
Collected for Emails:
- 6 samples (310 words average)

Looks good. Proceeding with extraction...
```

### Example 4: Profile Review with Changes

**User:**
```
Train my voice
```

**Agent:**
```
[... collection process ...]

Voice profile extracted. Please review before I save it:

---

[Profile includes: "Formal email openings: Dear Sir/Madam"]

---

Does this sound like you?
```

**User:**
```
No, I never use "Dear Sir/Madam" - that's way too formal
```

**Agent:**
```
Got it. I'll adjust the formality level. Re-extracting patterns with more casual tone...

Updated profile:

---

[Updated profile with: "Email openings: Hey [name]," or "Hi -"]

---

Better?
```

**User:**
```
Yes, that's me. Save it.
```

**Agent:**
```
Voice profile saved!

Location: ~/.anymanage/voice/voice_profile.md
```

## Integration with Other Workflows

### After Onboarding

User can train voice immediately after system setup:
```
User: Just set up agent-pm, want to train my voice
Agent: Great. Let's start voice training...
```

### Before Specialist Work

Voice applies to specialist deliverables:
```
User: Train my voice first
[Complete training]

User: Run strategy specialist for Acme Corp
[Specialist uses voice profile when generating deliverable]
```

### With Entity Work

Voice applies to entity-specific content:
```
User: Write email to Acme Corp about project status
Agent: [Uses voice profile + entity context]
```

## Related Protocols

- **Voice Application:** See `docs/protocols/voice-application.md` (Plan 03)
- **Session Protocol:** See `docs/protocols/session-protocol.md`
- **Command Patterns:** See `docs/protocols/command-patterns.md`

## Related Templates

- **Training Exercise:** See `templates/VOICE_TRAINING_EXERCISE.md`
- **Voice Profile Template:** See `templates/VOICE_PROFILE_TEMPLATE.md`
- **Extraction Prompt:** See `templates/VOICE_EXTRACTION_PROMPT.md`

---

*Protocol: voice-training*
*Version: 1.0*
*Last updated: 2026-01-25*
