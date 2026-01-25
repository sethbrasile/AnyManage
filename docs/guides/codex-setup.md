# Codex Setup Tutorial

This guide walks you through setting up Agent PM with OpenAI Codex. No terminal experience required - we'll explain every step.

**Codex is OpenAI's code-focused AI.** It's a good choice if you're already using OpenAI products.

---

## Before You Start

**What you need:**
- macOS, Windows, or Linux
- OpenAI API key (we'll get one)
- About 10 minutes

**Using a different agent?** See [Claude Code setup](claude-code-setup.md) or [opencode setup](opencode-setup.md) instead.

---

## Step 1: Get an OpenAI API Key

Codex needs an API key to work. If you don't have one:

1. Go to [platform.openai.com](https://platform.openai.com)
2. Sign up or log in
3. Navigate to API keys section
4. Create a new API key
5. Copy it somewhere safe (you won't see it again)

**Keep your API key private.** It's like a password.

---

## Step 2: Open Your Terminal

- **macOS:** Press `Cmd + Space`, type `Terminal`, press `Enter`
- **Windows:** Press `Windows` key, type `Command Prompt`, press `Enter`
- **Linux:** Press `Ctrl + Alt + T`

You'll see a window with a blinking cursor. That's where you'll type commands.

---

## Step 3: Install Codex

The installation method depends on how OpenAI packages Codex for command-line use.

**Try:**

```bash
npm install -g @openai/codex
```

**Or:**

```bash
pip install openai-codex
```

> **Note:** Check [OpenAI's documentation](https://platform.openai.com/docs) for the current installation command. These may change.

**How to know it worked:**

```bash
codex --version
```

You should see a version number.

---

## Step 4: Set Your API Key

Codex needs your API key. Set it as an environment variable.

**macOS/Linux:**

```bash
export OPENAI_API_KEY='your-key-here'
```

**Windows Command Prompt:**

```cmd
set OPENAI_API_KEY=your-key-here
```

**Windows PowerShell:**

```powershell
$env:OPENAI_API_KEY="your-key-here"
```

**Make it permanent** (so you don't retype every session):

- **macOS/Linux:** Add the export line to your `~/.bashrc` or `~/.zshrc` file
- **Windows:** Set it in System Properties > Environment Variables

---

## Step 5: Download Agent PM

Download the Agent PM files.

**Type:**

```bash
git clone https://github.com/your-repo/agent-pm.git
```

**What happens:** Your computer downloads all the project files.

**If you see "command not found: git":** Install git from [git-scm.com](https://git-scm.com).

---

## Step 6: Enter the Project Folder

Move into the downloaded folder.

**Type:**

```bash
cd agent-pm
```

**Verify:**

```bash
pwd
```

Should end with `/agent-pm`.

---

## Step 7: Start Codex

Launch the agent.

**Type:**

```bash
codex
```

If Codex has a different command to start interactive mode, use that instead. Check OpenAI's documentation for the exact command.

**What happens:** Codex loads the INSTRUCTIONS.md file and waits for your commands.

---

## Step 8: Your First Command

Test it out.

**Type:**

```
Add new client Acme Corp
```

**Expected result:**

```
Created Acme Corp:
- entities/Acme Corp/ENTITY_PROFILE.md
- entities/Acme Corp/ENTITY_ROADMAP.md
```

You've created your first client.

---

## Codex-Specific Notes

**Model Selection:** Codex might offer different model options. For Agent PM:
- Use the most capable model available
- Code-focused models work well
- GPT-4 variants recommended for complex reasoning

**Known Differences:**
- Response style may differ from Claude Code
- Some edge cases might behave differently
- Token limits vary by model

These are minor. Core Agent PM functionality works the same.

**Rate Limits:** OpenAI has API rate limits. If you hit them:
- Wait a few seconds between commands
- Consider upgrading your API tier
- Batch operations help reduce calls

---

## Try These Next

```
Add CEO is Jane Smith to Acme's profile
```

```
Show all clients
```

```
Process notes for Acme
```

```
What skills do you have?
```

---

## Troubleshooting

**"command not found: codex"**
Installation didn't complete. Try:
1. Close and reopen your terminal
2. Run install again
3. Check OpenAI docs for current command

**"Invalid API key" or "401 Unauthorized"**
Your API key isn't set correctly. Make sure:
1. You copied the full key
2. No extra spaces
3. The environment variable is set

**"Rate limit exceeded"**
You've made too many requests. Wait a minute and try again.

**"Model not found"**
You might not have access to the requested model. Check your OpenAI account for available models.

---

## Next Steps

- **[Getting Started Guide](getting-started.md)** - How the system works
- **[Customization](customization.md)** - Adapt for your industry
- **[Troubleshooting](troubleshooting.md)** - More help

---

*Setup complete! You're ready to use Agent PM with Codex.*
