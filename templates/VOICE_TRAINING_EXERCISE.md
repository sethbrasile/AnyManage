# Voice Training Exercise

**Goal:** Train the system to write in your personal voice and tone.

**Time needed:** 15-30 minutes (one-time setup)

**What you'll need:** 5-10 examples of your actual writing per content type you want to train

---

## Step 1: Choose Your Content Types

Which types of writing do you want the system to learn?

- [ ] **Emails** - Client communication, business correspondence
- [ ] **Technical Documentation** - READMEs, guides, process docs
- [ ] **Chat Messages** - Slack, Teams, informal communication
- [ ] **Reports/Proposals** - Formal documents, presentations

Select 1-3 types you use most often. The system will learn your voice for each.

---

## Step 2: Gather Your Writing Samples

For each content type you selected, gather **5-10 examples** of writing YOU actually wrote:

**For emails:** Find sent emails that sound like YOU (not AI-generated or templated)
**For documents:** Find docs you wrote (not just edited)
**For chat:** Copy messages that show your natural communication style
**For reports:** Find proposals or reports you authored

**Good samples:**
- Variety of recipients/contexts
- Different lengths (short and long)
- Your natural voice (not heavily edited)

**Avoid:**
- AI-generated content
- Heavily templated responses
- Writing from your first week at a job (voice may have evolved)

---

## Step 3: Run Voice Training

Say: **"Train my voice"** or **"/train-voice"**

The system will:
1. Check what capabilities are available
2. Ask you to provide samples for each content type
3. Extract your voice patterns
4. Show you the profile for review
5. Save it after your approval

---

## Step 4: Review and Approve

Before saving, you'll see your extracted voice profile including:

- Core Voice DNA (sentence structure, punctuation, signature traits)
- Patterns by content type (what you DO)
- Anti-patterns (what you NEVER do)
- Quality checklist

Read through and confirm it sounds like you. You can request changes before saving.

---

## Step 5: Learn from Corrections

After training, whenever the system writes in your voice:

**Option A - Tell it directly:**
"Here's what I changed: [paste your edited version]"

**Option B - Log corrections:**
Add to `ops/voice/CORRECTIONS.md` (the system will learn from this file)

The more you correct, the better it gets.

---

## Tips for Best Results

1. **Be yourself** - Don't sanitize your samples. Include your quirks.
2. **Variety matters** - Different recipients, different moods, different purposes
3. **Recent is better** - Your voice may have evolved over time
4. **Quality over quantity** - 5 great samples beat 20 mediocre ones

---

## Troubleshooting

**"My voice profile doesn't sound like me"**
- Add more diverse samples and re-train
- Use the correction learning to fix specific patterns

**"I can't upload files"**
- The system will provide a portable prompt you can use in Claude Desktop or ChatGPT web
- Complete training there, bring the profile back

**"I want different voices for different purposes"**
- One profile with tone modifiers handles this
- Specify "more formal" or "more casual" when requesting output

---

*After completing this exercise, the system will write in your voice. Start with "Write an email to [client] about [topic]" to test it.*
