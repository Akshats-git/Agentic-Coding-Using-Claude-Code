# Agentic Coding using Claude Code, Notes

Notes for the CampusX playlist **Agentic Coding using Claude Code** by Nitesh.

**Playlist:** https://youtube.com/playlist?list=PLKnIA16_RmvaYH3poI0oJvbDF4zEvpq8W
**Videos:** 15

## The project built across the playlist
An expense tracking website called **Spendly**. The stack is Python and Flask for the backend, SQLite for the database, Jinja2 templates with vanilla CSS and vanilla JavaScript for the frontend, and pytest for testing. Git and GitHub are used throughout.

## Index

| # | Topic | Notes | Duration |
|---|---|---|---|
| 1 | Playlist introduction, vibe coding vs agentic coding | [01-playlist-introduction.md](01-playlist-introduction.md) | 17 min |
| 2 | Setup, the starter project, and running Claude Code for free | [02-setup-and-free-usage.md](02-setup-and-free-usage.md) | 27 min |
| 3 | Slash commands, sessions, models, and permissions | [03-slash-commands.md](03-slash-commands.md) | 31 min |
| 4 | Making code changes and using images as context | [04-making-code-changes-and-images.md](04-making-code-changes-and-images.md) | 22 min |
| 5 | Context window management | [05-context-window-management.md](05-context-window-management.md) | 35 min |
| 6 | CLAUDE.md, the .claude folder, and auto memory | [06-claude-md-file.md](06-claude-md-file.md) | 47 min |
| 7 | Spec driven development | [07-spec-driven-development.md](07-spec-driven-development.md) | 28 min |
| 8 | Plan mode and ultraplan | [08-plan-mode-and-ultraplan.md](08-plan-mode-and-ultraplan.md) | 38 min |
| 9 | Custom slash commands | [09-custom-slash-commands.md](09-custom-slash-commands.md) | 46 min |
| 10 | Skills | [10-skills.md](10-skills.md) | 50 min |
| 11 | Subagents, the theory | [11-subagents-theory.md](11-subagents-theory.md) | 48 min |
| 12 | Custom subagents | [12-custom-subagents.md](12-custom-subagents.md) | 47 min |
| 13 | MCP servers | [13-mcp-servers.md](13-mcp-servers.md) | 55 min |
| 14 | Hooks | [14-hooks.md](14-hooks.md) | 65 min |
| 15 | Plugins and deployment | [15-plugins-and-deployment.md](15-plugins-and-deployment.md) | 35 min |

## How the playlist is structured
- **Videos 1 to 4** cover setup and basic usage.
- **Videos 5 to 7** are the three conceptual foundations: context window, CLAUDE.md, and spec driven development.
- **Videos 8 to 15** build the project feature by feature while introducing one major Claude Code concept per video.

## The final workflow taught by the playlist
1. `/create-spec <number> <feature-name>` creates the branch and the spec document.
2. Review the spec.
3. Plan mode generates the implementation plan.
4. Review the plan and implement it.
5. `/test-feature <spec-name>` runs the test writer and test runner subagents.
6. `/code-review-feature <spec-name>` runs the security and quality reviewer subagents in parallel.
7. `/ship-feature` commits, pushes, creates and merges the pull request, deletes the branch, and returns to main.

## Key ideas at a glance

**Context window.** Around 200k tokens, of which about 150k are usable. One feature per session. Use `/context` to check and `/compact` proactively.

**CLAUDE.md.** A persistent system prompt for your project. Generate it with `/init`, keep it under 200 lines, and treat it as a living document.

**Spec driven development.** Write the specification before any code. The spec is the single source of truth. This is what separates agentic coding from vibe coding.

**Skills.** Folders with a `SKILL.md` that give Claude specialised expertise. They load on demand through progressive disclosure. Commands and skills have now been merged by Anthropic.

**Subagents.** Specialised assistants with their own isolated context windows. They do the heavy work separately and return only a summary.

**MCP.** Connect external tools and services to Claude Code. Keep the number of servers minimal so they do not eat your context.

**Hooks.** Scripts that run automatically at specific lifecycle events. They bring determinism to a probabilistic system where CLAUDE.md instructions only work about 98 percent of the time.

**Plugins.** Packages that bundle skills, subagents, commands, hooks, and MCP servers so a whole workflow can be shared and installed in one step.
