# Claude Code Setup Tutorial

This guide walks you through setting up Agent PM with Claude Code. No terminal experience required - we'll explain every step.

---

## Before You Start

**What you need:**
- macOS, Windows, or Linux
- Claude Code installed (we'll cover this)
- About 10 minutes

**Using a different agent?** See [opencode setup](opencode-setup.md) or [codex setup](codex-setup.md) instead.

---

## Step 1: Install Claude Code

First, you need Claude Code on your computer. It's the AI assistant that will run Agent PM.

**Go to:** [https://claude.ai/code](https://claude.ai/code)

Follow the installation instructions for your operating system. The installer handles everything - just click through the prompts.

**How to know it worked:** After installation, you should be able to type `claude` in your terminal and see it respond.

Don't know how to open your terminal? That's the next step.

---

## Step 2: Open Your Terminal

The terminal is where you'll type commands. Think of it like a text-based file browser - instead of clicking folders, you type where you want to go.

**On macOS:**
1. Press `Cmd + Space` to open Spotlight
2. Type `Terminal`
3. Press `Enter`

**On Windows:**
1. Press the `Windows` key
2. Type `Command Prompt` or `PowerShell`
3. Press `Enter`

**On Linux:**
1. Press `Ctrl + Alt + T`
2. Or search for "Terminal" in your applications

**What you'll see:** A window with some text and a blinking cursor. Something like:

```
user@computer ~ %
```

This is the prompt. It's waiting for you to type a command.

The `~` symbol means you're in your home folder - like being in your main Documents area.

---

## Step 3: Download Agent PM

Now we'll download the Agent PM files to your computer. In terminal-speak, this is called "cloning a repository."

**Type this command** (copy and paste is fine):

```bash
git clone https://github.com/your-repo/agent-pm.git
```

**What happens:**
- Your computer downloads all the Agent PM files
- You'll see progress messages scrolling by
- Takes about 10-30 seconds depending on your internet

**What you'll see:**
```
Cloning into 'agent-pm'...
remote: Enumerating objects: 245, done.
remote: Counting objects: 100% (245/245), done.
Receiving objects: 100% (245/245), 85.12 KiB | 2.83 MiB/s, done.
```

**How to know it worked:** The prompt appears again (no error message), and you can see the files were downloaded.

---

## Step 4: Enter the Project Folder

Now we need to "go into" the folder you just downloaded. In terminal-speak, this is called "changing directory."

**Type:**

```bash
cd agent-pm
```

**What `cd` means:** "Change directory" - it's like double-clicking a folder to open it.

**How to know it worked:** Your prompt might change to show you're in the new folder:

```
user@computer agent-pm %
```

Or you can type `pwd` (print working directory) to confirm:

```bash
pwd
```

This shows your current location. It should end with `/agent-pm`.

---

## Step 5: Start Claude Code

Now for the moment of truth. Let's start Claude Code.

**Type:**

```bash
claude
```

**What happens:**
- Claude Code starts up
- It reads the INSTRUCTIONS.md file (the system's brain)
- It loads any existing project state
- Then it waits, ready for your instructions

**What you'll see:** Claude might show a brief loading message, then sit quietly. That's normal - it's designed to wait for you, not greet you.

If Claude says "No session state found. Want me to create STATE.md?" - type "yes". This creates a file to track your work across sessions.

---

## Step 6: Your First Command

Let's try something real. Ask Claude to create a new client.

**Type:**

```
Add new client Acme Corp
```

**What happens:**
1. Claude creates a folder: `entities/Acme Corp/`
2. Inside, it creates profile and roadmap files
3. It adds Acme Corp to the master dashboard
4. It confirms what it did

**What you'll see:**

```
Created Acme Corp:
- entities/Acme Corp/ENTITY_PROFILE.md
- entities/Acme Corp/ENTITY_ROADMAP.md

Next steps:
- Add facts: "Add CEO is Jane Smith to Acme's profile"
- Process notes: "Process notes for Acme"
- Check status: "Show Acme's status"
```

Congratulations! You just created your first client using natural language.

---

## Try These Next

Now that you're set up, try these commands:

**Add information:**
```
Add CEO is Jane Smith to Acme's profile
```

**See what you have:**
```
Show all clients
```

**Process meeting notes:**
```
Process notes for Acme
```
(Then paste your meeting notes when prompted)

**Get status:**
```
Show Acme's status
```

**Run a skill:**
```
What skills do you have?
```

---

## Common Hiccups

**"command not found: git"**
Git isn't installed. Download it from [git-scm.com](https://git-scm.com).

**"command not found: claude"**
Claude Code isn't installed or not in your path. Go back to Step 1.

**"fatal: repository not found"**
The URL might have changed. Check the Agent PM documentation for the current clone URL.

**Claude doesn't respond**
Press `Enter` after your command. If still stuck, press `Ctrl + C` to cancel and try again.

---

## Next Steps

Now that you're running:

- **[Getting Started Guide](getting-started.md)** - Understand how the system works
- **[Customization](customization.md)** - Adapt for your industry (software projects, patients, etc.)
- **[Troubleshooting](troubleshooting.md)** - Solutions to common issues

---

## Quick Reference

| What you want | What to type |
|---------------|--------------|
| Create client | "Add new client [name]" |
| Add info | "Add [fact] to [client]'s profile" |
| Process notes | "Process notes for [client]" |
| See status | "Show [client]'s status" |
| Weekly review | "Run weekly review" |
| Get help | "What can you do?" |

---

*Setup complete! You're ready to use Agent PM.*
