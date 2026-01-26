# Troubleshooting Guide

Can't figure out what went wrong? You're in the right place. Find your issue below and follow the fix.

---

## Installation Issues

### "command not found: git"

**What this means:** Your computer doesn't have git installed. Git is the tool that downloads the AnyManage files.

**How to fix:**
1. Download git from [git-scm.com/downloads](https://git-scm.com/downloads)
2. Run the installer (click through the defaults)
3. Close and reopen your terminal
4. Try `git --version` to confirm it works

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues) describing your operating system and the error message.

---

### "command not found: claude" (or opencode, codex)

**What this means:** The AI agent isn't installed, or your terminal doesn't know where to find it.

**How to fix:**
1. Make sure you installed the agent (see setup tutorials)
2. Close your terminal completely
3. Open a fresh terminal
4. Try the command again

If it still doesn't work, the installation might have failed:
- **Claude Code:** Reinstall from [claude.ai/code](https://claude.ai/code)
- **opencode:** Run `npm install -g opencode` or `pip install opencode`
- **codex:** Check [OpenAI's documentation](https://platform.openai.com/docs)

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues) with the exact error message.

---

### "Permission denied" during installation

**What this means:** Your computer is blocking the installation because you don't have admin rights for that location.

**How to fix:**

**macOS/Linux:**
```bash
sudo npm install -g opencode
```
Type your password when prompted.

**Windows:**
Run Command Prompt as Administrator:
1. Search for "Command Prompt"
2. Right-click it
3. Select "Run as administrator"
4. Try the install command again

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

### "Repository not found" when cloning

**What this means:** The URL for AnyManage might have changed, or there's a typo.

**How to fix:**
1. Double-check the clone URL for typos
2. Make sure you copied the full URL
3. Check the AnyManage documentation for the current URL

If the URL is correct but still fails:
- The repository might be private (you need access)
- Your internet connection might be blocking it

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

## Startup Issues

### "Agent doesn't load INSTRUCTIONS.md"

**What this means:** The agent started but didn't read the instructions file that tells it how to work.

**How to fix:**
1. Make sure you're in the anymanage folder: `pwd` should end with `/anymanage`
2. Check the file exists: `ls INSTRUCTIONS.md`
3. If missing, you might have cloned incorrectly - delete the folder and clone again

If the file exists but isn't loading:
- Try quitting the agent (`Ctrl+C`) and starting fresh
- Check that the file isn't corrupted (open it in a text editor)

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues) with details about what the agent is doing instead.

---

### "STATE.md not found" warning

**What this means:** This is normal for a fresh project. The agent is just telling you there's no saved session state.

**How to fix:**
This isn't really a problem. When the agent asks "Want me to create STATE.md?", say yes. It creates a file to track your work across sessions.

If you expected STATE.md to exist (you've used the project before):
- Check you're in the right folder
- Maybe someone deleted it - the agent will create a new one

---

### "Session doesn't remember previous work"

**What this means:** Either STATE.md was deleted, or you're starting from a different terminal session without loading context.

**How to fix:**
1. Make sure STATE.md exists in your project root
2. Start the agent fresh - it should load STATE.md automatically
3. If STATE.md exists but history seems missing, check the file contents

State is stored locally. If you:
- Moved to a new computer: State doesn't transfer automatically
- Deleted the anymanage folder: State was lost
- Used a different folder: Each folder has its own state

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

### "Agent seems to ignore instructions"

**What this means:** The agent is running but not following the INSTRUCTIONS.md rules (like greeting you when it shouldn't, or not recognizing commands).

**How to fix:**
1. Make sure you're in the anymanage folder (not a subfolder)
2. Check INSTRUCTIONS.md wasn't modified accidentally
3. Try quitting (`Ctrl+C`) and restarting

If the agent consistently misbehaves:
- Different agents (Claude Code, opencode, codex) may have slightly different behaviors
- Some edge cases aren't covered - the agent is doing its best

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues) with an example of what you asked and what happened.

---

## Entity Management Issues

### "Entity folder wasn't created"

**What this means:** You asked to add a client (or project, etc.) but no folder appeared.

**How to fix:**
1. Check the entities folder: `ls entities/`
2. The name might be slightly different (spaces, capitalization)
3. Try the command again with simpler phrasing: "Add new client [Name]"

Common causes:
- The agent might have asked a clarifying question you didn't answer
- Name collision with existing entity
- Typo in entity name

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

### "Profile didn't update"

**What this means:** You added information to an entity's profile but it's not there.

**How to fix:**
1. Check the right entity: Maybe the update went to a different one
2. Look at the profile file directly: `entities/[Name]/ENTITY_PROFILE.md`
3. The information might be in a different section than expected

If the file wasn't modified at all:
- Try more explicit phrasing: "Add [fact] to [Entity Name]'s profile"
- Make sure the entity exists first

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

### "Notes didn't process correctly"

**What this means:** You ran note processing but the results don't look right - tasks missing, wrong categorization, etc.

**How to fix:**
1. Check the source notes are clear (agent does better with structured notes)
2. Look for the "Follow-ups" section - ambiguous items end up there
3. Try processing again with clarifications

If important information was missed:
- Be more explicit in your notes ("Action item:", "John's phone:", etc.)
- Review what was extracted and manually add anything missing
- Use "Add [fact] to profile" for things that should be profile info

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues) with example notes and what you expected vs. got.

---

### "Duplicate entity created"

**What this means:** You accidentally created two folders for the same client (maybe "Acme" and "Acme Corp").

**How to fix:**
1. Decide which one to keep
2. Manually move any important files from the duplicate
3. Delete the duplicate folder
4. The agent has duplicate detection, but slight name differences can slip through

To prevent in future:
- Use consistent naming
- Check "Show all clients" before creating

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

## Skill and Specialist Issues

### "Skill not found"

**What this means:** You asked for a skill that doesn't exist or used the wrong name.

**How to fix:**
1. Ask "What skills do you have?" to see available skills
2. Try natural language: "process notes for Acme" instead of skill names
3. Check spelling

Available built-in skills:
- process-notes
- get-status
- weekly-review

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

### "Specialist doesn't produce deliverable"

**What this means:** You ran a specialist (assessment, strategy) but no file appeared in deliverables/.

**How to fix:**
1. Check the deliverables folder: `entities/[Name]/deliverables/`
2. File might have a different name than expected
3. The specialist might still be working (complex operations take time)

If truly nothing was created:
- Try again with explicit request: "Run assessment for [Entity] and save to deliverables"
- Check for error messages in the response

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

### "What specialists do you have?" shows nothing

**What this means:** The agent isn't finding the specialist definitions.

**How to fix:**
1. Make sure you're in the anymanage folder
2. Check the specialists exist: `ls .claude/agents/` or `ls .github/agents/`
3. Try asking differently: "/specialists" or "List available specialists"

If specialists folder is missing:
- You might have an incomplete installation
- Clone the repository again

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

## Voice Training Issues

### "Can't find voice profile"

**What this means:** You've trained your voice but the system can't find the profile.

**How to fix:**
1. Check primary location: `~/.anymanage/voice/voice_profile.md`
2. Check fallback location: `ops/voice/voice_profile.md`
3. If neither exists, you need to run voice training again

The profile might be in the wrong location:
- Primary (portable): `~/.anymanage/voice/`
- Fallback (project): `ops/voice/`

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

### "Voice not being applied"

**What this means:** You have a voice profile but outputs don't sound like you.

**How to fix:**
1. Check the profile exists and has content
2. Make sure the content type is one you trained (emails, docs, etc.)
3. Try explicit request: "Write this in my voice"

Voice only applies to trained content types. If you trained on emails but are writing documentation, the voice may not apply automatically.

To force voice application: "Write [content] using my voice profile"

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

### "Training didn't work as expected"

**What this means:** You went through voice training but the results don't capture your style.

**How to fix:**
1. Check you provided 5-10 samples (more is better)
2. Make sure samples were your actual writing (not edited by others)
3. Try training again with better samples

Good samples:
- Emails you wrote yourself
- Documents in your natural voice
- Not heavily edited by others
- Represent how you actually write (not aspirational)

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues) (don't include your actual writing samples for privacy).

---

## API and Authentication Issues

### "API key not found" or "Unauthorized"

**What this means:** The agent can't authenticate with the AI service.

**How to fix:**

**For opencode/codex:**
1. Make sure your API key is set correctly
2. Check for typos and extra spaces
3. Verify the key is still valid (not expired or revoked)

Set the environment variable:
- macOS/Linux: `export OPENAI_API_KEY='your-key'`
- Windows: `set OPENAI_API_KEY=your-key`

**For Claude Code:**
Claude Code typically handles authentication through the app. Make sure you're logged in.

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

### "Rate limit exceeded"

**What this means:** You've made too many requests too quickly.

**How to fix:**
1. Wait a minute and try again
2. For batch operations, process fewer items at once
3. Consider upgrading your API tier if this happens often

Rate limits protect the service. They're usually per-minute or per-hour.

---

## Git and File Issues

### "Changes aren't being tracked"

**What this means:** Files are modified but git doesn't see them.

**How to fix:**
This shouldn't happen in normal use - the agent handles git automatically. But if it does:
1. The file might be in `.gitignore`
2. Git might not be initialized (rare)
3. The agent might be configured to skip git

You don't need to worry about git. The agent manages it silently.

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

### "File appears corrupted or garbled"

**What this means:** A file has unexpected characters or formatting.

**How to fix:**
1. Try opening the file in a different text editor
2. Check if your editor is using the right encoding (UTF-8)
3. If the file is truly corrupted, check git history for a good version

If you're comfortable with git:
```bash
git log --oneline [filename]
git checkout [commit] -- [filename]
```

**Still stuck?** [Open an issue](https://github.com/your-repo/anymanage/issues).

---

## Getting More Help

If your issue isn't listed here:

1. **Search existing issues:** [github.com/your-repo/anymanage/issues](https://github.com/your-repo/anymanage/issues)
2. **Ask the agent:** Describe your problem - it might be able to help diagnose
3. **Open a new issue:** Include:
   - What you were trying to do
   - What you expected to happen
   - What actually happened
   - Any error messages (exact text)
   - Your operating system and agent (Claude Code, opencode, codex)

---

*Last updated: 2026-01-25*
