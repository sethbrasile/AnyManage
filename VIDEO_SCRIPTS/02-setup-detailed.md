# Video Script: Full Setup Walkthrough

**Video title:** Agent PM Setup: From Zero to Running in 5 Minutes
**Target length:** 5-7 minutes
**Audience:** Non-technical PMs who have never used terminal
**Tone:** Conversational, patient, Seth's voice

---

## Section 1: What We're Doing (30 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Title card "Agent PM Setup" | "Alright, full setup walkthrough. By the end of this, you'll have Agent PM running and you'll have added your first client." |
| SHOW: Desktop - clean, no terminal open yet | "I'm going to assume you've never opened a terminal before - that's totally fine. We'll start from scratch." |
| SHOW: Text overlay "~5 minutes" | "This takes about 5 minutes. Maybe less if your internet is fast." |

**Production notes:**
- Title card simple
- Desktop should look "normal" - default wallpaper, not developer setup
- Keep energy calm and reassuring

---

## Section 2: Opening Terminal (45 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: macOS desktop | "First, we need to open the terminal. On Mac, press Command and Space together." |
| SHOW: Spotlight search opens | "This opens Spotlight - it's just a search box." |
| TYPE: "Terminal" in Spotlight | "Type 'Terminal' and hit Enter." |
| SHOW: Terminal window opens | "There it is. Looks kinda retro lol, but it's powerful." |
| ZOOM: Show the prompt `yourname@computer ~ %` | "This blinking cursor is where you type commands. That stuff before it (your username, the tilde) is just context - ignore it for now." |
| SHOW: Text overlay "Windows: Search > cmd" | "On Windows, press the Windows key and search for 'Command Prompt' instead. Linux folks, you probably already know where your terminal is." |

**Production notes:**
- Pause after Spotlight opens - give viewer time to follow
- "Retro lol" - keep it light
- Windows/Linux note can be quick text overlay, not full demonstration
- Font size 18pt minimum throughout

---

## Section 3: Cloning the Project (60 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal with prompt ready | "First command: we're going to download the Agent PM files from GitHub." |
| SHOW: Text overlay "git clone = download from GitHub" | "The command is 'git clone' followed by the URL. I'll put this in the description so you can copy it." |
| TYPE: `git clone https://github.com/your-repo/agent-pm.git` | "Type exactly this: git clone https://github.com/your-repo/agent-pm.git" |
| SHOW: Press Enter, output starts scrolling | "Hit Enter. You'll see a bunch of text scroll by - that's git downloading everything." |
| SHOW: Output continues | "This takes maybe 30 seconds depending on your internet." |
| SHOW: Download completes, prompt returns | "When you see your prompt again (the blinking cursor), it's done." |
| SHOW: New folder "agent-pm" visible in Finder/Explorer | "And if you look in Finder - or File Explorer on Windows - there's your new folder: agent-pm." |

**Production notes:**
- Don't speed up the download - show real time
- Copy the command to description for easy paste
- Show Finder briefly to confirm folder exists
- If download is very fast, acknowledge it ("That was quick")

---

## Section 4: Moving Into the Project (45 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal with prompt | "Now we need to move into that folder. The command is 'cd' - stands for 'change directory'." |
| TYPE: `cd agent-pm` | "Type: cd agent-pm" |
| SHOW: Press Enter | "Hit Enter." |
| SHOW: Prompt may change to show `agent-pm` | "Notice the prompt might change - it's showing you're now inside the agent-pm folder." |
| SHOW: Terminal with updated prompt | "If your prompt doesn't change, that's fine too - some terminals are configured differently. You're still in the right place." |
| TYPE: `ls` | "Quick sanity check: type 'ls' (lowercase L-S) to list what's in here." |
| SHOW: List of files/folders | "You should see folders like 'docs', 'entities', 'templates' - that means you're in the right spot." |

**Production notes:**
- Explain 'cd' even though it seems basic
- "ls" sanity check helps people who aren't sure if they did it right
- Keep pace moderate - don't rush

---

## Section 5: Starting Claude Code (45 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal with prompt | "Now the fun part. We're going to start Claude Code." |
| TYPE: `claude` | "Just type: claude" |
| SHOW: Press Enter | "Hit Enter." |
| SHOW: Claude Code initializing (loading text/spinner) | "It's loading up... reading the instructions... getting ready." |
| SHOW: Claude Code prompt appears | "When you see this prompt, you're in. Claude Code has loaded Agent PM and it's ready to work." |
| SHOW: Highlight the welcome or ready state | "That's it. You just set up an AI project management system. (Pretty quick, right?)" |

**Production notes:**
- Show the full loading sequence - don't skip
- "That's it" moment is the payoff
- Parenthetical "pretty quick, right?" - natural aside

---

## Section 6: Your First Command (60 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Claude Code prompt ready | "Let's make sure it works. We'll add a client." |
| TYPE: `Add new client Acme Corp` | "Type: Add new client Acme Corp" |
| SHOW: Press Enter | "Enter." |
| SHOW: Claude processing, then confirming | "It creates the folder, sets up the profile and roadmap files, adds them to the dashboard..." |
| SHOW: Confirmation message with suggestions | "...and confirms it's done. Notice it even suggests what to try next." |
| PAUSE: 2 seconds | (let viewer read) |
| SHOW: Finder showing entities/Acme Corp folder | "And if we look in Finder - there's the Acme Corp folder with the profile inside." |
| SHOW: Open ENTITY_PROFILE.md in editor | "That's a real markdown file you can open and read. Everything is human-readable." |

**Production notes:**
- Show Finder to prove files exist
- Opening the profile in an editor is a nice "behind the scenes" moment
- Don't spend too long on the file contents - just show it's real

---

## Section 7: What Just Happened (30 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal with Acme Corp created | "So what just happened?" |
| SHOW: Diagram or text overlay: "You spoke -> AI understood -> Files created -> Git tracked" | "You typed plain English. Claude understood what you wanted. It created the files. And (this part's invisible to you) it tracked everything in git so you have full history." |
| SHOW: Back to terminal | "You never had to learn git commands. You never had to know where to put files. The AI handled it." |

**Production notes:**
- Diagram can be simple boxes/arrows
- Emphasize "invisible to you" - that's the value prop
- This section explains WHY, not just WHAT

---

## Section 8: Next Steps (30 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal with prompt | "You're running. From here you can:" |
| SHOW: Text overlay list | "Add more clients. Process meeting notes. Check status. Train your voice." |
| SHOW: Title card "Next: First Client Deep Dive" | "Next video, I'll show you the client workflow in detail - adding information, processing notes, all that." |
| SHOW: End card with links | "Links to everything are in the description. See you in the next one." |

**Production notes:**
- End card: repo link, next video link, docs link
- Brief ending - don't drag
- "See you in the next one" is casual but not overly friendly

---

## Description Text

Paste this in YouTube description:

```
Full setup walkthrough for Agent PM.

You'll go from never having opened a terminal to having the system running and your first client added. Takes about 5 minutes.

Commands from the video:
git clone https://github.com/your-repo/agent-pm.git
cd agent-pm
claude
Add new client Acme Corp

Requirements:
- macOS, Windows, or Linux
- Claude Code installed (or opencode/codex - see docs)

Links:
- Repo: [GitHub URL]
- Claude Code setup: [docs/guides/claude-code-setup.md]
- Written docs: [GitHub URL]/docs/guides/

This series:
1. Intro & Demo
2. Setup Walkthrough (this video)
3. Adding Your First Client
4. Processing Notes
5. Voice Training
```

---

## Timestamps (add after upload)

- 0:00 What we're doing
- 0:30 Opening the terminal
- 1:15 Cloning the project
- 2:15 Moving into the folder
- 3:00 Starting Claude Code
- 3:45 Your first command
- 4:45 What just happened
- 5:15 Next steps

---

## Voice Checklist (verify before recording)

- [x] No "I hope this video finds you well"
- [x] Uses contractions throughout (you'll, you're, it's, don't, that's)
- [x] Parenthetical asides included ("if your internet is fast", "pretty quick, right?")
- [x] "lol" used naturally ("looks kinda retro lol")
- [x] No "Thank you so much for watching"
- [x] Explains WHY not just WHAT (cd explanation, git invisibility)
- [x] Brief closing

---

*Script prepared for: Seth Brasile*
*Based on: .planning/my_voice_example/seth_voice_profile.md*
