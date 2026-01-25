# opencode Setup Tutorial

This guide walks you through setting up Agent PM with opencode. No terminal experience required - we'll explain every step.

**opencode is open source and free.** It's a great choice if you want a self-hosted or community-driven option.

---

## Before You Start

**What you need:**
- macOS, Windows, or Linux
- Node.js or Python installed (we'll check)
- About 10 minutes

**Using a different agent?** See [Claude Code setup](claude-code-setup.md) or [codex setup](codex-setup.md) instead.

---

## Step 1: Check Prerequisites

opencode needs either Node.js or Python to run. Let's check if you have them.

**Open your terminal first:**

- **macOS:** Press `Cmd + Space`, type `Terminal`, press `Enter`
- **Windows:** Press `Windows` key, type `Command Prompt`, press `Enter`
- **Linux:** Press `Ctrl + Alt + T`

**Check for Node.js:**

```bash
node --version
```

If you see a version number (like `v18.17.0`), you're good. If you see "command not found," you'll need to install Node.js from [nodejs.org](https://nodejs.org).

**Or check for Python:**

```bash
python --version
```

If you see a version (like `Python 3.11.4`), you're good. If not, get Python from [python.org](https://python.org).

---

## Step 2: Install opencode

Now install the opencode agent. The command depends on whether you have Node.js or Python.

**If you have Node.js:**

```bash
npm install -g opencode
```

**If you have Python:**

```bash
pip install opencode
```

**What happens:** Your computer downloads the opencode software and sets it up so you can run it from anywhere.

**How to know it worked:**

```bash
opencode --version
```

You should see a version number. If you see "command not found," the installation didn't complete. Try closing and reopening your terminal.

> **Note:** Installation commands may change. Check [opencode.ai](https://opencode.ai) for the latest instructions if the above doesn't work.

---

## Step 3: Download Agent PM

Now we'll download the Agent PM files. In terminal-speak, this is called "cloning a repository."

**Type:**

```bash
git clone https://github.com/your-repo/agent-pm.git
```

**What happens:**
- Your computer downloads all the Agent PM files
- You'll see progress messages
- Takes 10-30 seconds

**If you see "command not found: git":** You need to install git. Download it from [git-scm.com](https://git-scm.com).

**How to know it worked:** The prompt comes back without error messages.

---

## Step 4: Enter the Project Folder

Move into the folder you just downloaded.

**Type:**

```bash
cd agent-pm
```

**What `cd` means:** "Change directory" - like double-clicking a folder to open it.

**Verify you're in the right place:**

```bash
pwd
```

This shows your current location. It should end with `/agent-pm`.

---

## Step 5: Start opencode

Let's start the agent.

**Type:**

```bash
opencode
```

**What happens:**
- opencode starts and loads the INSTRUCTIONS.md file
- It may take a moment to initialize
- Then it waits for your commands

If opencode asks about API keys or configuration, follow its prompts. You may need to provide an API key from your LLM provider (OpenAI, Anthropic, etc.).

---

## Step 6: Your First Command

Test it out with a real command.

**Type:**

```
Add new client Acme Corp
```

**What you'll see:**

```
Created Acme Corp:
- entities/Acme Corp/ENTITY_PROFILE.md
- entities/Acme Corp/ENTITY_ROADMAP.md

Next steps:
- Add facts: "Add CEO is Jane Smith to Acme's profile"
- Process notes: "Process notes for Acme"
```

You've created your first client.

---

## opencode-Specific Notes

**API Configuration:** opencode typically requires an API key for your LLM backend. Check opencode's documentation for setting up:
- OpenAI API keys
- Anthropic API keys
- Local model connections

**Known Differences from Claude Code:**
- File watching behavior may differ
- Some advanced features may not be available
- Response formatting might look slightly different

These differences are minor. The core Agent PM functionality works the same way.

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

**"command not found: opencode"**
The installation didn't complete. Try:
1. Close and reopen your terminal
2. Run the install command again
3. Check opencode's official docs

**"API key not found" or "Unauthorized"**
You need to configure your LLM API key. Check opencode's documentation for setting environment variables.

**"Error: INSTRUCTIONS.md not found"**
Make sure you're in the agent-pm folder. Type `pwd` to check, then `cd agent-pm` if needed.

**Commands work but output is different**
Different LLMs have different personalities. The core functionality should work, but phrasing may vary.

---

## Next Steps

- **[Getting Started Guide](getting-started.md)** - How the system works
- **[Customization](customization.md)** - Adapt for your industry
- **[Troubleshooting](troubleshooting.md)** - More help

---

*Setup complete! You're ready to use Agent PM with opencode.*
