# Task Sync Playbook

Quick reference for syncing tasks with external tools like Trello.

---

## Quick Commands

| Say This | What It Does |
|----------|-------------|
| "Sync trello" | Full sync - pull and push changes |
| "Pull from trello" | Get updates from Trello only |
| "Push to trello" | Send local changes to Trello only |
| "Check integrations" | See connection status |

---

## Daily Workflow

### Morning

1. **Start your session** (integration status auto-checked)
2. **Sync:** "Sync trello" to get overnight updates
3. **Review any conflicts** that were resolved

### During Work

- **Mark tasks complete locally** - they'll sync to Trello
- **Add notes to tasks** - these stay local (that's the point!)
- **If "unavailable" message appears** - keep working, sync later

### End of Day

- **Optional:** "Sync trello" to push final updates
- **Or just close** - next session will catch up

---

## What Goes Where

Understanding this makes sync intuitive.

| Belongs in Trello | Belongs in Local Files |
|-------------------|------------------------|
| Task status (done/blocked) | Full context and notes |
| Due dates | Background information |
| Assignments | Related files and links |
| Quick comments | Detailed discussion |
| Visual board view | Complete history |

**Why?** Your team sees status in Trello. You keep rich context locally where it's always accessible.

---

## Common Scenarios

### You Worked Offline

You made changes without Trello connection.

**What to do:**
1. When back online: "Sync trello"
2. Your local changes push to Trello
3. Any Trello changes pull to local

**No data loss** - everything was saved locally.

---

### Team Made Changes in Trello

You didn't touch tasks, but teammates did.

**What to do:**
1. "Sync trello"
2. Their updates pull to your local files
3. Your files now reflect current status

---

### Conflict Detected

Both you and Trello changed the same task since last sync.

**What happens:**
1. System keeps Trello version (team's shared state)
2. You're notified: "Conflict on [task]. Kept Trello version: Done"
3. Your local context notes are preserved

**Why Trello wins:** Trello is where your team coordinates. Keeping the team's version prevents confusion.

---

### Trello Is Down

External service is unavailable.

**What happens:**
1. Message: "Trello unavailable - working with local data"
2. Keep working normally
3. When Trello recovers: "Trello is back. Sync pending changes?"

**No worries:** Everything important is in your local files.

---

### Rate Limited

Too many sync operations too fast.

**What happens:**
1. Message: "Rate limited. Try again in a few minutes."
2. Your changes are queued, not lost
3. Wait a bit, then sync again

**This is rare** with normal use. Only happens with bulk operations.

---

## Sync Details

### What Syncs Automatically

| From Trello → Local | From Local → Trello |
|---------------------|---------------------|
| Card status (list) | Task completion |
| Due dates | Status changes |
| Labels | Due date changes |
| Card descriptions | - |

### What Doesn't Sync

- **Trello comments** - stay in Trello (team communication)
- **Local notes** - stay local (your context)
- **Attachments** - stay in Trello
- **Member assignments** - stay in Trello

---

## List Mapping

How Trello lists map to task status:

| Trello List | Local Status |
|-------------|--------------|
| To Do, Backlog | `[ ]` Not started |
| In Progress, Doing | `[-]` In progress |
| Done, Complete | `[x]` Complete |

Custom list names? Run "Configure trello" to set up your mapping.

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| "Authentication failed" | "Add trello integration" to reconnect |
| "Rate limited" | Wait a few minutes, then sync again |
| Sync seems stuck | "Check integrations" to see status |
| Wrong board syncing | "Configure trello" to change board |
| Tasks not appearing | Make sure you're syncing the right board |

### Need More Help?

- See [docs/guides/troubleshooting.md](../docs/guides/troubleshooting.md)
- File an issue on GitHub

---

## Best Practices

1. **Sync at session start and end** - keeps things consistent
2. **Don't worry about conflicts** - external wins is predictable
3. **Keep detailed notes locally** - that's what local files are for
4. **Use Trello for status** - that's what it's good at
5. **Let sync happen automatically** - don't micro-manage it

---

## Integration Status Reference

Check `INTEGRATIONS.md` in your project root for current status, or say "Check integrations".

---

*For integration setup, see [docs/INTEGRATION_GUIDE.md](../docs/INTEGRATION_GUIDE.md)*
