# Getting Started with Agent PM

This guide explains what Agent PM is and how it works. For specific setup instructions, see the agent-specific guides:

- [Claude Code setup](claude-code-setup.md)
- [opencode setup](opencode-setup.md)
- [codex setup](codex-setup.md)

## What Is This?

Agent PM is an AI-powered system that helps you manage clients, projects, or whatever you're tracking. You talk to it in plain English, and it handles all the file organization and technical details.

Think of it like having an assistant who:
- Creates folders and documents when you add a new client
- Extracts tasks from your meeting notes
- Keeps track of what's done and what's pending
- Generates reports and assessments
- Remembers everything about every client

The "AI" part is the coding agent (Claude Code, opencode, or codex) that reads the instructions and does the work. The "PM" part is the file structure and templates that tell the AI what to do.

## How Does It Work?

Three things work together:

1. **Files as data** - Everything is stored in markdown files. Client profiles, roadmaps, notes - all in folders you can read and edit directly if you want.

2. **AI as processor** - The coding agent reads your instructions and makes changes to the files. It creates folders, updates documents, extracts information from notes.

3. **Natural language as interface** - You just type what you want. "Add new client Acme" or "Process notes for Beta" or "Show me what's happening with Gamma."

You never need to learn commands or syntax. The AI figures out what you mean.

## File Structure

When you start, you'll have these folders:

```
agent-pm/
  entities/           <- Your clients/projects live here
  templates/          <- Document templates for new entities
  ops/                <- Team operations and logs
  docs/               <- Documentation (you're reading this)
  CONFIG.md           <- Settings (entity type, etc.)
  ROADMAP.md          <- Master dashboard
  STATE.md            <- Current session state
  INSTRUCTIONS.md     <- AI instructions (you don't need to touch this)
```

### The entities/ folder

This is where your actual work lives. When you add a client, it creates:

```
entities/Acme Corp/
  ENTITY_PROFILE.md   <- Everything about this client
  ENTITY_ROADMAP.md   <- Tasks and progress
  notes/              <- Meeting notes, communications
  notes/archive/      <- Processed notes
  knowledge/          <- Learned context from specialists
  deliverables/       <- Assessment reports, strategy docs
  internal/           <- Internal-only content (not shared)
```

### The templates/ folder

These are the starting points for new documents. When you add a client, the AI copies these templates and fills in the name. You can customize templates to match your industry.

### The ops/ folder

Team-level stuff: playbooks, voice profiles, logs. This isn't client-specific - it's how your whole operation works.

## What Happens When You Start

When you open a coding agent in the agent-pm folder, it:

1. Reads INSTRUCTIONS.md to understand how to behave
2. Loads your current state (if you were working on something)
3. Waits silently for you to give instructions

There's no greeting or status dump. It's ready when you are.

## First Things to Try

Once you're set up (see agent-specific guides), try these:

**Add your first client:**
```
Add new client Acme Corp
```
The AI creates the folder, sets up profile and roadmap, adds them to your dashboard.

**See their profile:**
```
Show me Acme's profile
```

**Add some details:**
```
Add CEO is John Smith to Acme's profile
```

**Check your dashboard:**
```
Show status
```

**Process meeting notes:**
If you have notes, paste them or put them in the client's notes/ folder, then:
```
Process notes for Acme
```
The AI extracts tasks, profile facts, and follow-ups.

## What's an "Entity"?

Throughout the docs, you'll see "entity" instead of "client". That's because this system can manage anything:

- **Clients** (default) - Marketing agencies, consultancies
- **Projects** - Software teams, construction
- **Products** - Product managers
- **Patients** - Healthcare
- **Engagements** - Consulting firms

You configure what you're managing in CONFIG.md. The default is "client" but you can change it to match your industry. See the [customization guide](customization.md) for details.

## Getting Help

If something doesn't work as expected:

1. Ask the AI: "What went wrong?" or "Help"
2. Check the [troubleshooting guide](troubleshooting.md)
3. Open an issue on GitHub

The AI is designed to explain problems in plain English and suggest fixes.

---

**Next:** Set up your specific agent:
- [Claude Code setup](claude-code-setup.md)
- [opencode setup](opencode-setup.md)
- [codex setup](codex-setup.md)

Or explore more:
- [Customization](customization.md) - Adapt for your industry
- [Skill discovery](skill-discovery.md) - See all available workflows
