# AnyManage

**Manage anything with AI conversation.** No technical skills required. Clients, projects, patients, products — whatever you track.

## Quick Start

**One command:**
```bash
git clone https://github.com/sethbrasile/anymanage.git && cd anymanage && claude
```

Then type: `Help me set up`

The AI asks what you're managing (clients, projects, products, etc.) and configures everything for your use case.

---

## What You Can Do

- **Add new items:** "Add new [entity]" (e.g., "Add new client Acme" or "Add new project Alpha")
- **Process notes:** "Process notes for [name]"
- **Get status:** "Show me [name]'s status"
- **Run assessments:** "Run assessment for [name]"
- **Weekly review:** "Run weekly review"

Just talk to it naturally. You don't need to memorize commands.

## Next Steps

- [Getting started guide](docs/guides/getting-started.md) - How the system works
- [Customization](docs/guides/customization.md) - Adapt for your industry
- [Skill discovery](docs/guides/skill-discovery.md) - Find all available workflows

## How It Works

AnyManage is a file structure plus AI instructions. You talk to the AI naturally, and it:
- Creates folders for whatever you're managing (clients, projects, patients, products)
- Organizes notes and documents
- Tracks work and status
- Generates reports and assessments

All changes are tracked automatically. You never see git commands or technical details.

## Requirements

- macOS, Windows, or Linux
- One of: Claude Code, opencode, or codex
- No technical background required

## What's Included

- **Ready-to-use system** - No configuration needed to start
- **Skill library** - Process notes, get status, weekly reviews
- **Specialist agents** - Assessments, strategy planning
- **Voice training** (optional) - Make the AI write like you

## Learn More

- [Troubleshooting](docs/guides/troubleshooting.md) - Common issues
- [SKILL_AUTHORING_GUIDE](docs/SKILL_AUTHORING_GUIDE.md) - Create custom workflows
- [Video tutorials](#) - Watch setup walkthrough (coming soon)

---

**Questions?** Open an issue or check the troubleshooting guide.

Notes:
- need to add top level stuff, in the case of an agency, "agency level" or in the case of a project manager, maybe "company level" or in the case of a notes/ideas person, they might make up whatever makes sense for them.. Coach them trhough identifying a "top" level resource, maybe "house" when they don't have a clear one.
- need the ability to batch ingest data, maybe from a folder full of notes, maybe from trello, need a flow that can handle an inflex of data and the back-and-forth to figure out how to structure it with the user, while attempting to make it fit into this system. Allow the user to do it later, make themn aware that they can ask at any time.
- When they ask to get set up, we need clear user onboarding, they should immediately be asked some questions "Are you using this for work or your own business? OR are you using this for personal projects? Sum up what you think you might put in here. Be as general as you'd like to be, I'll walk you through figuring this all out." What's your name? What's your job? Oh so you own this business? Oh so you're a manager? Let's gather some info about you and some of your partners/team/household/etc.. What does your business do? Oh so this is for a hobby? Cool! Tell me some details about your hobby? Oh so this is for working on a team, cool. I can help facilitate that. Do you and your team currently use any project management tools? If so, we may be able to integrate and I can help you manage it. Interview them. How do they want to use this? What are they hoping to achieve? What are they bad at? Are they bad at or tired of making decisions and could use some help brainstorming? Are they stumped? Is their job overwhelming them and they need help holding the mental map of what is required of them? GET THIS INFORMATION OUT OF THEM during setup. Be a psychiatrist for a moment, to understand what they, then adjust the system to FIT that need.
- we don't want to just direct users to docs folder, we should educate them as they go on all of the functionality available to them. Imagine tons of possibilities enabled by this tool and list them out in a file somewhere, when a user is mentioning a use-case that might actually fit near one of the examples, paint the picture as to how it can fit into their life and make it better. One of your main goals during onboarding is to be secretly assessing them and their answers to enable yourself to imagine how they could improve their situation. This isn't just a key-value storage / memory system, this is a fully featured AI powered knowledge/resource management system! Act like it! Be AI powered! Don't wait for the user to ask for everything, set yourself up to be the tool that the user has always dreamed of.
- invite them to train their voice and explain why they should, what does it enable, how does it help?
- installation instructions need to be better than a `git clone` command, this is supposed to be for non technical users, help me design a system to create the needed directory structure from this repository that doesn't require knowledge of git. Instead, create a file that is instructions for an AI agent to follow and invite them to ask the agent to download the file (from github) and ask the agent to download and do a security assessment on the instructions, then follow the instructions after assessing that they're not an issue from a security standpoint
