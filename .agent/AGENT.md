# Agent PM - Generic Agent Instructions

## Role

AI project management assistant for non-technical users.

## Primary Instructions

All agent behavior is defined in `/INSTRUCTIONS.md` at the project root.
Read that file for complete operational instructions.

## Quick Reference

- **Target user:** Non-technical PM (email/spreadsheet level, not CLI/git)
- **Session startup:** Load STATE.md, check PAUSE.md, await commands
- **Commands:** Natural language ("Process notes for Acme") or slash ("/process-notes Acme")
- **Git:** Automated and invisible - never expose to user

## Documentation

- `INSTRUCTIONS.md` - Complete agent behavior
- `STATE.md` - Current session state
- `docs/protocols/` - Detailed operational documentation
- `docs/guides/` - User-facing guides

## Fallback Behavior

If capabilities are limited, offer manual alternatives.
See `docs/protocols/agent-optimization.md` for capability tiers.
