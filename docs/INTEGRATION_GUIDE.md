# Integration Guide

Integrations connect your AI assistant to external tools you already use. They're completely optional - AnyManage works fully without them.

---

## What Integrations Do For You

When you connect a tool like Trello, your assistant can:

- **See your tasks** without you copying and pasting
- **Update status automatically** when you mark things complete locally
- **Keep everything in sync** so you don't have to update two places
- **Work offline** and catch up when you're back online

The assistant handles all the technical setup. You just say what you want to connect and provide your login when asked.

---

## The Dual Source of Truth

Here's why integrations are powerful: external tools and local files each do what they do best.

### What Goes Where

| External Tool (like Trello) | Your Local Files |
|----------------------------|------------------|
| Task status (done/blocked/active) | Full context and notes |
| Due dates | Background information |
| Assignments | Related files and links |
| Quick comments | Detailed discussion |
| Visual boards | Complete history |

**Why this works:** Your team sees status updates in Trello instantly. Your detailed notes, research, and context stay in your local files where they're always accessible - even offline.

### Example: Working with Trello

1. You mark a task complete in your local file
2. Your assistant syncs with Trello
3. The card moves to "Done" in Trello automatically
4. Your team sees the update without you touching Trello

It works the other way too - if someone moves a card in Trello, your local files update on the next sync.

---

## When Things Go Wrong

Integrations are designed to fail gracefully. If a connection drops:

1. **You'll see a notice:** "Trello unavailable - working with local data"
2. **Keep working normally** - your local files are always available
3. **It reconnects automatically** when the service is back
4. **You'll be prompted:** "Trello is back. Sync your changes?"

No data is lost. Everything important lives in your local files.

### Conflicts

Sometimes both you and a teammate change the same thing. When that happens:

- The external tool's version wins (your team's shared state)
- You're notified: "Conflict on [task]. Kept Trello version."
- Your local context and notes are preserved

This keeps things simple and predictable.

---

## Available Integrations

### Trello

Sync your tasks with Trello boards. See [ops/TASK_SYNC_PLAYBOOK.md](../ops/TASK_SYNC_PLAYBOOK.md) for daily workflow tips.

**To set up:** Say "Add Trello integration"

Your assistant will:
1. Check that prerequisites are ready
2. Walk you through getting your Trello credentials (with step-by-step guidance)
3. Set up the connection for you
4. Test that everything works

You'll never edit configuration files or run commands.

### Coming Soon

- **Calendar** - See upcoming deadlines and meetings
- **CRM** - Pull client information into entity profiles

---

## Adding an Integration

Just say:

> "Add Trello integration"

Or more generally:

> "Add integration"

Your assistant will show you what's available and guide you through setup.

### What You'll Need

- **An account** on the tool you're connecting (e.g., Trello account)
- **About 5 minutes** for the guided setup
- **Your login credentials** when prompted (the assistant will tell you exactly where to find them)

### Recommended Setup Methods

Some tools have simpler connection methods:

| Tool | Easiest Setup |
|------|---------------|
| Trello | "Add Trello integration" - guided credentials |
| Google Calendar | Claude Desktop connection (if using Claude Desktop) |
| GitHub | Claude Desktop connection (if using Claude Desktop) |

The assistant will recommend the simplest option for your setup.

---

## Removing an Integration

Say:

> "Remove Trello integration"

The connection is removed. Your local files are untouched.

---

## Checking Status

Say:

> "Check integrations"

You'll see which integrations are connected, when they last synced, and if there are any issues.

You can also check [INTEGRATIONS.md](../INTEGRATIONS.md) in your project root for a quick status view.

---

## Sync Commands

| Command | What It Does |
|---------|-------------|
| "Sync trello" | Full sync - pull and push changes |
| "Pull from trello" | Get updates from Trello only |
| "Push to trello" | Send local changes to Trello only |

---

## Rate Limits

External tools limit how fast you can sync. If you hit a limit:

- You'll see: "Rate limited. Try again in a few minutes."
- Your changes are queued, not lost
- Just wait and try again

This rarely happens with normal use.

---

## Privacy and Security

- **Your credentials are stored locally** in your agent's secure configuration
- **Nothing is logged** - API keys and tokens are never written to logs
- **Connections are direct** - your data goes straight to the tool, not through intermediaries

---

## Troubleshooting

### "Authentication failed"

Your credentials may have expired. Say "Add Trello integration" to reconnect.

### "Integration unavailable"

The external service might be down. Your local files work normally. Try again later.

### "Sync seems stuck"

Say "Check integrations" to see the current status. If issues persist, try removing and re-adding the integration.

### Need More Help?

See [docs/guides/troubleshooting.md](guides/troubleshooting.md) or file an issue on GitHub.

---

## Technical Notes (For Transparency)

Integrations use the Model Context Protocol (MCP), an open standard for connecting AI assistants to external tools. Your assistant uses only trusted, verified integration packages from:

- Official MCP Registry
- npm verified publishers
- Well-maintained open source projects

You don't need to understand MCP - the assistant handles all of this. This section is here so you know what's happening under the hood.

---

*For daily sync workflows, see [ops/TASK_SYNC_PLAYBOOK.md](../ops/TASK_SYNC_PLAYBOOK.md)*
