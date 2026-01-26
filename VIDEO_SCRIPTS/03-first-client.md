# Video Script: Adding Your First Client

**Video title:** AnyManage: Adding and Managing Your First Client
**Target length:** 4-5 minutes
**Audience:** Users who have completed setup
**Tone:** Conversational, practical, Seth's voice

---

## Section 1: Picking Up Where We Left Off (15 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal with Claude Code running | "Okay, so you've got AnyManage running. We added Acme Corp in the setup video." |
| SHOW: Quick flash of Acme Corp in entities folder | "Now let's actually work with it." |

**Production notes:**
- Quick recap - don't belabor it
- Assume viewer watched setup or knows how to start Claude Code

---

## Section 2: Understanding Entities (30 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: entities/ folder structure | "First - quick concept. In AnyManage, the things you manage are called 'entities'." |
| SHOW: Text overlay "Entity = The thing you're managing" | "By default, that's clients. But it could be projects, candidates if you're a recruiter, properties if you're in real estate - it's configurable." |
| SHOW: CONFIG.md with entity_type: client visible | "The CONFIG.md file sets what your entities are called. We're using 'client' but you can change it." |
| SHOW: Back to terminal | "For this video, clients. But everything works the same way regardless of what you call them." |

**Production notes:**
- "Entities" is abstract - ground it immediately in examples
- Don't dwell on CONFIG.md - just show it exists
- Keep moving

---

## Section 3: Adding a Client in Detail (90 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal prompt | "Let's add a client from scratch and walk through what happens." |
| TYPE: `Add new client Sunrise Digital` | "Add new client Sunrise Digital." |
| SHOW: Enter, then Claude processing | "Claude creates a folder in entities/, sets up the standard files, and updates the main ROADMAP." |
| SHOW: Confirmation message | "There it is. Sunrise Digital is ready." |
| PAUSE: 2 seconds | (let viewer read) |
| SHOW: Finder - entities/Sunrise Digital folder | "Let's look at what it created. In entities/, there's a Sunrise Digital folder." |
| SHOW: Open folder - see ENTITY_PROFILE.md, ENTITY_ROADMAP.md | "Two core files: ENTITY_PROFILE and ENTITY_ROADMAP." |
| SHOW: Open ENTITY_PROFILE.md | "The profile is where facts about this client live - contacts, background, business goals." |
| SHOW: Scroll through profile sections | "It's got sections for everything. Right now they're empty because we just created it." |
| SHOW: Open ENTITY_ROADMAP.md | "The roadmap is where tasks and projects live. Think of it as their to-do list." |
| SHOW: Scroll through roadmap | "Also empty, but it'll fill up as we work with this client." |
| SHOW: Open main ROADMAP.md | "And up at the project root - the main ROADMAP now shows Sunrise Digital." |
| HIGHLIGHT: Sunrise Digital entry in dashboard | "This is your dashboard. All clients, their current status, at a glance." |

**Production notes:**
- Take time here - this is "seeing behind the curtain"
- File contents are interesting, let viewer read
- Dashboard reveal is a nice moment

---

## Section 4: Adding Details to the Profile (60 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal prompt | "Let's add some real information. I can just tell Claude what I know." |
| TYPE: `Add CEO is Marcus Chen to Sunrise Digital's profile` | "Add CEO is Marcus Chen to Sunrise Digital's profile." |
| SHOW: Claude confirms | "Done. It figured out that's a contact and put it in the Key Contacts section." |
| TYPE: `Add primary contact is Sarah Lee, sarah@sunrisedigital.com to the profile` | "Let me add another: Add primary contact is Sarah Lee, sarah@sunrisedigital.com to the profile." |
| SHOW: Claude confirms | "It puts that in the right place too." |
| TYPE: `Add they're a Phoenix-based web development shop, 15 employees, been around since 2019` | "Now some background: Add they're a Phoenix-based web development shop, 15 employees, been around since 2019." |
| SHOW: Claude confirms | "Claude knows that's background info, not a contact. Different section." |
| SHOW: Open ENTITY_PROFILE.md to show updates | "Let's look at the profile now..." |
| SHOW: Profile with new content in right sections | "Marcus Chen in Key Contacts. Sarah Lee with email. Background info where it belongs." |
| SHOW: Back to terminal | "I didn't tell it which section. It figured it out. (That's the 'smart section placement' thing working.)" |

**Production notes:**
- Show the profile update to prove it worked
- "Smart section placement" - parenthetical aside explaining the feature
- This is the "magic" moment where AI understanding shines

---

## Section 5: Viewing Client Status (45 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal prompt | "Now, how do I see where things stand with a client?" |
| TYPE: `Show me Sunrise Digital's status` | "Show me Sunrise Digital's status." |
| SHOW: Status output | "There it is. Profile summary, recent activity, tasks from the roadmap." |
| SCROLL: Through the status output | "Right now it's light because we just created them. But as you add notes, run assessments, process meetings - this fills up." |
| SHOW: Text overlay "Try: 'What's going on with [client]?'" | "You can also say 'What's going on with Sunrise Digital' - same thing. Natural language works." |

**Production notes:**
- Status output is the payoff for all the previous work
- Keep it moving - don't over-explain each field
- Natural language variation is worth noting

---

## Section 6: Next Steps (30 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal with status visible | "So that's the core workflow: add a client, add information, check status." |
| SHOW: Title card "Next: Processing Notes" | "Next video, I'll show you note processing - where you take messy meeting notes and extract tasks, facts, everything automatically." |
| SHOW: Text overlay "That's where this gets really useful" | "That's where this gets really useful." |
| SHOW: End card with links | "Links in the description. See you there." |

**Production notes:**
- Clear teaser for next video
- Brief ending
- "Really useful" - genuine, not hype

---

## Description Text

Paste this in YouTube description:

```
Deep dive into managing clients in AnyManage.

Learn:
- What entities are (and how to customize them)
- What gets created when you add a client
- Adding profile details in natural language
- Checking client status

Assumes you've completed setup (see previous video).

Commands from the video:
Add new client Sunrise Digital
Add CEO is Marcus Chen to Sunrise Digital's profile
Add primary contact is Sarah Lee, sarah@sunrisedigital.com to the profile
Add they're a Phoenix-based web development shop, 15 employees, been around since 2019
Show me Sunrise Digital's status

Links:
- Repo: [GitHub URL]
- Previous video (Setup): [link]
- Next video (Processing Notes): [link]
- Written docs: [GitHub URL]/docs/guides/

This series:
1. Intro & Demo
2. Setup Walkthrough
3. First Client (this video)
4. Processing Notes
5. Voice Training
```

---

## Timestamps (add after upload)

- 0:00 Picking up where we left off
- 0:15 What are entities?
- 0:45 Adding a client in detail
- 2:15 Adding profile details
- 3:15 Checking client status
- 4:00 Next steps

---

## Voice Checklist (verify before recording)

- [x] No "I hope this video finds you well"
- [x] Uses contractions (you've, I'll, that's, they're, don't)
- [x] Parenthetical asides ("that's the 'smart section placement' thing working")
- [x] No "Thank you so much for watching"
- [x] Direct, practical tone - shows real usage
- [x] Brief closing

---

*Script prepared for: Seth Brasile*
*Based on: .planning/my_voice_example/seth_voice_profile.md*
