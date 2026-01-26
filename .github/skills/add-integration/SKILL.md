---
name: add-integration
description: Set up a new external tool integration
triggers:
  - "add integration"
  - "add [service] integration"
  - "connect [service]"
  - "set up [service]"
  - "integrate with [service]"
  - "connect to [service]"
---

# Add Integration

Walks users through connecting external tools. The agent handles all technical setup - users just provide credentials when prompted.

---

## Behavior

### Phase 1: Discovery

**If no service specified:**
```
What would you like to connect?

Available integrations:
- Trello - Sync tasks with Trello boards

Coming soon:
- Calendar - See deadlines and meetings
- CRM - Pull client information

Which one?
```

**If service specified:** Confirm and proceed to Phase 2.

---

### Phase 2: Prerequisite Check

Check that required tools are available:

1. **Node.js** - Required for most integrations

**If Node.js missing:**
```
You'll need Node.js installed for this integration.

On Mac: I can install it for you using Homebrew. Want me to do that?
On Windows: Download from nodejs.org/en/download

Let me know when you're ready to continue.
```

**If prerequisites met:** Proceed to Phase 3.

---

### Phase 3: Server Discovery

Search trusted sources for the integration server:

1. Official MCP Registry (registry.modelcontextprotocol.io)
2. Known good packages (maintained list)
3. npm verified publishers

**Present to user:**
```
Found Trello integration. This will let you:
- See your Trello tasks here
- Sync status automatically
- Work offline and catch up later

Ready to set it up?
```

---

### Phase 4: Credential Collection

Guide user through obtaining credentials step-by-step.

**For Trello:**
```
Let's get your Trello credentials. I'll walk you through it.

Step 1: Go to trello.com/power-ups/admin
Step 2: Click "New" to create a Power-Up (any name works, like "Agent PM")
Step 3: Copy your API Key from the page

Paste your API Key here:
```

After API key received:
```
Got it. Now we need a token.

Go to this link (I've added your API key):
https://trello.com/1/authorize?expiration=never&name=AgentPM&scope=read,write&response_type=token&key=[API_KEY]

Click "Allow" and copy the token shown.

Paste your token here:
```

---

### Phase 5: Installation

Install and configure the integration. User sees only progress messages.

**What agent does (silently):**
- Claude Code: `claude mcp add --transport stdio --env TRELLO_API_KEY=... --env TRELLO_TOKEN=... trello -- npx -y @delorenj/mcp-server-trello`
- opencode: Updates opencode.json mcp section
- Other agents: Appropriate configuration method

**What user sees:**
```
Setting up connection...
```

---

### Phase 6: Verification

Test the connection automatically.

**Success:**
```
Connected! Your Trello integration is ready.

Try "sync trello" to see your tasks.

Tip: The integration syncs automatically when you start a session.
```

**Failure:**
```
Couldn't connect to Trello. Let's check a few things:

1. Make sure your API key and token are correct
2. Check that your internet connection is working
3. Verify your Trello account has access to boards

Want to try again?
```

---

### Phase 7: Board Selection (Trello-specific)

For Trello, after successful connection:
```
Which Trello board should I sync with?

1. Marketing Tasks
2. Client Projects
3. Personal

(You can change this later with "configure trello")
```

Save selection to INTEGRATIONS.md.

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Prerequisites missing | Offer to help install, provide download link |
| Invalid credentials | "Those credentials didn't work. Let's try again from step 1." |
| Network error | "Couldn't connect. Check your internet and try again." |
| Service unavailable | "Trello seems to be down. Try again in a few minutes." |
| Unknown service | "I don't have an integration for [service] yet. Available: Trello" |

---

## Supported Integrations

| Service | MCP Server | Notes |
|---------|------------|-------|
| Trello | @delorenj/mcp-server-trello | Full CRUD, board sync |

**Coming soon:** Calendar, CRM

---

## Related

- [docs/INTEGRATION_GUIDE.md](../../../docs/INTEGRATION_GUIDE.md) - How integrations work
- [docs/protocols/integration-framework.md](../../../docs/protocols/integration-framework.md) - Protocol details
- [INTEGRATIONS.md](../../../INTEGRATIONS.md) - Current status
