---
name: troubleshoot
description: Diagnose and resolve user issues with AnyManage. Use when user reports problems, errors, or unexpected behavior. Guides users through troubleshooting steps and knows when to escalate to GitHub issues.
license: Apache-2.0
metadata:
  author: anymanage
  version: "1.0"
  knowledge_base: docs/guides/troubleshooting.md
---

# Troubleshoot Skill

Help users diagnose and resolve issues with AnyManage.

## When to Use

Trigger this skill when user:
- Says "help", "something's wrong", "not working", "broken"
- Describes an error message
- Says "I tried to [action] but [unexpected result]"
- Asks "why isn't [feature] working?"
- Reports unexpected behavior
- Says "I'm stuck" or "can't figure out"
- Mentions specific errors like "command not found", "permission denied", "not found"

## How It Works

This skill uses `docs/guides/troubleshooting.md` as its knowledge base. Don't duplicate content - reference and apply it.

**Diagnosis flow:**

1. **Identify the symptom**
   - What did the user try to do?
   - What happened instead?
   - Any error messages?

2. **Categorize the issue**
   - Installation: git, agent setup, permissions
   - Startup: INSTRUCTIONS.md, STATE.md, session
   - Entity management: creation, profiles, notes
   - Skills/specialists: discovery, execution, output
   - Voice training: profile, application, learning
   - API/auth: keys, rate limits, permissions

3. **Apply troubleshooting steps**
   - Reference the relevant section from troubleshooting.md
   - Walk user through fixes step by step
   - Verify each step before moving to next

4. **Escalate if needed**
   - If none of the documented solutions work
   - If the issue seems like a bug
   - Direct to: https://github.com/your-repo/anymanage/issues

## Diagnosis Patterns

**Error message present:**
- Match against known errors in troubleshooting.md
- Apply the specific fix
- Verify resolution

**Symptom without error:**
- Ask clarifying questions
- Narrow down category
- Apply most likely fix first

**Vague complaint ("it's broken"):**
- Ask: "What were you trying to do?"
- Ask: "What happened when you tried?"
- Gather enough info to diagnose

## Response Format

When helping with issues:

```
I see - [restate the problem in plain terms].

This usually happens because [brief explanation].

Let's fix it:
1. [First step]
2. [Second step]
3. [Verification step]

Try that and let me know if it works.
```

When escalating:

```
I wasn't able to resolve this with the usual fixes.

This might be a bug or edge case. I'd suggest opening an issue:
https://github.com/your-repo/anymanage/issues

Include:
- What you were trying to do
- What happened
- The exact error message
- Your operating system and agent (Claude Code, opencode, codex)

That will help the maintainers investigate.
```

## Knowledge Base Reference

For detailed solutions, see `docs/guides/troubleshooting.md`. Key sections:

- **Installation Issues:** git, agent setup, permissions, repository
- **Startup Issues:** INSTRUCTIONS.md loading, STATE.md, session memory
- **Entity Management:** folder creation, profile updates, note processing, duplicates
- **Skill/Specialist Issues:** discovery, execution, deliverables
- **Voice Training:** profile location, application, training quality
- **API/Auth:** keys, rate limits, authentication

## What NOT To Do

- Don't guess at solutions not in the knowledge base
- Don't ask user to modify core system files without reason
- Don't suggest complex git operations (keep git invisible)
- Don't dismiss issues as "user error" - assume good faith

## Slash Command

`/troubleshoot` or `/help`

Example: `/troubleshoot "command not found: claude"`

## Related Resources

- Troubleshooting guide: `docs/guides/troubleshooting.md`
- Error recovery protocol: `docs/protocols/error-recovery.md`
- Session protocol: `docs/protocols/session-protocol.md`

---

*Skill: troubleshoot*
*Version: 1.0*
