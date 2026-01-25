# Phase 7: Documentation and Onboarding - Research

**Researched:** 2026-01-25
**Domain:** Technical documentation, beginner tutorials, CLI onboarding, video production
**Confidence:** HIGH

## Summary

Phase 7 creates documentation that enables non-technical users to set up and operate Agent PM without external help. Research focused on four domains: beginner-friendly README structure, terminal/CLI tutorials for complete beginners, video tutorial script writing, and troubleshooting guide formats. The system has rich internal documentation (15 protocol files, 9 templates, 2 specialists, 3 skills) but lacks user-facing guides and a clear entry point.

**Key findings:**
- Progressive disclosure is the dominant pattern in modern documentation (show essential first, advanced on request)
- Beginner CLI tutorials succeed when they assume zero knowledge and explain every keystroke
- Video scripts for technical content require A/V (Audio/Visual) format pairing narration with screen actions
- Troubleshooting guides work best as scannable FAQs with visual aids and escalation paths
- Reference examples are more effective than abstract explanations for non-technical users

**Current state:**
- Strong foundation: INSTRUCTIONS.md, 15 detailed protocols, complete skill/specialist systems
- No README.md (critical gap - no entry point)
- No docs/guides/ directory (all docs are for agents, not users)
- No reference example implementation (marketing agency mentioned in PROJECT.md but not built)
- Voice training system complete (Phase 6) providing voice for video scripts

**Primary recommendation:** Build documentation in progressive layers following the "one clear first step" principle. Create README.md as single entry point, separate tutorials per agent platform (Claude Code, opencode, codex), include working reference example with realistic fake data, and structure content to support YouTube video series while maintaining standalone utility.

## Standard Stack

### Documentation Format
| Format | Purpose | Why Standard |
|--------|---------|--------------|
| Markdown | All documentation | Universal, git-friendly, readable in terminal and web, no build step required |
| README.md | Single entry point | GitHub/GitLab convention, first file users see, always displayed on repo page |
| docs/ structure | Organization | Standard across open source, clear hierarchy, expected location |

### Tutorial Writing Tools
| Tool | Use Case | Why Standard |
|------|----------|--------------|
| Plain Markdown | Step-by-step guides | Accessible, copyable, works offline, no special tooling needed |
| Code blocks with syntax highlighting | Command examples | GitHub renders with syntax highlighting, clearly distinguishes commands from prose |
| Screenshots/GIFs (optional) | Visual confirmation | Industry standard for CLI tutorials, helps users verify they're on track |

### Video Script Format
| Format | Purpose | Best Practice |
|--------|---------|--------------|
| A/V Script (Audio/Visual) | Technical walkthroughs | Two-column format: left=visual (screen actions), right=audio (narration) |
| VIDEO_SCRIPT.md | Markdown-based scripts | Same git workflow as docs, reviewable, version controlled |

**Installation:**
No installation required - all standard markdown, no build dependencies.

## Architecture Patterns

### Recommended Documentation Structure

```
agent-pm/
├── README.md                          # ENTRY POINT (one clear first step)
├── docs/
│   ├── guides/                        # User-facing tutorials
│   │   ├── getting-started.md         # Universal first steps
│   │   ├── claude-code-setup.md       # Claude Code specific
│   │   ├── opencode-setup.md          # opencode specific
│   │   ├── codex-setup.md             # codex specific
│   │   ├── customization.md           # How to adapt for your industry
│   │   └── troubleshooting.md         # FAQ format
│   ├── protocols/                     # Agent instructions (already exists)
│   └── reference-example/             # Working marketing agency demo
│       ├── README.md                  # What this example demonstrates
│       ├── entities/                  # Sample clients with fake data
│       └── ops/                       # Sample operational files
├── templates/                         # Already exists
├── VIDEO_SCRIPTS/                     # One-time deliverable for YouTube
│   ├── 01-intro-demo.md              # Overview + what to expect
│   ├── 02-setup-detailed.md          # Installation walkthrough
│   ├── 03-first-client.md            # Adding your first entity
│   └── 04-voice-training.md          # Optional advanced feature
└── INSTRUCTIONS.md                    # Already exists
```

### Pattern 1: Progressive Disclosure for Beginners

**What:** Show users only essential information initially, reveal advanced features on request.

**When to use:** All beginner-facing documentation, especially README and getting-started guide.

**Example structure:**
```markdown
# Agent PM

**For:** Non-technical project managers who want AI assistance managing clients/projects/etc.

## What You'll Get

In 5 minutes, you'll have a system where you can:
- Add a client by saying "Add new client Acme Corp"
- Process meeting notes by saying "Process notes for Acme"
- Get status by saying "Show me Acme's status"

The AI handles all the file organization, git commits, and technical details.

## Quick Start

**One command to start:**
```bash
git clone <repo-url> agent-pm
cd agent-pm
claude
```

That's it. Claude Code will start and load your system.

---

**Want more?** See [detailed setup guides](docs/guides/) for your specific agent.

**Need customization?** See [customization guide](docs/guides/customization.md).

**Having issues?** Check [troubleshooting](docs/guides/troubleshooting.md).
```

**Why it works:**
- Beginner sees exactly one thing to do first
- Success happens quickly (5 minutes, not 30)
- Advanced details available but not overwhelming
- Each "see more" link is specific, not generic

**Source:** [Progressive Disclosure - NN/G](https://www.nngroup.com/articles/progressive-disclosure/), [Progressive Disclosure Matters: Applying 90s UX Wisdom to 2026 AI Agents](https://aipositive.substack.com/p/progressive-disclosure-matters)

### Pattern 2: Agent-Specific Tutorials

**What:** Separate tutorial for each agent platform (Claude Code, opencode, codex) instead of one generic guide.

**When to use:** Setup instructions where agent-specific differences matter.

**Why separate tutorials:**
- Each agent has different installation process
- Different capability tiers (Claude Code has more features than opencode in terminal-only mode)
- Different user bases with different expectations
- Reduces cognitive load (user doesn't parse "if you're using X, do Y; if Z, do A")

**Common structure all tutorials share:**
```markdown
# Getting Started with [Agent Name]

## Prerequisites
- [List what user needs BEFORE starting]
- Example: macOS, Windows, or Linux
- Example: Terminal comfort level: none required

## Installation
[Step-by-step specific to this agent]

## First Session
[What happens when you start]

## Your First Command
[One simple thing to try]

## Next Steps
[Links to common workflows]
```

**Source:** Phase 6 CONTEXT.md locked decision on separate tutorials per agent.

### Pattern 3: Conversational Tutorial Tone

**What:** Write tutorials like you're explaining to a friend, not writing a manual.

**When to use:** All user-facing guides (not agent protocols).

**Techniques:**
- Use "you" and "your" (direct address)
- Explain why, not just what: "This creates a folder because the AI needs somewhere to store client files"
- Use analogies: "Think of entities like folders in a filing cabinet - each client gets their own"
- Acknowledge confusion: "This might seem weird at first, but..."
- Celebrate small wins: "Nice! You just created your first client."

**Example transformation:**

**Technical (avoid):**
```
Execute the entity creation command with the client name parameter.
The system will instantiate a directory structure and populate templates.
```

**Conversational (use):**
```
Tell the AI to add a client: "Add new client Acme Corp"

The AI will create a folder for Acme, set up their files, and confirm it's done.
```

**Source:** Phase 6 CONTEXT.md locked decision on "conversational guide tone", [How to Write Technical Documentation in 2025](https://dev.to/auden/how-to-write-technical-documentation-in-2025-a-step-by-step-guide-1hh1)

### Pattern 4: Reference Example with Progressive Layers

**What:** Working example that starts minimal and shows "before/after" states for each workflow.

**When to use:** Demonstrating the system to new users who learn by example.

**Structure:**
```
docs/reference-example/
├── README.md                  # What this example shows
├── 00-initial-state/          # Bare system (just cloned)
├── 01-first-client/           # After adding one client
├── 02-with-notes/             # After processing notes
├── 03-with-specialists/       # After running assessment
└── 04-full-workflow/          # Complete realistic state
```

Each layer includes:
- Full file tree snapshot
- Explanation of what changed
- The exact commands user ran
- Why this matters

**Rationale:** Users can compare states and understand what each command actually does.

**Source:** Phase 6 CONTEXT.md locked decision on "progressive layers approach".

### Pattern 5: Video Script A/V Format

**What:** Two-column script pairing audio (narration) with visual (screen actions).

**When to use:** Creating YouTube walkthrough videos.

**Format:**
```markdown
## Section: Adding Your First Client

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal with prompt ready | "Now let's add your first client." |
| TYPE: `Add new client Acme Corp` | "Just type: Add new client Acme Corp" |
| SHOW: AI response confirming creation | "The AI creates everything you need and confirms it's done." |
| ZOOM: Highlight the suggestion "Want to add details?" | "Notice it suggests next steps. You can add details now or later." |

**Production notes:**
- Keep terminal text large (16pt+ font)
- Pause 2 seconds after each AI response
- Use mouse highlight for key phrases
```

**Rationale:**
- Script + actions stay synchronized
- Editor/reviewer sees exactly what happens on screen
- Production notes ensure consistent visual style

**Source:** [Writing Instructional Video Scripts: Eight Top Tips](https://altuent.com/insights/writing-instructional-video-scripts-eight-top-tips/), [How to Write a Script for a Video Tutorial](https://wowto.ai/blog/how-to-write-a-script-for-a-video-tutorial)

### Pattern 6: FAQ-Format Troubleshooting

**What:** Troubleshooting guide structured as scannable questions users actually ask.

**When to use:** Common problems documentation.

**Structure:**
```markdown
# Troubleshooting

## Installation Issues

### "Command not found: claude"
**What this means:** Claude Code isn't installed or not in your PATH.

**How to fix:**
1. Verify installation: `which claude`
2. If not found, install: [link to installation]
3. Restart terminal

**Still stuck?** [Open an issue](github-issues-link)

### "Permission denied"
[Same structure]
```

**Key elements:**
- Question phrased as user would ask it
- Plain English explanation of what went wrong
- Numbered steps to fix (not prose)
- Escalation path if solution doesn't work
- Visual aids (screenshots) for multi-step fixes

**Source:** [How to Create a Troubleshooting Guide](https://scribe.com/library/troubleshooting-guide), [Build Troubleshooting Guides That Empower Customer Service Teams](https://knowmax.ai/blog/troubleshooting-guides-for-customer-service/)

## Don't Hand-Roll

Problems that already have solutions - don't build custom implementations:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Markdown rendering | Custom parser | GitHub/GitLab built-in rendering | Free, tested, zero config |
| Video editing | Custom tools | Standard video editor (Final Cut, Premiere) | Voice scripts are one-time deliverable, not worth tooling |
| Tutorial hosting | Custom site | GitHub Pages / README.md | Users expect docs in repo, not external site |
| Screenshot capture | Manual screenshots | Built-in OS tools (Cmd+Shift+4 on Mac) | Simple, no dependencies |
| Code syntax highlighting | Custom highlighter | GitHub Flavored Markdown | Already built-in, supports all languages |

**Key insight:** Agent PM is developer-focused and lives on GitHub. Use GitHub's conventions and built-in features instead of reinventing documentation infrastructure.

## Common Pitfalls

### Pitfall 1: No Clear First Step

**What goes wrong:** README has sections for Features, Installation, Usage, Configuration, Advanced Topics. User doesn't know what to do first.

**Why it happens:** Documentation written from maintainer perspective (explaining what exists) not user perspective (what do I do right now).

**How to avoid:**
- Start README with one numbered step: "1. Clone this repo"
- Make it executable immediately (not "read these docs first")
- Move features/background to "Why use this?" section AFTER getting started

**Warning signs:**
- README starts with "About" or "Features" section
- First heading is "Introduction"
- No command appears in first 200 words
- "Installation" comes after "Features"

**Source:** [How to Create the Perfect README for Your Open Source Project](https://dev.to/github/how-to-create-the-perfect-readme-for-your-open-source-project-1k69), Phase 7 CONTEXT.md success criteria "README.md has single clear first step"

### Pitfall 2: Terminal Tutorials Assume Too Much

**What goes wrong:** Tutorial says "Open your terminal" without explaining what a terminal is or how to find it.

**Why it happens:** Writer has terminal open all day, forgets beginners don't.

**How to avoid:**
- Assume user has NEVER used terminal
- Include screenshot of where to find terminal (macOS Spotlight, Windows search)
- Explain what `cd` means before using it
- Show full paths, not relative: `/Users/yourname/agent-pm` not `~/agent-pm`
- Explain what happens when a command succeeds (silence? confirmation message?)

**Warning signs:**
- Uses `~` without explaining it
- Uses `cd` without showing `pwd`
- Assumes user knows what "clone" means
- No explanation of what command output means

**Source:** [Command Line for Beginners – How to Use the Terminal Like a Pro](https://www.freecodecamp.org/news/command-line-for-beginners/), [Ubuntu's Linux Command Line for Beginners](https://ubuntu.com/tutorials/command-line-for-beginners)

### Pitfall 3: Video Scripts Without Seth's Voice

**What goes wrong:** Video script sounds like corporate training video, not like Seth wrote it.

**Why it happens:** Writer defaults to formal "professional" narration style.

**How to avoid:**
- Review `.planning/my_voice_example/seth_voice_profile.md` before writing
- Use contractions: "you'll" not "you will"
- Use parenthetical asides: "(this might take a minute)"
- Use "lol" if something is genuinely funny/absurd
- Avoid: "Let's begin", "Now we will", "Please note that"
- Use: "Let's add a client", "Here's what happens", "Important:"

**Warning signs:**
- Script uses "Additionally," "Furthermore," "Moreover"
- Closing is "Thank you for watching" (Seth doesn't say this)
- No personality - could be any narrator
- Overly enthusiastic: "Wonderful!" "Fantastic!"

**Source:** Phase 7 CONTEXT.md decision on using voice heavily from `.planning/my_voice_example/`, Phase 6 complete voice training system

### Pitfall 4: Troubleshooting Guide as Prose

**What goes wrong:** Troubleshooting section is paragraphs of explanation instead of scannable Q&A.

**Why it happens:** Writing documentation like an essay instead of a reference tool.

**How to avoid:**
- Structure as FAQ: question → answer → solution steps
- Use numbered steps for solutions (not paragraphs)
- Include "Still stuck?" escalation path
- Add screenshots for complex fixes
- Test every solution on clean system

**Warning signs:**
- No questions, just topics
- Solutions are prose paragraphs
- No numbered steps
- Missing escalation path
- Written but not tested

**Source:** [What Is a Troubleshooting Guide and How to Write One](https://www.archbee.com/blog/troubleshooting-guide)

### Pitfall 5: Reference Example Too Perfect

**What goes wrong:** Example shows idealized perfect state, not realistic workflow progression.

**Why it happens:** Writer creates final state only, skips intermediate steps.

**How to avoid:**
- Show incomplete states: "Here's what it looks like with just one client"
- Include realistic fake data (not "Lorem Ipsum Client #1")
- Show before/after for each workflow
- Include mistakes/corrections: "Notice the typo got fixed when I updated the profile"

**Warning signs:**
- Example shows 10 perfect clients with complete data
- No progression - just final state
- Data is obviously fake (Client A, Client B, Client C)
- No mistakes or realistic edits

**Source:** Phase 7 CONTEXT.md decision on "include generated deliverables so users see specialist outputs"

### Pitfall 6: Jargon Without Definitions

**What goes wrong:** Documentation uses terms like "entity," "specialist," "skill" without explaining what they mean.

**Why it happens:** Writer is immersed in the system, forgets these aren't universal terms.

**How to avoid:**
- Define custom terms on first use
- Link to glossary for complex concepts
- Use plain English alternatives when possible: "client" not "entity instance"
- Test docs on someone unfamiliar with the system

**Warning signs:**
- Uses system-specific terms in README without definition
- Assumes user knows what a "specialist" is
- Links to detailed docs without summary explanation

**Source:** [10 Essential Technical Documentation Best Practices for 2026](https://www.documind.chat/blog/technical-documentation-best-practices)

## Code Examples

### Example 1: Progressive README Structure

```markdown
# Agent PM

**AI project management for non-technical users.**

Tell it what to do in plain English. It handles the files, git, and technical details.

## Quick Start

**One command:**
```bash
git clone <repo> agent-pm && cd agent-pm && claude
```

Type: `Add new client Acme Corp`

The AI creates everything and confirms it's done.

---

**That's it!** You're running.

### What You Can Do

- Add clients: "Add new client [name]"
- Process notes: "Process notes for [client]"
- Get status: "Show me [client]'s status"
- Run assessments: "Run assessment for [client]"

### Next Steps

- [Detailed setup guide](docs/guides/getting-started.md) - Step-by-step walkthrough
- [Customization](docs/guides/customization.md) - Adapt for your industry
- [Troubleshooting](docs/guides/troubleshooting.md) - Common issues

### How It Works

Agent PM is a file structure + AI instructions. You talk to the AI naturally, and it:
- Creates folders for clients
- Organizes notes and documents
- Tracks work and status
- Generates reports and assessments

All changes are tracked in git automatically (you never see git commands).

### Requirements

- macOS, Windows, or Linux
- One of: Claude Code, opencode, or codex
- No technical background required

### What's Included

- Ready-to-use system (no configuration needed)
- Skill library (process notes, get status, weekly reviews)
- Specialist agents (assessments, strategy planning)
- Optional voice training (make the AI write like you)

### Learn More

- [Reference example](docs/reference-example/) - See it in action
- [Video tutorials](VIDEO_SCRIPTS/) - Watch setup walkthrough
- [Customization guide](docs/guides/customization.md) - Adapt for your needs
```

**Source:** Derived from [How to Create the Perfect README](https://dev.to/github/how-to-create-the-perfect-readme-for-your-open-source-project-1k69), [Beginner-Friendly README Guide](https://www.readmecodegen.com/blog/beginner-friendly-readme-guide-open-source-projects)

### Example 2: Agent-Specific Tutorial Pattern

```markdown
# Getting Started with Claude Code

## Before You Start

**You need:**
- macOS, Windows, or Linux
- Claude Code installed ([installation guide](https://claude.ai/code))

**Don't have Claude Code?**
- [Try opencode instead](opencode-setup.md) (open source, free)
- [Try codex instead](codex-setup.md) (OpenAI's agent)

**Terminal experience?** None required - we'll show every step.

## Step 1: Clone Agent PM

Open your terminal:
- **macOS:** Press Cmd+Space, type "Terminal", press Enter
- **Windows:** Press Windows key, type "Command Prompt", press Enter
- **Linux:** Press Ctrl+Alt+T

You'll see a prompt like this:
```
yourname@computer ~ %
```

Copy and paste this command:
```bash
git clone https://github.com/your-repo/agent-pm.git
```

Press Enter. You'll see text scrolling - that's git downloading files.

When it finishes, you'll see your prompt again.

## Step 2: Open the Project

Type:
```bash
cd agent-pm
```

Press Enter.

**What this does:** "cd" means "change directory" - you're moving into the agent-pm folder.

## Step 3: Start Claude Code

Type:
```bash
claude
```

Press Enter.

**What happens:** Claude Code starts and loads the Agent PM instructions. You'll see a welcome message.

## Step 4: Add Your First Client

At the Claude prompt, type:
```
Add new client Acme Corp
```

Press Enter.

**What happens:**
- Claude creates a folder: `entities/Acme Corp/`
- Creates profile and roadmap files
- Confirms: "Created Acme Corp with profile and roadmap."
- Suggests next steps

**That's it!** You just created your first client.

## Next Steps

Try these commands:
- "Show me Acme's profile" - See the client file
- "Add CEO is Jane Smith to Acme's profile" - Update details
- "Process notes for Acme" - Extract tasks from meeting notes

**Want more?**
- [Common workflows](../guides/common-workflows.md)
- [Voice training](../guides/voice-training.md) - Make AI write like you
- [Troubleshooting](../guides/troubleshooting.md) - If something went wrong
```

**Source:** Derived from [Ubuntu Command Line for Beginners](https://ubuntu.com/tutorials/command-line-for-beginners), [Command Line for Beginners](https://www.freecodecamp.org/news/command-line-for-beginners/)

### Example 3: Video Script with Seth's Voice

```markdown
# Video Script: Setting Up Agent PM

**Target length:** 5-7 minutes
**Audience:** Non-technical project managers
**Tone:** Conversational, Seth's voice (see `.planning/my_voice_example/`)

---

## Section 1: Intro (30 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Title card "Agent PM Setup" | "Quick setup walkthrough - you'll be running in about 5 minutes." |
| SHOW: Desktop with terminal icon highlighted | "We're going to open the terminal (don't worry, I'll show you where it is)..." |
| SHOW: Mouse hovering over terminal | "...clone the project, and start the AI." |

**Production notes:**
- Keep title card simple (no animations)
- Use system default desktop (not customized)
- Mouse movement slow and deliberate

---

## Section 2: Opening Terminal (45 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: macOS desktop | "On Mac, press Command-Space to open Spotlight." |
| TYPE: "Terminal" in Spotlight | "Type 'Terminal' and press Enter." |
| SHOW: Terminal window opening | "This is your terminal. Looks a bit retro lol, but it's super powerful." |
| ZOOM: Show the prompt `user@computer ~ %` | "This prompt is where we'll type commands. It's waiting for you to tell it what to do." |

**Production notes:**
- Pause 2 seconds after terminal opens
- Zoom in on prompt (make text visible)
- Font size: 16pt minimum

---

## Section 3: Cloning the Project (60 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal with prompt ready | "First command: clone the project from GitHub." |
| TYPE: `git clone https://github.com/user/agent-pm.git` | "Copy this command - I've got it in the description below." |
| SHOW: Output scrolling | "It's downloading all the files. This takes maybe 30 seconds depending on your internet." |
| SHOW: Prompt returns | "When you see your prompt again, it's done." |
| TYPE: `cd agent-pm` | "Now move into the folder: c-d space agent-pm." |
| SHOW: Prompt changes to show `agent-pm` | "Notice the prompt now shows 'agent-pm' - that means you're inside the project." |

**Production notes:**
- Display command in description/pinned comment
- Show real download speed (don't speed up)
- Pause 1 second before next command

---

## Section 4: Starting Claude Code (45 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal prompt in agent-pm folder | "Now start Claude Code. Just type: claude" |
| TYPE: `claude` | (pause while typing) |
| SHOW: Claude Code loading | "Claude Code loads the instructions and gets ready." |
| SHOW: Claude Code prompt | "When you see this, you're ready to go." |

**Production notes:**
- Show loading time realistically
- Don't skip the initialization output

---

## Section 5: Adding First Client (90 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Claude Code prompt | "Let's add a client. Watch how natural this is." |
| TYPE: `Add new client Acme Corp` | "Just type: Add new client Acme Corp" |
| SHOW: Claude processing | "Claude creates the folder, sets up files, adds it to your dashboard..." |
| SHOW: Confirmation message | "...and confirms it's done." |
| HIGHLIGHT: "Want to add some details?" suggestion | "Notice it suggests what to do next. You can add details now, or just keep going." |
| TYPE: `Show me Acme's profile` | "Let's see what it created. Type: Show me Acme's profile" |
| SHOW: Profile content displayed | "Here's the profile file - it's got sections for all the info you'll want to track." |
| ZOOM: Show "Key Contacts" section | "Contacts, background, business goals - all ready for you to fill in." |

**Production notes:**
- Type at normal speed (not fast)
- Show full confirmation message
- Zoom smoothly (not jarring)

---

## Section 6: Next Steps (30 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal with completed commands visible | "That's it - you're running. You added a client, saw the files, all in under 5 minutes." |
| SHOW: Docs folder in file browser | "Check the docs folder for guides on processing notes, running assessments, all that stuff." |
| SHOW: Title card "Next: Voice Training" | "Next video I'll show you how to train the AI to write in your voice - pretty cool." |
| FADE: To end card with links | "Links to everything in the description. Thanks!" |

**Production notes:**
- Show file browser briefly (visual confirmation)
- End card: docs link, next video link, subscribe button
- Keep ending brief (Seth style - no long outro)

---

## Description Text

Paste this in YouTube description:

```
Setting up Agent PM in 5 minutes.

Commands from the video:
git clone https://github.com/user/agent-pm.git
cd agent-pm
claude
Add new client Acme Corp

Links:
- Agent PM repo: [URL]
- Documentation: [URL]/docs/guides/
- Troubleshooting: [URL]/docs/guides/troubleshooting.md

Next video: Voice training (make the AI write like you)
```

**Timestamps (add after upload):**
- 0:00 Intro
- 0:30 Opening Terminal
- 1:15 Cloning Project
- 2:15 Starting Claude Code
- 3:00 Adding First Client
- 4:30 Next Steps
```

**Source:** Derived from [Writing Instructional Video Scripts](https://altuent.com/insights/writing-instructional-video-scripts-eight-top-tips/), `.planning/my_voice_example/seth_voice_profile.md`

## State of the Art

Current approaches to beginner CLI documentation:

| Old Approach | Current Approach (2026) | When Changed | Impact |
|--------------|------------------------|--------------|--------|
| Assume git knowledge | Explain git invisibly or hide completely | ~2023 with AI agents | Non-technical users can use git-based tools |
| Generic "works everywhere" tutorial | Agent-specific tutorials | 2024-2025 | Better UX, fewer edge cases, clearer instructions |
| Text-only documentation | Text + visual aids + video | Always evolving | Video is now expected for technical onboarding |
| Features-first README | Quick-start-first README | ~2020 open source shift | Users succeed faster, higher adoption |
| Complete docs upfront | Progressive disclosure | 2025-2026 | Reduces overwhelm, improves completion rates |
| Professional narrator voice | Personal/authentic voice | 2025-2026 with AI tools | Higher engagement, more relatable |

**Deprecated/outdated:**
- Long "Introduction" sections before actionable content - skip straight to Quick Start
- Single monolithic tutorial - separate by platform/agent
- Alphabetical command reference as primary docs - use task-based organization
- "Read the docs first" mentality - docs support doing, not replace doing

**Current best practice (2026):**
- README.md has one clear first step in first 50 words
- Video walkthroughs are standard, not optional
- Progressive disclosure (essential → intermediate → advanced)
- Voice/personality in tutorials (conversational, not corporate)
- Working examples with realistic data (not Lorem Ipsum)

## Open Questions

### 1. Video Production Scope

**What we know:**
- Phase 7 CONTEXT.md specifies video scripts as "one-time deliverable" for YouTube launch
- Scripts needed: intro/demo, detailed setup, feature walkthroughs
- Should be in Seth's voice

**What's unclear:**
- Should scripts include B-roll suggestions? (probably not - keep minimal)
- Editing notes vs production notes? (probably same thing, keep simple)
- Optimal video length? (research suggests 5-7 min for setup, 2-3 min for features)

**Recommendation:** Keep video scripts minimal - just A/V format with basic production notes. Seth will handle production details during actual recording. Scripts are blueprints, not film production documents.

### 2. Reference Example Completeness

**What we know:**
- Generic marketing agency use case
- Progressive layers (minimal → complete)
- Include generated deliverables from specialists

**What's unclear:**
- How many example clients is "enough"? (3-5 probably ideal)
- Should example include voice training? (probably yes, shows the feature)
- Should example be in repo or separate download? (in repo, docs/reference-example/)

**Recommendation:** 3 clients with varying complexity: 1) minimal (just added), 2) with notes processed, 3) with specialist deliverables. Include voice_profile.md to demonstrate that feature. Keep in repo for easy access.

### 3. Troubleshooting Skill vs Documentation

**What we know:**
- Phase 7 CONTEXT.md specifies "detailed troubleshooting docs AND skill for agent"
- Skill gives agent full context to help users diagnose
- Docs give users self-service FAQ

**What's unclear:**
- Should skill reference docs or duplicate content? (reference, single source of truth)
- Should skill be agent-specific or generic? (generic with agent-aware sections)

**Recommendation:** Create troubleshooting.md as single source of truth. Create troubleshooting skill that references this file and adds agent intelligence (can diagnose, suggest fixes based on user's environment). Skill should read troubleshooting.md and use it as knowledge base.

### 4. Multi-Agent Testing Complexity

**What we know:**
- System must work with Claude Code, opencode, codex at minimum
- Each has different capabilities and limitations
- Separate tutorials per agent

**What's unclear:**
- Who tests on all three agents? (realistically: Claude Code primarily, others best-effort)
- What if feature works in one agent but not others? (document in agent-specific tutorial)

**Recommendation:** Test primarily on Claude Code (most capable). Document known limitations in agent-specific tutorials. Community can contribute fixes/clarifications for other agents. Don't block launch on perfect cross-agent parity.

## Sources

### Primary (HIGH confidence)

**Documentation Best Practices:**
- [10 Essential Technical Documentation Best Practices for 2026](https://www.documind.chat/blog/technical-documentation-best-practices) - Current standards
- [How to Write Technical Documentation in 2025: A Step-by-Step Guide](https://dev.to/auden/how-to-write-technical-documentation-in-2025-a-step-by-step-guide-1hh1) - Practical guide
- [Documentation Best Practices - Google Style Guide](https://google.github.io/styleguide/docguide/best_practices.html) - Industry standard

**README Structure:**
- [How to Create the Perfect README for Your Open Source Project](https://dev.to/github/how-to-create-the-perfect-readme-for-your-open-source-project-1k69) - GitHub best practices
- [How to Write a Beginner-Friendly README for Open-Source Projects](https://www.readmecodegen.com/blog/beginner-friendly-readme-guide-open-source-projects) - Complete 2025 guide
- [How to Write a Good README File for Your GitHub Project](https://www.freecodecamp.org/news/how-to-write-a-good-readme-file/) - freeCodeCamp guide

**CLI/Terminal Tutorials:**
- [Ubuntu's Linux Command Line for Beginners](https://ubuntu.com/tutorials/command-line-for-beginners) - Zero-knowledge assumed
- [Command Line for Beginners – How to Use the Terminal Like a Pro](https://www.freecodecamp.org/news/command-line-for-beginners/) - Full handbook
- [Command Line Interface Guidelines](https://clig.dev/) - Modern CLI design principles

**Video Tutorial Scripts:**
- [Writing Instructional Video Scripts: Eight Top Tips](https://altuent.com/insights/writing-instructional-video-scripts-eight-top-tips) - Industry best practices
- [How to Write a Script for a Video Tutorial: Step-by-Step Guide](https://wowto.ai/blog/how-to-write-a-script-for-a-video-tutorial) - Comprehensive guide

**Troubleshooting Guides:**
- [What Is a Troubleshooting Guide and How to Write One](https://www.archbee.com/blog/troubleshooting-guide) - Complete guide
- [How to Create a Troubleshooting Guide](https://scribe.com/library/troubleshooting-guide) - Template and best practices
- [Build Troubleshooting Guides That Empower Customer Service Teams](https://knowmax.ai/blog/troubleshooting-guides-for-customer-service/) - FAQ format approach

**Progressive Disclosure:**
- [Progressive Disclosure - NN/G](https://www.nngroup.com/articles/progressive-disclosure/) - UX foundation
- [Progressive Disclosure Matters: Applying 90s UX Wisdom to 2026 AI Agents](https://aipositive.substack.com/p/progressive-disclosure-matters) - Modern application
- [What is Progressive Disclosure? | IxDF](https://www.interaction-design.org/literature/topics/progressive-disclosure) - Design patterns

### Secondary (MEDIUM confidence)

**AI Coding Agent Landscape:**
- [AI Coding Tools: The Complete Guide to Claude Code, OpenCode & Modern Development](https://senrecep.medium.com/ai-coding-tools-the-complete-guide-to-claude-code-opencode-modern-development-eb9da4477dc1) - 2026 overview
- [Agent Skills | OpenCode](https://opencode.ai/docs/skills) - Cross-platform skill format

**Project Management Context:**
- [GitHub Project Management: a Guide](https://www.zenhub.com/github-project-management) - Git-based PM patterns

### Internal Documentation (HIGH confidence)

**Existing System Documentation:**
- `/Users/seth/Documents/GitHub/agent-pm/INSTRUCTIONS.md` - Agent instructions
- `/Users/seth/Documents/GitHub/agent-pm/.planning/PROJECT.md` - Project context
- `/Users/seth/Documents/GitHub/agent-pm/docs/protocols/` - 15 protocol files
- `/Users/seth/Documents/GitHub/agent-pm/.planning/my_voice_example/seth_voice_profile.md` - Voice reference for video scripts

**Phase Context:**
- Phase 7 CONTEXT.md - Locked decisions (separate tutorials, progressive layers, video scripts, FAQ format)
- Phase 6 RESEARCH.md - Voice training system research (capability assessment, extraction patterns)

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - Markdown is universal, no dependencies, proven in open source
- Architecture patterns: HIGH - Progressive disclosure and agent-specific tutorials verified in 2026 research
- Video scripts: MEDIUM - A/V format standard for technical content, Seth's voice application is custom
- Troubleshooting: HIGH - FAQ format is industry standard, verified across multiple sources
- Reference example: MEDIUM - Pattern is sound (progressive layers), specific implementation needs validation

**Research scope:**
- Beginner CLI tutorials - extensive coverage, high confidence
- README best practices - current 2026 standards, verified across sources
- Video script writing - technical tutorial specific guidance found
- Troubleshooting guides - FAQ format and structure well-documented
- Progressive disclosure - modern UX principle, recently applied to AI agents

**Gaps identified:**
- No existing README.md (critical for Phase 7)
- No docs/guides/ directory (all docs currently agent-facing)
- No reference example built (mentioned in PROJECT.md but not implemented)
- Video scripts are one-time deliverable (not permanent system feature)

**Research date:** 2026-01-25
**Valid until:** 30 days (stable domain - documentation best practices evolve slowly)
