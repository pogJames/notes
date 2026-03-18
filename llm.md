# Claude Code — Core Features Cheatsheet

**What it is:** Claude Code is a programmable agentic platform, not just a coding assistant.  \
Extensions plug into different parts of its loop to add context, connections, and automation.

---

## Architecture at a Glance

```
┌─────────────────────────────────────────────────────────┐
│                     EXTENSION LAYER                     │
│  CLAUDE.md │ Skills │ MCP │ Hooks │ Subagents │ Plugins  │
├─────────────────────────────────────────────────────────┤
│                       CORE LOOP                         │
│         Read → Reason → Act → Observe → Repeat          │
└─────────────────────────────────────────────────────────┘
```

| Extension    | When it loads          | Purpose                            |
|--------------|------------------------|------------------------------------|
| `CLAUDE.md`  | Every session, always  | Persistent project context/rules   |
| **Skills**   | On demand / auto-match | Reusable knowledge & workflows     |
| **MCP**      | Always (background)    | External tools, APIs, databases    |
| **Hooks**    | On lifecycle events    | Deterministic automation           |
| **Subagents**| When spawned           | Isolated sub-tasks                 |
| **Plugins**  | At install time        | Bundled config for distribution    |

---

## CLAUDE.md — Persistent Memory

Claude's always-on context file. Loaded at every session start.

```
CLAUDE.md              ← project root (shared)
~/.claude/CLAUDE.md    ← user-level (personal defaults)
```

**Use for:** code style rules, project conventions, architecture notes, team preferences.

```markdown
# CLAUDE.md example
- Always use TypeScript strict mode
- API routes live in /src/api
- Run `npm test` before committing
```

---

## Skills — Reusable Knowledge & Workflows

Markdown files that encode expertise or procedures. Invoked as slash commands or auto-matched by Claude.

```
.claude/skills/deploy.md    → /deploy
.claude/skills/review.md    → /review
```

**Two types:**
- **Reference skill** — knowledge Claude absorbs (API style guide, architecture docs)
- **Action skill** — step-by-step workflow Claude executes (`/deploy`, `/pr-review`)

**Priority (highest → lowest):** managed → user → project  
**Plugin skills** are namespaced: `/my-plugin:review`

```markdown
# deploy.md
## Description
Deploys the app to staging

## Steps
1. Run `npm run build`
2. Run `npm test`
3. Push to staging branch
4. Monitor logs for 2 minutes
```

---

## MCP — Model Context Protocol

Connects Claude to external services. Think "USB-C for AI."

```json
// .mcp.json (project root, committed to git)
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["@github/mcp-server"]
    }
  }
}
```

```bash
# Add via CLI
claude mcp add --transport http github https://mcp.github.com/mcp \
  --header "Authorization: Bearer $GITHUB_TOKEN"

# List connected servers
/mcp
```

**Popular MCP servers:** GitHub, PostgreSQL, Slack, Jira/Linear, Sentry, Filesystem, Browser  
**⚠ Context cost:** each server consumes tokens. Remove unused ones with `/mcp`.

**Scope priority:** local > project > user

---

## Hooks — Lifecycle Automation

Scripts that fire automatically at specific events. Deterministic — no prompting needed.

```json
// .claude/settings.json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write",
      "hooks": [{ "type": "command", "command": "npm run lint" }]
    }],
    "PreToolUse": [{
      "matcher": "Bash",
      "hooks": [{ "type": "command", "command": "./scripts/safety-check.sh" }]
    }]
  }
}
```

**Hook events:**

| Event            | Fires when…                        |
|------------------|------------------------------------|
| `PreToolUse`     | Before any tool executes           |
| `PostToolUse`    | After any tool executes            |
| `SessionStart`   | Session begins                     |
| `SessionEnd`     | Session ends                       |
| `PrePrompt`      | Before sending prompt to model     |
| `PermissionRequest` | Claude requests a permission    |
| `Compaction`     | Context window compaction occurs   |

**Hook response fields:**
```json
{
  "block": true,           // PreToolUse only — stop the action
  "message": "Reason",     // Shown to user
  "feedback": "Info",      // Non-blocking note
  "suppressOutput": true   // Hide command output
}
```

**Common uses:** auto-format on write, block `rm -rf`, secret detection, Slack notifications, CI triggers.

---

## Subagents — Isolated Sub-tasks

Claude spawns a child agent with its own context window. Results returned as a summary to the parent.

**Why use them:**
- Prevent context pollution (heavy file reads, searches)
- Parallel execution of independent tasks
- Specialized deep-dives without bloating main session

**Subagent config (`.claude/agents/code-reviewer.md`):**
```markdown
---
name: code-reviewer
description: Reviews code for quality and security issues
tools: [Read, Grep, Bash]
---

You are a code review specialist. Analyze the provided files and return
a structured report with findings, severity levels, and suggested fixes.
```

**Scope priority:** managed → CLI flag → project → user → plugin  
**Context:** subagents do NOT inherit your conversation history or invoked skills.

---

## Plugins — Packaged Configurations

Bundle skills + hooks + subagents + MCP servers into one installable unit. Good for team sharing or marketplace distribution.

```
my-plugin/
├── plugin.json          ← metadata & manifest
├── skills/
│   └── deploy.md
├── agents/
│   └── reviewer.md
└── hooks/
    └── lint.sh
```

```bash
# Install from marketplace
claude plugin install ./my-plugin

# Plugin skills are namespaced
/my-plugin:deploy
```

**Use plugins when:** sharing setup across repos, distributing opinionated configs to teammates, or publishing to the community marketplace.

---

## Agent Teams (Feb 2026)

Orchestrate multiple specialized agents from a single lead agent. Configured through the prompt itself, not YAML.

```
"You are the lead. Spawn a security-auditor and a performance-analyzer.
Have them review the auth module independently and report findings."
```

- Teammates inherit lead's permissions and MCP connections
- Fine-tune with hooks or `teams.json`
- Unlike subagents, orchestration logic is described in natural language

---

## Configuration Hierarchy

```
~/.claude/settings.json          ← user-level (personal)
.claude/settings.json            ← project-level (shared)
.claude/settings.local.json      ← local overrides (gitignored)
.mcp.json                        ← MCP servers (project root)
CLAUDE.md                        ← persistent context
```

---

## Slash Commands Quick Reference

```bash
/mcp                  # List connected MCP servers & token costs
/hooks                # Interactive hooks setup
/context              # Show current context usage
/clear                # Clear conversation context
/<skill-name>         # Invoke an action skill
/<plugin>:<skill>     # Invoke a namespaced plugin skill
```

---

## Decision Guide: Which Extension?

```
Need persistent project rules?        → CLAUDE.md
Need repeatable workflows/knowledge?  → Skills (slash commands)
Need to connect to external APIs/DBs? → MCP
Need automatic triggers on events?    → Hooks
Need to isolate heavy sub-tasks?      → Subagents
Need to share config across repos?    → Plugins
Need multi-agent orchestration?       → Agent Teams
```

---

## Extension Interaction Example

```
CLAUDE.md        → "use TypeScript strict, API in /src/api"
MCP (GitHub)     → reads open issue #42
Skill /plan      → generates implementation plan
Subagent         → reads 40 files to map codebase (isolated)
Claude (lead)    → implements the feature
Hook (PostWrite) → runs ESLint automatically
Hook (PostWrite) → runs `tsc --noEmit` type check
MCP (Jira)       → marks ticket as Done
```

---

*Sources: [Claude Code Docs](https://code.claude.com/docs) · [Extend Claude Code](https://code.claude.com/docs/en/features-overview) · Last verified March 2026*
