# Video Script: Processing Notes

**Video title:** Agent PM: Turn Meeting Notes into Tasks Automatically
**Target length:** 4-5 minutes
**Audience:** Users who have added at least one client
**Tone:** Conversational, practical, Seth's voice

---

## Section 1: The Problem (30 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Stock image or text overlay of messy notes | "Meeting notes pile up. You know how it is." |
| SHOW: Text overlay "Where was that action item?" | "Somewhere in there is who's doing what, important facts about the client, stuff that needs to go on the calendar..." |
| SHOW: Highlight a buried action item in notes | "...and it takes forever to dig through and extract it all." |
| SHOW: Terminal with Claude Code | "Agent PM fixes this." |

**Production notes:**
- Keep problem statement quick - everyone knows this pain
- Don't dwell on negativity, move to solution fast

---

## Section 2: The Solution (30 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal prompt | "One command: process notes for [client name]." |
| SHOW: Text overlay showing categories | "The AI reads through your notes and extracts: tasks (things to do), facts (things to remember), calendar items (dates to track), and follow-ups (things to check back on)." |
| SHOW: Arrows pointing to different files | "Each thing goes to the right place automatically. Tasks go to the roadmap. Facts go to the profile. Nothing falls through the cracks." |

**Production notes:**
- Quick overview before showing it in action
- Categories visual helps set expectations

---

## Section 3: Adding Notes (60 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal prompt | "First, you need notes. Couple ways to do this." |
| SHOW: entities/Sunrise Digital/notes/ folder | "Option one: put a file in the client's notes folder. Just a text file or markdown file with your meeting notes." |
| SHOW: Create new file in notes/ | "I'll create one called 'kickoff-call.md'." |
| SHOW: Paste content into file | "Here's some typical meeting notes..." |
| SHOW: Notes content - messy, real-looking | "There's tasks buried in here, facts about their business, a deadline mentioned. Normal meeting notes." |
| SHOW: Back to terminal | "Option two: paste notes directly into the conversation. Claude can read whatever you paste." |
| SHOW: Text overlay "Pro tip: NOTES.md inbox" | "Pro tip: there's also a NOTES.md file at the project root. Drop notes in there and process them later - kind of like an inbox." |

**Production notes:**
- Show real-looking notes, not obviously fake
- Notes should include:
  - Action items mixed into prose
  - A deadline or date mentioned
  - Some facts about the client
- Don't over-explain the inbox - just mention it

---

## Section 4: Processing Notes (90 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal prompt | "Now the fun part." |
| TYPE: `Process notes for Sunrise Digital` | "Process notes for Sunrise Digital." |
| SHOW: Claude processing, thinking | "Claude reads through everything in the notes folder..." |
| SHOW: Output starting to appear | "...and starts categorizing what it finds." |
| SHOW: Tasks section of output | "Tasks: 'Set up analytics dashboard' - that's going to the roadmap. 'Schedule follow-up call' - that too." |
| SHOW: Facts section of output | "Facts: 'They use HubSpot for CRM' - that's going to the profile." |
| SHOW: Calendar section of output | "Calendar: 'Launch date is March 15' - flagged as a date to track." |
| SHOW: Follow-ups section | "Follow-ups: 'Check on logo approval by next week' - that's something to circle back on." |
| PAUSE: 2 seconds | (let viewer see all categories) |
| SHOW: Confirmation - notes archived | "And the original notes get archived - not deleted, moved to notes/archive/ with a timestamp." |
| SHOW: Archive folder with dated file | "So you always have the original if you need it." |

**Production notes:**
- This is the main event - give it space
- Each category reveal should feel satisfying
- Pause to let viewer read the output
- Archive mention is important - shows nothing is lost

---

## Section 5: Checking the Results (60 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal prompt | "Let's see where everything went." |
| TYPE: `Show me Sunrise Digital's profile` | "Profile first." |
| SHOW: Profile with new facts | "There's the HubSpot fact. It knew that was CRM info and put it in the right section." |
| TYPE: `Show me Sunrise Digital's roadmap` | "Now the roadmap." |
| SHOW: Roadmap with new tasks | "Tasks are here - analytics dashboard, follow-up call. Prioritized and ready to work." |
| SHOW: notes/archive/ folder | "And in the archive..." |
| SHOW: Archived note with header | "...our original notes, marked as processed with a timestamp." |
| SHOW: Back to terminal | "One command turned messy notes into organized, actionable stuff." |

**Production notes:**
- Quick tour showing where things landed
- Don't read everything - just highlight key items
- "One command" callback to earlier

---

## Section 6: Next Steps (30 seconds)

| VISUAL | AUDIO |
|--------|-------|
| SHOW: Terminal | "That's note processing. Do this after every meeting and you'll never lose an action item again. (Or a fact. Or a date.)" |
| SHOW: Title card "Next: Voice Training" | "Next video is optional but cool: voice training. Make the AI write like you instead of like a robot." |
| SHOW: End card | "Links in the description." |

**Production notes:**
- Parenthetical aside reinforces value
- "Optional but cool" - honest framing
- Brief ending

---

## Description Text

Paste this in YouTube description:

```
Turn messy meeting notes into organized tasks, facts, and calendar items.

One command extracts:
- Tasks (go to client roadmap)
- Facts (go to client profile)
- Calendar items (dates to track)
- Follow-ups (things to circle back on)

Original notes archived, never deleted.

Commands from the video:
Process notes for Sunrise Digital
Show me Sunrise Digital's profile
Show me Sunrise Digital's roadmap

Links:
- Repo: [GitHub URL]
- Previous video (First Client): [link]
- Next video (Voice Training): [link]
- Written docs: [GitHub URL]/docs/guides/

This series:
1. Intro & Demo
2. Setup Walkthrough
3. First Client
4. Processing Notes (this video)
5. Voice Training
```

---

## Timestamps (add after upload)

- 0:00 The problem: notes pile up
- 0:30 The solution: automatic extraction
- 1:00 Adding notes to the system
- 2:00 Processing notes
- 3:30 Checking the results
- 4:30 Next steps

---

## Voice Checklist (verify before recording)

- [x] No "I hope this video finds you well"
- [x] Uses contractions (you'll, it's, that's, there's, don't)
- [x] Parenthetical asides ("Or a fact. Or a date.")
- [x] No "Thank you so much for watching"
- [x] Conversational ("You know how it is")
- [x] Brief closing

---

*Script prepared for: Seth Brasile*
*Based on: .planning/my_voice_example/seth_voice_profile.md*
