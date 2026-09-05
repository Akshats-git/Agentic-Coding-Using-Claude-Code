# Video 3: Slash Commands in Claude Code

**Duration:** about 31 minutes
**Link:** https://www.youtube.com/watch?v=eW9FADWxS1k

## Main idea
Slash commands are shortcuts you type inside a Claude Code session. They start with a slash and trigger a predefined action or workflow. You do not have to write a long prompt every time.

## Why they exist
Programmers repeat the same requests again and again. The Claude Code team turned those repeatable patterns into single commands. One word can now run a whole workflow.

## Two types of slash commands
1. **Built in commands.** They come with Claude Code by default.
2. **Custom slash commands.** You create them for your own repeated workflows.

## Sessions
A session is one conversation with Claude Code.
- It starts when you run `claude`.
- It ends when you run `/exit`.
- Every session has a unique ID.
- A session stores the full message history including your prompts, Claude replies, and tool messages.
- Sessions are saved automatically and can be resumed later even after closing the terminal.

**Resuming a session**
- `claude -r` shows a list of past conversations. Use the arrow keys to pick one and press enter.
- `/resume` switches to another session from inside a running session.

## Good practices for sessions
1. **One task per session.** Create a new session for each feature. This keeps tasks separated and keeps the context window clean.
2. **Rename the session right after starting it.** If you do not, Claude names it after your first question, which is not useful. Name it after the feature you are building.
3. **Commit often.** Create a Git commit at every milestone inside a session.

## Session related commands
| Command | What it does |
|---|---|
| `/exit` | Closes the current session |
| `/resume` | Switches to another saved session |
| `/rename <name>` | Renames the current session |
| `/export <file.md>` | Saves the whole conversation as a file in your project |

**Why export matters:** during a big refactor you can feed the exported file back to Claude as context.

## The /btw command
`/btw` stands for by the way.
- It lets you ask a side question while Claude is working on your main task.
- The question and its answer do not become part of your conversation history.
- Your main context stays clean.
- Press space after reading the answer to remove it.

Example: Claude is writing your login feature and you suddenly want to know what the Jinja templating engine is. Ask it with `/btw`.

## Login and account commands
- `/logout` logs you out of your Claude Code account. Useful when switching between a personal account and a company account.
- `/login` starts the login flow again.
- On login you choose a theme and then a login method. The options are a Claude subscription account, an Anthropic Console account with API access, or a third party platform.

## Models in Claude Code
| Model | Strength | When to use |
|---|---|---|
| Opus | Most powerful and most expensive | Complex tasks, planning, architecture |
| Sonnet | Balanced speed, quality, and cost. Usually the default | Everyday coding |
| Haiku | Fastest and cheapest | Simple repetitive tasks |

**The power user pattern**
- Use Opus for the planning phase where you think through architecture, write specs, and make decisions.
- Switch to Sonnet for the implementation phase where you only need reliable code generation.
- This playlist follows the same pattern.

Use `/model` to switch between models.

## Usage tracking
- `/usage` shows how much of your plan you have used.
- Usage is tracked at two levels. One is the current session and the other is a weekly limit.
- Opus burns through the limits much faster than Sonnet or Haiku.
- Check `/usage` while developing so you know how much is left.
- `/extra-usage` lets you top up your account with 5 or 10 dollars instead of waiting for the reset.

## Insight commands
- `/stats` shows usage statistics such as tokens used, models used, number of sessions, active days, longest session, and current streak.
- `/insights` generates a detailed HTML report about how you use Claude Code and how you can improve. Run it after about 10 to 15 sessions.

## /config
Opens a settings table where you can change Claude Code behaviour. Examples include turning on thinking mode for step by step reasoning, turning verbose output on or off, showing a terminal progress bar, and changing the language.

## /permissions
Claude Code is an agent built on top of a large language model. The model is the brain and tools are the hands.

**Built in tools**
- Read tool to read files on your system.
- Write tool to write or edit files.
- Bash tool to run terminal commands.
- Web search tool to fetch documentation from the internet.
- You can add your own tools using MCP.

By default Claude asks permission before using a tool. Repeated permission prompts get frustrating, so you can set rules.

**The four tabs**
- **Allow:** tools that run without asking.
- **Ask:** tools that always ask first.
- **Deny:** tools that can never be used.
- **Workspace:** workspace level rules.

**How to add a rule**
1. Run `/permissions`.
2. Click add a new rule.
3. Type the tool name, for example `WebSearch`.
4. For a shell command use bracket form, for example `Bash(git init)`.
5. Choose where to save it.

**Three save locations**
1. **Local project settings.** Applies only to you in this project.
2. **Global project settings.** Shared with everyone who works on the repository.
3. **User settings.** Applies to every project on your machine.

Allowed rules are stored in `.claude/settings.local.json` inside your project under `permissions` and `allow`.

**Warning:** never allow a command that should not run without your approval.

## Two more commands
- `/theme` changes the look of Claude Code. Dark mode, light mode, and a few more options.
- `/voice` turns on voice mode. Press and hold the space bar and speak your prompt instead of typing it. Run it again to disable.

## Tip
Type `/` and scroll with the arrow keys to see the full list of slash commands with descriptions. Stretch the terminal window if the descriptions are cut off. You do not need to memorise all of them.
