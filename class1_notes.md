# Claude Code — Class 1 Notes

---

## 1. Vibe Coding (AI-Assisted Development)

Vibe coding means using AI tools to rapidly build, iterate, and refine ideas — especially useful in fast-paced environments like hackathons.

**Why it's useful:**
- Helps non-technical participants turn ideas into working prototypes
- Speeds up development and debugging
- Enables rapid experimentation
- Reduces the need for deep coding expertise

---

## 2. Why Choose Claude?

- **Strong coding capabilities** — handles complex programming tasks effectively
- **Long context understanding** — remembers and works with large codebases
- **Better refactoring** — improves and restructures code efficiently
- **Agentic capabilities** — can take multi-step actions and assist in workflows

---

## 3. Setting Up Claude in VS Code

1. Install Claude CLI or the VS Code extension
2. Open the VS Code terminal
3. Start Claude:
   ```
   claude
   ```
4. A new session begins

---

## 4. Claude Models

| Model | Best For |
|---|---|
| **Opus** | Most powerful — complex reasoning and deep coding (slower, costlier) |
| **Sonnet** | Balanced speed + intelligence — best for everyday development |
| **Haiku** | Fastest and lightest — simple tasks and quick answers |

> Opus = Think & Design &nbsp;|&nbsp; Sonnet = Build & Execute

---

## 5. Sessions

### What is a Session?
A session is a continuous interaction where Claude remembers your conversation, tracks code changes, and maintains context automatically.

> Think of it as a browser tab — your working memory for that task.

### Starting a Session
```
claude
```

### Resuming a Session
```bash
claude --resume              # opens interactive session picker
claude --resume <session-id> # resume a specific session by ID or name
claude -r                    # shorthand for --resume (opens picker)
claude -r <session-id>       # shorthand to resume specific session
```

### Ending a Session
```
/exit
```

### Session Analogy

| Concept | Analogy |
|---|---|
| Session | Browser tab |
| `claude` | Open new tab |
| `--resume` / `-r` | Reopen old tab |
| `/exit` | Close tab |

---

## 6. Hackathon Tips

- Start by describing your idea clearly to Claude
- Ask for: project structure, boilerplate code, step-by-step implementation
- Use it to: debug errors, refactor messy code, add features quickly

---

## 7. Slash Commands

Slash commands start with `/` and let you control Claude Code quickly without writing long prompts.

**Benefits:**
- Trigger actions instantly
- Save time, especially in hackathons
- Reduce repetitive typing
- Give better control over sessions

### Types of Slash Commands

| Type | Description |
|---|---|
| **Built-in** | Pre-installed commands available by default |
| **Custom** | User-defined commands for specific workflows (e.g. `/build-app`, `/fix-bug`) |
| **Skills** | Bundled prompt-based commands that Claude executes (marked below) |
| **MCP Prompts** | Dynamically added by connected MCP servers (`/mcp__<server>__<prompt>`) |

---

### Complete Built-in Command Reference

| Command | Description | Aliases |
|---|---|---|
| `/add-dir <path>` | Add a working directory for file access in the current session | — |
| `/agents` | Manage subagent configurations | — |
| `/autofix-pr [prompt]` | Spawn a web session to watch the current PR and auto-fix CI failures or review comments | — |
| `/branch [name]` | Create a conversation branch at the current point | `/fork` |
| `/btw <question>` | Ask a quick side question without adding it to conversation history | — |
| `/clear` | Start a new conversation with empty context (previous stays resumable) | `/reset`, `/new` |
| `/color [color\|default]` | Set prompt bar color for the session (`red`, `blue`, `green`, etc.) | — |
| `/compact [instructions]` | Free up context by summarizing the conversation | — |
| `/config` | Open Settings UI (model, theme, auto-compact, etc.) | `/settings` |
| `/context` | Visualize context usage as a colored grid with optimization suggestions | — |
| `/copy [N]` | Copy the last (or Nth) assistant response to clipboard | — |
| `/debug [description]` | Enable debug logging for the current session | — |
| `/desktop` | Continue this session in the Claude Code Desktop app (macOS/Windows only) | `/app` |
| `/diff` | Open interactive diff viewer for uncommitted changes and per-turn diffs | — |
| `/doctor` | Diagnose and verify Claude Code installation and settings | — |
| `/effort [level\|auto]` | Set model effort level (`low`, `medium`, `high`, `xhigh`, `max`) | — |
| `/exit` | Exit the CLI | `/quit` |
| `/export [filename]` | Export current conversation as plain text | — |
| `/extra-usage` | Configure extra usage to keep working when rate limits are hit | — |
| `/fast [on\|off]` | Toggle fast mode | — |
| `/feedback` | Submit feedback about Claude Code | `/bug` |
| `/focus` | Toggle focus view (last prompt + tool summary + final response only) | — |
| `/help` | Show help and all available commands | — |
| `/hooks` | View hook configurations for tool events | — |
| `/ide` | Manage IDE integrations and show status | — |
| `/init` | Initialize project with a `CLAUDE.md` guide | — |
| `/insights` | Generate a report analyzing your Claude Code sessions (last ~30 days) | — |
| `/install-github-app` | Set up the Claude GitHub Actions app for a repository | — |
| `/install-slack-app` | Install the Claude Slack app | — |
| `/keybindings` | Open or create keybindings configuration file | — |
| `/login` | Sign in to your Anthropic account | — |
| `/logout` | Sign out from your Anthropic account | — |
| `/mcp` | Manage MCP server connections and OAuth authentication | — |
| `/memory` | Edit `CLAUDE.md` memory files and manage auto-memory | — |
| `/model [model]` | Select or change the AI model | — |
| `/permissions` | Manage allow, ask, and deny rules for tool permissions | `/allowed-tools` |
| `/plan [description]` | Enter plan mode to design an approach before executing | — |
| `/plugin` | Manage Claude Code plugins | — |
| `/powerup` | Discover Claude Code features through interactive lessons | — |
| `/privacy-settings` | View and update privacy settings (Pro/Max plans) | — |
| `/recap` | Generate a one-line summary of the current session | — |
| `/release-notes` | View changelog in an interactive version picker | — |
| `/reload-plugins` | Reload all active plugins without restarting | — |
| `/remote-control` | Make this session available for remote control from claude.ai | `/rc` |
| `/rename [name]` | Rename the current session (auto-generates name if none given) | — |
| `/resume [session]` | Resume a conversation by ID/name, or open session picker | `/continue` |
| `/review [PR]` | Review a pull request locally in the current session | — |
| `/rewind` | Rewind conversation and/or code to a previous point | `/checkpoint`, `/undo` |
| `/sandbox` | Toggle sandbox mode | — |
| `/schedule [description]` | Create, update, list, or run scheduled routines | `/routines` |
| `/security-review` | Analyze pending branch changes for security vulnerabilities | — |
| `/skills` | List available skills | — |
| `/stats` | Alias for `/usage` (opens on Stats tab) | `/usage` |
| `/status` | Open Settings interface showing version, model, account, and connectivity | — |
| `/statusline` | Configure the Claude Code status line | — |
| `/tasks` | List and manage background tasks | `/bashes` |
| `/team-onboarding` | Generate a team onboarding guide from Claude Code usage history | — |
| `/teleport` | Pull a Claude Code web session into this terminal | `/tp` |
| `/terminal-setup` | Configure terminal keybindings (Shift+Enter, etc.) | — |
| `/theme` | Change the color theme | — |
| `/tui [default\|fullscreen]` | Set terminal UI renderer | — |
| `/ultraplan <prompt>` | Draft a plan, review in browser, then execute remotely | — |
| `/ultrareview [PR]` | Run deep multi-agent cloud code review | — |
| `/upgrade` | Open upgrade page for higher plan tier | — |
| `/usage` | Show session cost, plan usage limits, and activity stats | `/cost`, `/stats` |
| `/voice [hold\|tap\|off]` | Toggle voice dictation | — |
| `/web-setup` | Connect GitHub account to Claude Code on the web | — |

> Note: Some commands only appear based on your platform, plan, or environment (e.g. `/desktop` is macOS/Windows only).

---

### Key Commands Explained

**`/compact`** — When your context gets large, this summarizes the conversation to free up space while keeping Claude's understanding intact.

**`/context`** — Shows how much of Claude's context window is used, displayed as a colored grid with warnings if you're running low.

**`/rename [name]`** — Name your session so you can find it easily later with `claude -r`. Without a name, Claude auto-generates one from the conversation.

**`/resume [session]`** — Used inside an active session to switch to a different session without exiting. Works by ID, name, or opens a picker.

**`/btw <question>`** — Ask something off-topic without polluting the main conversation context. Claude answers but it doesn't stay in the session flow.

**`/model`** — Switch between Opus, Sonnet, and Haiku mid-session. Change takes effect immediately.

**`/usage`** — See how many tokens you've used, your plan limits, and session cost.

**`/export [filename]`** — Save the full conversation as a plain text file for documentation or handovers.

**`/permissions`** — Control what actions Claude can take without asking you first (the allowlist). Can also be configured in `settings.json`.

**`/config`** — Opens a UI to change model, theme, auto-compact behavior, and other preferences. Equivalent to editing `settings.json` directly.

---

## 8. Terminal vs In-Session: Resume Commands

| Command | Where Used | What it Does |
|---|---|---|
| `claude -r` | Terminal | Opens session picker to resume a past session |
| `claude -r <id>` | Terminal | Resumes a specific session directly |
| `/resume` | Inside Claude | Opens session picker without exiting current session |
| `/resume <id>` | Inside Claude | Switches to a specific session without exiting |
