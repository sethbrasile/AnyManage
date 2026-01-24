# Stack Research: AI-Assisted Project Management Templates (2025)

**Research Date:** 2026-01-24
**Context:** Building agent-agnostic PM template for non-technical users

---

## AI Agent Compatibility

### Claude Code

**Instruction File Patterns:**
- `.claude/` directory for configuration and settings
- `.claude/settings.local.json` for permissions and local overrides
- Custom instructions via system prompts (no standard file location yet)
- `.planning/` directory pattern for project context (emerging convention)
- `PROJECT.md` for project state and requirements tracking

**Subagent/Skill Conventions:**
- Skills defined via SDK (not file-based by default)
- Can be user-defined skills in `.claude/skills/` (community pattern)
- MCP (Model Context Protocol) for tool integration
- JSON-based permission model in settings.local.json

**MCP Integration Approach:**
- MCP servers for external integrations (Trello, Gmail, etc.)
- Plugins via marketplace (codemap, safety-net, plannotator)
- Server definitions in Claude Desktop config (~/.config/claude/config.json)
- Not directly portable across agents

**Strengths:**
- Rich permission system for sandboxing
- Strong skill/plugin ecosystem
- Good markdown interpretation
- Native git awareness

**Limitations:**
- Proprietary configuration format
- MCP servers not universally compatible
- Desktop config not repo-portable

---

### Cursor

**Instruction File Patterns:**
- `.cursorrules` file in project root (primary instruction mechanism)
- Plain text format with markdown-style sections
- Single file contains all project-specific instructions
- No nested directory structure required

**Configuration Approach:**
- `.cursorignore` for file exclusions (gitignore syntax)
- Settings managed via IDE, not typically repo-committed
- Custom instructions inline in .cursorrules

**Tool Use Approach:**
- VSCode extension model
- Built-in terminal, file browser, search
- No standardized skill/subagent pattern
- Extensions not typically configured via files in repo

**Strengths:**
- Single-file simplicity (.cursorrules)
- Works in familiar IDE environment
- Good for code-centric workflows

**Limitations:**
- Tight coupling to VSCode/Cursor IDE
- Limited file-based configuration
- Not CLI-native (harder for non-technical users)

---

### Aider

**Instruction File Patterns:**
- `.aider.conf.yml` for configuration (YAML)
- `.aiderignore` for file exclusions
- Can use `.aider.input.history` for command history
- Supports custom prompts via command-line args

**Configuration Approach:**
- YAML configuration file
- Command-line first design
- Git-native (automatic commits)
- Model-agnostic (works with OpenAI, Anthropic, etc.)

**Tool Use Approach:**
- CLI-based workflow
- Built-in git operations
- No plugin/skill system
- Direct model API calls

**Strengths:**
- Simple YAML config
- Excellent git integration
- CLI-native (good for scripts/automation)
- Model-agnostic

**Limitations:**
- No skill/subagent extensibility
- Limited to code editing workflows
- Less suitable for general PM tasks

---

### OpenCode (GitHub Copilot CLI / Codex)

**Instruction File Patterns:**
- No standardized instruction file (as of 2025)
- Uses repository context implicitly
- Comments and README.md as implicit instructions
- `.github/` directory for GitHub-specific configs

**Configuration Approach:**
- GitHub Copilot settings in IDE
- No repo-portable configuration standard
- Relies on code context rather than explicit instructions

**Tool Use Approach:**
- GitHub integrations (Actions, Issues, PRs)
- IDE extensions (VSCode, JetBrains)
- Terminal commands via `gh` CLI

**Strengths:**
- Deep GitHub integration
- Good code completion
- Familiar to developers

**Limitations:**
- No clear instruction file pattern
- IDE-dependent
- Less suitable for non-code workflows
- Limited PM-specific features

---

## Cross-Agent Patterns

### Universal Approaches (Work Across All Agents)

1. **Markdown Documentation Structure**
   - All agents read and understand markdown well
   - Use standard frontmatter (YAML or TOML)
   - Clear heading hierarchy (H1, H2, H3)
   - Code blocks with language tags
   - Lists (ordered/unordered) for tasks and requirements

2. **Directory Conventions**
   - `.planning/` - Strategic context, roadmaps, research
   - `docs/` - User-facing documentation
   - `templates/` - Reusable document templates
   - Hidden config dirs (`.claude/`, `.cursor/`, etc.) for agent-specific

3. **README-First Approach**
   - Root README.md as entry point
   - Links to detailed docs in subdirectories
   - Clear "How to Use This Project" section
   - Examples and common commands

4. **Git as Source of Truth**
   - All agents can read git history
   - Commit messages as audit trail
   - Branches for different contexts
   - Tags for milestones

5. **Semantic Naming Conventions**
   - `PROJECT.md` - Current state and requirements
   - `ROADMAP.md` - Future plans
   - `CHANGELOG.md` - Historical changes
   - `CONTEXT.md` - Background information
   - `TEMPLATES/` - Reusable structures

### Agent-Specific Overrides Pattern

**Recommended Structure:**
```
/
├── README.md                 # Universal entry point
├── INSTRUCTIONS.md          # Agent-agnostic base instructions
├── .claude/
│   └── instructions.md      # Claude-specific extensions
├── .cursorrules             # Cursor-specific (entire file)
├── .aider.conf.yml         # Aider-specific config
└── .planning/
    ├── PROJECT.md
    └── config.json          # Generic config (agent reads as needed)
```

**Key Principle:**
- Base instructions in markdown (INSTRUCTIONS.md or README.md)
- Agent-specific configs only for features unique to that agent
- Fallback: if agent doesn't find its config, use base instructions

---

## Markdown Conventions

### Frontmatter Patterns

**YAML Frontmatter (Recommended - most universal):**
```yaml
---
title: Project Management Template
version: 1.0.0
entity_type: client
last_updated: 2026-01-24
agents:
  - claude-code
  - cursor
  - aider
---
```

**Advantages:**
- Widely supported (Jekyll, Hugo, Obsidian, etc.)
- Easy to parse
- Human-readable
- All AI agents understand YAML

**Alternative: TOML Frontmatter:**
```toml
+++
title = "Project Management Template"
version = "1.0.0"
entity_type = "client"
+++
```

### Directory Structure Conventions

**Recommended Layout:**
```
/
├── README.md                    # Start here
├── INSTRUCTIONS.md             # Base agent instructions
├── .planning/                  # Strategic context (agent-managed)
│   ├── PROJECT.md
│   ├── ROADMAP.md
│   ├── config.json
│   └── research/
├── entities/                   # Domain-specific (clients, projects, etc.)
│   ├── [entity-name]/
│   │   ├── README.md
│   │   ├── notes/
│   │   └── deliverables/
│   └── templates/
│       └── entity-template.md
├── templates/                  # Document templates
│   ├── meeting-notes.md
│   ├── project-brief.md
│   └── status-update.md
├── protocols/                  # Repeatable workflows
│   ├── onboard-entity.md
│   ├── process-notes.md
│   └── weekly-review.md
├── .claude/                    # Claude Code specific
├── .cursorrules               # Cursor specific
└── .aider.conf.yml           # Aider specific
```

### Template/Placeholder Patterns

**Recommended Patterns:**

1. **Double Curly Braces (Mustache-style):**
   ```markdown
   # {{entity_type}} Brief: {{entity_name}}

   **Date:** {{date}}
   **Owner:** {{owner}}
   ```

2. **Square Brackets with Description:**
   ```markdown
   # Client Brief: [CLIENT_NAME]

   **Date:** [YYYY-MM-DD]
   **Owner:** [Project Manager Name]
   ```

3. **All Caps Placeholders:**
   ```markdown
   # CLIENT_NAME Project Brief

   PROJECT_DESCRIPTION

   ## Deliverables
   - DELIVERABLE_1
   - DELIVERABLE_2
   ```

**Why These Work:**
- Easy to spot visually
- Simple regex patterns for replacement
- Clear to non-technical users
- AI agents recognize them as placeholders

### Special Formatting for AI Agents

**Task Lists (All agents recognize):**
```markdown
- [ ] Incomplete task
- [x] Completed task
```

**Status Indicators:**
```markdown
## Status: 🟢 Active | 🟡 Pending | 🔴 Blocked

**Current Phase:** Planning
**Progress:** 60%
**Next Action:** Review requirements
```

**Semantic Sections (helps agents parse):**
```markdown
## Context
[Background information]

## Requirements
- Requirement 1
- Requirement 2

## Constraints
- Constraint 1

## Next Steps
1. Step 1
2. Step 2
```

---

## Technology Stack Recommendations

### Core Stack (Agent-Agnostic)

1. **Configuration Format: JSON**
   - Rationale: All agents can parse, widely supported, simple
   - Use for: Entity definitions, agent settings, template variables
   - Alternative: YAML (more human-readable, but slightly less universal)

2. **Instruction Format: Markdown**
   - Rationale: Universal, human-readable, agent-parseable
   - Use for: All documentation, instructions, templates
   - Convention: Use YAML frontmatter for metadata

3. **Directory Structure: Semantic Naming**
   - `.planning/` - Agent-managed context
   - `entities/` - Domain data (clients, projects, etc.)
   - `templates/` - Reusable structures
   - `protocols/` - Workflow definitions

4. **Version Control: Git**
   - Rationale: All agents support it, required for history
   - Approach: Agent commits automatically, user never interacts
   - Pattern: Descriptive commit messages with operation context

5. **Template Engine: Simple String Replacement**
   - Rationale: No dependencies, works everywhere
   - Pattern: `{{variable}}` or `[PLACEHOLDER]`
   - Implementation: Agent does find-replace or regex

### Optional Enhancements

1. **Semantic Search: grepai + Ollama**
   - Purpose: Faster context retrieval for agents
   - Impact: Reduces token usage in long repos
   - Tradeoff: Additional setup, local compute
   - Recommendation: Offer as optional, document benefits

2. **External Integrations: MCP Servers**
   - Purpose: Connect to Trello, Gmail, etc.
   - Compatibility: Claude Code native, others require adapters
   - Recommendation: Provide pattern + examples, not requirements

3. **Automation: Shell Scripts**
   - Purpose: Common operations as single commands
   - Pattern: `./pm new-client "Acme Corp"`
   - Rationale: Memorizable for non-technical users
   - Implementation: Bash scripts that call agent with prepared prompts

### Agent Detection Pattern

**config.json structure:**
```json
{
  "entity_type": "client",
  "template_version": "1.0.0",
  "agents": {
    "claude-code": {
      "instructions_file": ".claude/instructions.md",
      "mcp_servers": ["trello", "gmail"]
    },
    "cursor": {
      "instructions_file": ".cursorrules"
    },
    "aider": {
      "config_file": ".aider.conf.yml"
    }
  },
  "fallback_instructions": "INSTRUCTIONS.md"
}
```

**Agent reads:**
1. Check for agent-specific config in `config.json`
2. Load agent-specific instructions if present
3. Fall back to `INSTRUCTIONS.md` if not
4. Merge with base context from `.planning/PROJECT.md`

---

## Confidence Levels

### High Confidence (90%+)

✅ **Markdown as universal instruction format**
- All modern AI agents parse markdown natively
- Established conventions (frontmatter, code blocks, tasks)
- Human-readable for non-technical users

✅ **Directory-based organization (.planning/, templates/, etc.)**
- Clear separation of concerns
- Scales with project complexity
- Easy for agents to navigate

✅ **Git as invisible backend**
- All agents support git operations
- Required for history/audit trail
- Can be fully automated

✅ **YAML frontmatter for metadata**
- Widely supported across tools
- Structured but human-readable
- Easy to parse

### Medium Confidence (60-90%)

⚠️ **Agent-specific config files coexisting**
- Appears to work in practice
- No formal standard for multi-agent support
- Risk: Config drift between agents

⚠️ **JSON for configuration vs YAML**
- JSON more universal for parsing
- YAML more human-friendly
- Both work, preference depends on audience

⚠️ **Shell scripts for automation**
- Works on Mac/Linux
- Windows requires WSL or Git Bash
- May intimidate non-technical users (but hides in docs)

⚠️ **grepai/Ollama for semantic search**
- Promising for token reduction
- Requires additional setup
- Not all agents integrate seamlessly

### Low Confidence (30-60%)

🔶 **MCP server portability**
- Currently Claude Code specific
- No clear path for Cursor/Aider integration
- May need agent-specific integration code

🔶 **Skill/subagent standardization**
- Each agent has different extensibility model
- No universal skill definition format
- Likely requires per-agent implementations

🔶 **OpenCode/Codex instruction patterns**
- Limited documentation on file-based instructions
- Appears to rely more on implicit context
- May need different approach than other agents

---

## Implementation Recommendations

### Phase 1: Universal Foundation
1. **Structure:** Markdown-based with semantic directories
2. **Config:** JSON for machine-readable, Markdown for human-readable
3. **Entry Point:** README.md with clear "Getting Started"
4. **Base Instructions:** INSTRUCTIONS.md that all agents can read

### Phase 2: Agent-Specific Optimizations
1. **Claude Code:** `.claude/instructions.md` + MCP integration docs
2. **Cursor:** `.cursorrules` with full instructions
3. **Aider:** `.aider.conf.yml` for git preferences
4. **Fallback:** Always ensure base instructions work standalone

### Phase 3: Optional Enhancements
1. **Shell Scripts:** Common operations (`./pm new-client`, etc.)
2. **Semantic Search:** grepai setup guide for advanced users
3. **Integrations:** MCP server examples (Trello, Gmail)
4. **Templates:** Rich template library with placeholder conventions

### For Non-Technical Users
1. **Video Tutorial:** Show exact commands to run
2. **Cheat Sheet:** 5-10 common operations with exact syntax
3. **Error Messages:** Helpful, natural language guidance
4. **Examples:** Working reference implementation (marketing agency)

---

## Open Questions

1. **How much agent-specific configuration is worth it?**
   - Tradeoff: Better experience per agent vs maintenance burden
   - Recommendation: Start minimal, add based on user feedback

2. **Should protocols be executable or instructional?**
   - Executable: Shell scripts that call agent
   - Instructional: Markdown that user tells agent to follow
   - Recommendation: Both - scripts call agent with protocol as context

3. **What's the right balance of structure vs flexibility?**
   - Too rigid: Limits customization
   - Too loose: Confusing for new users
   - Recommendation: Opinionated defaults, clear extension points

4. **How to handle agent version differences?**
   - Agents update frequently
   - Instructions may become outdated
   - Recommendation: Version tag instructions, update guidance docs

---

## Next Steps for Stack Validation

1. **Prototype Testing:**
   - Build minimal template with all 3 agents
   - Test same operation across agents
   - Document what works/breaks

2. **User Testing:**
   - Non-technical user runs setup
   - Document friction points
   - Refine based on feedback

3. **Performance Testing:**
   - Measure token usage with/without grepai
   - Test large repos (100+ entities)
   - Validate git invisibility claims

4. **Integration Testing:**
   - MCP servers with Claude Code
   - Cursor with GitHub integration
   - Aider with automatic commits

---

## References

Based on analysis of:
- Current project structure (`/Users/seth/Documents/GitHub/agent-pm`)
- Claude Code conventions (`.claude/`, permissions model)
- Cursor documentation (`.cursorrules` pattern)
- Aider configuration (`.aider.conf.yml`)
- General markdown/git best practices
- AI agent instruction patterns (2025 state)

**Note:** Some recommendations are inferred from agent behavior and community patterns, as formal multi-agent standards are still emerging as of 2025.
