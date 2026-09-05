# Video 6: CLAUDE.md, the .claude Folder, and Auto Memory

**Duration:** about 47 minutes
**Link:** https://www.youtube.com/watch?v=QzA12C5NsjU

## Main idea
This is a theory video. It covers the CLAUDE.md file, the `.claude` folder, and the auto memory feature. These concepts are used in every video after this one.

## Why CLAUDE.md exists
LLMs have no memory. Claude Code is built on LLMs, so it does not remember your past sessions.

Without a memory file you have to explain the whole project again in every new session.
- What is the database setup.
- Which frontend libraries you use.
- Which backend libraries you use.
- What your coding conventions are.

This is tiring and error prone. If you forget a detail the generated code becomes inconsistent. Small projects survive this. Big projects do not.

## What CLAUDE.md is
CLAUDE.md is a special project level instruction file used by Claude Code to guide how it behaves while working on your codebase.

- Think of it as a persistent system prompt.
- It is a plain Markdown file.
- It sits in your project directory.
- Claude Code loads it automatically at the start of every session.

## Two ways to create it
**Method 1: manual.** Create a file named `CLAUDE.md` in capital letters in your project directory and write the details yourself.

**Method 2: the `/init` command.** Claude Code scans your codebase, analyses it, and generates the file for you.

The instructor prefers `/init` because:
- You may be working on someone else's codebase and not know it on day one.
- A large codebase has patterns you may not spot manually.
- You may not know the right format if this is your first CLAUDE.md.
- It is much faster.

Most people generate with `/init` and then edit the result.

## How /init works behind the scenes
1. Claude Code starts an internal agent.
2. The agent scans high signal config files first such as `package.json`, `requirements.txt`, and `README`.
3. It reads the whole directory tree structure.
4. It studies the tech stack, folder layout, and naming conventions.
5. It writes a `CLAUDE.md` file in the project root.

**Important:** the generated file is only about 30 percent useful. The other 70 percent is your job. Workflows, constraints, what to avoid, deployment steps, and naming conventions all have to come from you.

## What a good CLAUDE.md should contain
1. **Project overview.** A short description so Claude immediately knows what it is building. Example: "This is a FastAPI backend for a health tracking application that stores patient BMI records and exposes CRUD APIs."
2. **Architecture.** How the codebase is structured and where things belong. Example: routes live in `routes/`, business logic lives in `services/`, schemas live in `schemas/`.
3. **Coding style.** The conventions your whole team follows. Example: use type hints in Python, prefer Pydantic models, keep functions small and focused.
4. **Preferred libraries and tools.** Tell Claude which frameworks it may use and not to go outside them.
5. **Commands.** The exact commands to install, run, test, and deploy the project.
6. **Critical rules.** Warnings, edge cases, and things to avoid. Example: do not touch `database.py` unless needed, do not generate patient IDs yourself.

The instructor also added a **development roadmap** listing all future routes with a status column. This gives Claude a clear plan and makes the whole workflow more streamlined across sessions.

## The .claude folder
The `.claude` folder is a local configuration directory that controls how Claude Code behaves. Think of it as Claude Code's toolbox.

**It stores:**
- `settings.json` for tool permissions.
- `settings.local.json` for personal project level settings that are not shared.
- `commands/` for your custom slash commands.
- `rules/` for split rule files.
- `skills/` for skill markdown files.
- `agents/` for your subagents.

## Two kinds of .claude folders
| | Project level | Global or user level |
|---|---|---|
| Location | Project root directory | Home directory |
| Scope | One project | Every project on your machine |
| Shared with team | Yes, it lives in the repo | No, it stays on your machine |
| Used for | Project specific commands, workflows, and settings | Personal commands you want everywhere |

The global folder also has a `projects/` directory where each project gets its own folder holding that project's sessions and memory.

## Five places a CLAUDE.md can live
1. **Project root.** The standard location. Created by `/init`.
2. **Inside the project's `.claude` folder.** There is no behaviour difference from the root. Some people prefer keeping all config in one place.
3. **`CLAUDE.local.md` in the project.** For your personal project level preferences. It is automatically git ignored so your private tricks never reach the repo.
4. **In the home directory `.claude` folder.** For personal preferences that apply to every project such as your coding style defaults, preferred tools, and general working style.
5. **Inside any subfolder of the project.** Useful for large repositories. Claude Code recurses upward from the current working directory and reads any CLAUDE.md files it finds.

**Important:** only the root CLAUDE.md loads automatically in every session. A subfolder CLAUDE.md loads only when Claude is working inside that folder.

## Best practices
1. Start with `/init` and then remove what is not useful, instead of writing the file from scratch.
2. Commit every change to CLAUDE.md with Git.
3. Only put universally applicable things in it. Anything specific to one feature does not belong there.
4. You can write `IMPORTANT` before a line to mark it as critical. Use this sparingly. The rule is: if everything is important then nothing is.
5. Keep the file under 200 lines. As the instruction count grows, Claude's instruction following quality drops. This is true for all LLMs. Around 200 to 300 lines is the accepted limit.
6. Rule of thumb for trimming: ask "if I remove this line, will Claude start making mistakes?" If the answer is no then remove it.
7. Treat CLAUDE.md as a living document. Refresh it after every feature. Add new things and remove old ones.
8. **Codify repeated mistakes.** If Claude keeps making the same error, do not just correct it. Also tell Claude to add that instruction to CLAUDE.md.
9. Audit it periodically for instruction drift. Old instructions can become redundant or even harmful. Review weekly or monthly.

## Three ways to keep the file under 200 lines
1. **Split into the `rules/` folder.** Put topic specific files inside `.claude/rules/` such as `code-style.md`, `testing.md`, and `security.md`. These do not load automatically. Claude loads them only when needed, which is a kind of lazy loading. It also improves maintainability.
2. **Use imports.** Load another file from inside CLAUDE.md instead of writing everything in one place.
3. **Create CLAUDE.md files in subdirectories.** One for the frontend folder, one for the backend folder, and one for the database folder.

## Auto memory
Auto memory is a persistent directory where Claude records learnings, patterns, and insights as it works.

**How it works**
- Claude silently observes your work across sessions.
- When it notices something meaningful it saves it to a file called `memory.md`.
- Example: your project tracks expenses in INR and not USD, so Claude saves that.
- The file loads at the start of every new session along with CLAUDE.md.

**Where the file lives**
`~/.claude/projects/<your project>/memory/memory.md`

**Important limit:** only the top 200 lines of `memory.md` load in a new session. Keep the file managed.

## The /memory command
Run `/memory` to see three options.
1. **Project memory** opens your project's CLAUDE.md.
2. **User memory** opens the CLAUDE.md in your home directory.
3. **Open auto memory folder** opens `memory.md`.

## How CLAUDE.md and memory.md are related
Both are persistent memory files that load in every new session. The only real difference is who writes them.
- CLAUDE.md is written by the programmer.
- memory.md is written by Claude.

## Creating memories yourself
You do not have to wait for Claude. At the end of a long session you can prompt: "Based on whatever we discussed and developed in this session, update your memory.md file."

In the demo the instructor typed "update your memory files, we use INR and not USD" and Claude saved it immediately.
