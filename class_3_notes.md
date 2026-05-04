# Class 3 Notes — Claude Code Deep Dive
**Date:** 2026-05-04  
**Project:** Spendly Expense Tracker  
**Topics:** /init, CLAUDE.md types, .claude folder, Rules, Agents, Skills, Best Practices, Auto-Memory

---

## 1. The `/init` Command

`/init` is a built-in Claude Code skill that **automatically generates a `CLAUDE.md` file** by analyzing your codebase.

### What it does:
1. Reads key files — `package.json`, `README.md`, config files, entry points, directory structure
2. Extracts: build/dev/test commands, architecture patterns, project conventions
3. Writes a `CLAUDE.md` that future Claude Code sessions load automatically

### Why it matters:
Every new Claude Code session reads `CLAUDE.md` first — so Claude already "knows" the stack, commands, and architecture without you re-explaining from scratch.

### Ways to improve the generated CLAUDE.md:
- Add **gotchas & constraints** — e.g., "always activate venv before running"
- Add **decisions already made** — e.g., "no ORM, raw SQLite only"
- Add **what NOT to do** — e.g., "don't add a JS framework, keep it vanilla"
- Add **step-by-step implementation order** for guided projects
- Add **database schema** once implemented
- Remove anything Claude can figure out by reading the code

---

## 2. The `.claude` Folder

Claude Code uses **two `.claude` folders** — project-level and global.

### Project-level: `D:\CLAUDE\expense-tracker\.claude\`
| File | Purpose |
|---|---|
| `settings.json` | Project-scoped permissions and hooks — **committed to git, shared with team** |
| `settings.local.json` | Personal local overrides — **gitignored, not shared** |
| `commands/` | Custom slash commands scoped to this project |
| `rules/` | Modular instruction files with optional path-scoping |
| `skills/` | Reusable prompt-based skills (slash commands) |
| `agents/` | Custom subagent definitions |

### Global: `C:\Users\Adari Shanmukh\.claude\`
| File/Folder | Purpose |
|---|---|
| `settings.json` | Permissions that apply across ALL projects |
| `CLAUDE.md` | Global instructions loaded in every session |
| `projects/` | Per-project auto-memory files |
| `keybindings.json` | Custom keyboard shortcuts |

**Key distinction:** Project `settings.json` is shared via git. `settings.local.json` is personal and gitignored.

---

## 3. Types of CLAUDE.md Files

Claude Code supports a **hierarchy** of CLAUDE.md files — they all stack together, none override others.

### Load order (all concatenated):
```
Global CLAUDE.md (~/.claude/CLAUDE.md)        <- always loaded, every project
    +
Project root CLAUDE.md (expense-tracker/)     <- loaded for this repo
    +
Sub-directory CLAUDE.md (e.g., database/)     <- loaded only when working in that folder
    +
CLAUDE.local.md (any level)                   <- personal, gitignored
```

### The 4 Types:

**1. Global CLAUDE.md** — `~/.claude/CLAUDE.md`
- Applies to every Claude Code session regardless of project
- Use for: personal style, preferences, things you always/never want Claude to do
- Example: "I prefer concise responses. Always use Python type hints."

**2. Project Root CLAUDE.md** — `expense-tracker/CLAUDE.md`
- Applies to any session opened in this project
- Committed to git — shared with teammates
- Use for: build commands, architecture overview, project conventions

**3. Sub-directory CLAUDE.md** — `expense-tracker/database/CLAUDE.md`
- Applies only when Claude works in that specific folder
- Use for: module-specific rules (e.g., SQLite query conventions, Jinja2 patterns)

**4. CLAUDE.local.md** — any level, gitignored
- Personal override at any level
- Use for: local paths, personal API keys, dev shortcuts not for sharing

---

## 4. Rules

Rules are **modular, topic-specific instruction files** in `.claude/rules/`. A structured alternative to one giant CLAUDE.md.

### Key difference from CLAUDE.md:
Rules support `paths:` frontmatter — they **only load when Claude works with matching files**, saving context space.

```markdown
---
paths:
  - "database/**/*.py"
---

Always use parameterized queries. Never build SQL strings with f-strings.
```

This rule only loads when Claude touches database files. CLAUDE.md always loads everything.

### Structure example:
```
.claude/
└── rules/
    ├── code-style.md        <- always loaded
    ├── testing.md           <- always loaded
    ├── api-design.md        <- only loads for src/api/**
    └── database.md          <- only loads for database/**
```

### Where rules live:
- Project: `.claude/rules/`
- Personal (all projects): `~/.claude/rules/`

---

## 5. Agents (Subagents)

Subagents are **specialized Claude instances** that run in their own isolated context window. Claude delegates tasks to them to keep the main conversation clean.

### Built-in agent types:

| Type | Tools | Best for |
|---|---|---|
| `Explore` | Read-only (Glob, Grep, Read) | Codebase research |
| `Plan` | No write tools | Architecture planning |
| `general-purpose` | All tools | Default, mixed tasks |

### Custom agents:
Create `.claude/agents/<name>/AGENT.md` with a system prompt and tool restrictions.

### When to use subagents vs main conversation:

| Use subagent | Use main conversation |
|---|---|
| Exploring many files you won't reference again | Small focused task |
| Running tasks in parallel simultaneously | Tight feedback loop needed |
| Context is filling up | Intermediate steps matter |
| Isolating heavy investigation | Quick question or correction |

### Parallel agents example:
```
Use subagents to simultaneously:
1. Security review src/auth/
2. Check test coverage
3. Flag performance issues
```
Three agents run at once, report back clean summaries.

---

## 6. Skills

Skills are **reusable, on-demand procedures** that map to slash commands. They only consume context when invoked.

### How they map to commands:
- Directory name becomes the command name
- `~/.claude/skills/summarize-changes/SKILL.md` becomes `/summarize-changes`
- `.claude/skills/add-route/SKILL.md` becomes `/add-route` (project only)

### Where skills live:
| Location | Path | Scope |
|---|---|---|
| Personal | `~/.claude/skills/<name>/SKILL.md` | All projects |
| Project | `.claude/skills/<name>/SKILL.md` | This project only |

### Creating a custom skill:
```markdown
---
description: Summarize uncommitted changes and flag risks
---

## Changes
!`git diff HEAD`

Summarize in 3 bullets. Flag missing error handling or hardcoded values.
```
The `!` prefix runs the command live — always fresh data injected before Claude sees the skill.

### Key frontmatter options:
```yaml
disable-model-invocation: true  # Only YOU can trigger it (not Claude auto)
allowed-tools: Bash(git *)      # Pre-approve tools, skip permission prompts
context: fork                   # Run in isolated subagent
paths:                          # Auto-load only for these files
  - "src/api/**"
arguments: [issue, branch]      # Named args: /fix-issue 123 -> $issue = 123
```

### Skills vs CLAUDE.md vs Rules:
| Feature | CLAUDE.md / Rules | Skills |
|---|---|---|
| Loading | At session start | Only when invoked |
| Context cost | Upfront | On-demand |
| Best for | Persistent guidance | Repeatable workflows |

---

## 7. Good Practices

### CLAUDE.md
- Keep under **200 lines** — long files cause rules to get ignored
- Include only what Claude can't infer from the code
- Test it: if Claude still does the wrong thing after adding a rule, prune the file
- Use `.claude/rules/` with `paths:` for file-specific rules

### Context Management
- **`/clear`** between unrelated tasks — fresh context beats polluted long sessions
- **`/compact`** mid-session: `/compact Focus on the API changes`
- **`Esc+Esc`** to rewind to any earlier checkpoint
- Delegate heavy file exploration to subagents
- **`/context`** to see what's consuming space

### Prompting — Always give Claude something to verify against:
| Without | With |
|---|---|
| "Fix the login bug" | "Users fail login after timeout. Check src/auth/. Write a failing test first, then fix." |
| "Make it look better" | "[paste screenshot] Match this design. Compare before/after and list differences." |

### Explore -> Plan -> Implement flow for big tasks:
1. `Shift+Tab` twice -> **Plan Mode** (read-only, explore without changing anything)
2. Ask Claude to write a detailed plan, review it
3. Exit plan mode, implement
4. Verify with tests/screenshots
5. Fresh session or subagent reviews the result (Writer/Reviewer pattern)

### Permissions & Security:
```json
{
  "permissions": {
    "allow": ["Bash(git *)", "Bash(python *)"],
    "deny": ["Edit(/secrets/**)"]
  }
}
```
Add allowlists so you stop approving the same commands repeatedly.

### Hooks — for things that MUST happen:
```json
{
  "hooks": {
    "PostToolUse": [{
      "condition": { "toolName": "Edit" },
      "command": "python -m py_compile {filePath}"
    }]
  }
}
```
Hooks **guarantee execution**. CLAUDE.md instructions are advisory. Hooks always run.

### Common mistakes to avoid:
| Mistake | Fix |
|---|---|
| One giant session for everything | `/clear` between unrelated tasks |
| Correcting Claude 3+ times in a row | `/clear` and restart with better prompt |
| CLAUDE.md keeps growing | Prune it; move reference docs to skills |
| No verification criteria given | Always provide tests, screenshots, or expected outputs |
| Approving the same command repeatedly | Add it to the permissions allowlist |

---

## 8. Auto-Memory

Auto-memory is Claude Code's system for **persisting information across separate conversations** so you never start completely blank.

### Where it lives:
```
C:\Users\Adari Shanmukh\.claude\projects\D--CLAUDE-expense-tracker\memory\
```

### Two key files:
- **`MEMORY.md`** — index file, always loaded at session start (first 200 lines / 25KB)
- **Individual memory files** (e.g., `user_role.md`, `feedback_responses.md`) — loaded on demand

### How it loads:
```
New session starts
      |
MEMORY.md index loads automatically
      |
Claude reads pointers -> loads relevant detail files
      |
Claude already "knows" your preferences and project context
```

### 4 Types of memories:
| Type | What it stores | When to save |
|---|---|---|
| **user** | Your role, skill level, background | When Claude learns about you |
| **feedback** | How you want Claude to behave | After corrections AND confirmations |
| **project** | Goals, decisions, constraints, deadlines | When project decisions are made |
| **reference** | Where things live externally | When you mention external resources |

### What TO save:
- Your background and skill level
- Preferences (response style, formatting)
- Architectural decisions — especially WHY ("no ORM — keep it simple for learning")
- Constraints ("can't use paid APIs")
- Past mistakes Claude made

### What NOT to save:
- File paths, function names (just read the file)
- Git history (use `git log`)
- Architecture visible in the code
- Current task progress (use Tasks for that)
- Things already in CLAUDE.md

### Memory vs CLAUDE.md:
| | Auto-memory | CLAUDE.md |
|---|---|---|
| Who writes it | Claude automatically | You manually |
| What goes in it | Personal preferences, decisions | Project commands, architecture |
| Shared with team | No — personal only | Yes — committed to git |
| When loaded | Relevant entries on demand | Everything every session |

### How to explicitly save/recall:
```
Remember: we're not using SQLAlchemy — raw SQLite only
Remember: I prefer short responses with no emojis
Forget what you saved about X
What do you remember about this project?
```

### Golden rule for memory:
If a memory names a file, function, or flag — **verify it still exists before acting on it**.
Memory is a snapshot of the past, not a live view. If memory conflicts with current code, trust the code.

---

## 9. Quick Reference — When to Use What

| Need | Use |
|---|---|
| Rules every session, whole team | `CLAUDE.md` |
| Rules for specific files only | `.claude/rules/` with `paths:` |
| Personal style across all projects | `~/.claude/CLAUDE.md` |
| Repeatable workflow / slash command | Skill |
| Must-happen automation | Hook |
| Isolate heavy exploration | Subagent |
| Persist info across sessions | Auto-memory |
| Track current session work | Tasks |
| Personal project overrides | `CLAUDE.local.md` |

---

## 10. Spendly-Specific Takeaways

Memory worth saving explicitly for this project:
```
Remember: Spendly uses raw SQLite, no ORM — intentional for learning
Remember: routes follow step-numbered placeholder pattern in app.py
Remember: Indian Rupee (Rs.) is the currency throughout the app
Remember: this is a learning project — explain things with beginner context
```

CLAUDE.md improvements to make later:
- Add database schema once implemented
- Add the implementation order (Step 3 -> 4 -> 7 -> 8 -> 9)
- Add `database/CLAUDE.md` with SQLite query conventions
- Add `CLAUDE.local.md` for personal venv path
