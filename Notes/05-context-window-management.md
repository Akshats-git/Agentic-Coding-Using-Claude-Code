# Video 5: Context Window Management in Claude Code

**Duration:** about 35 minutes
**Link:** https://www.youtube.com/watch?v=lN5tLx2_7HQ

## Main idea
The context window is the model's working memory. It is limited. This video explains how it works in Claude Code and how to manage it well.

## Two doubts answered first
1. **The project looks too basic.** A simple project keeps your attention on Claude Code instead of on the features. Complex projects come later.
2. **Why the terminal and not the VS Code plugin.** Claude Code has several access modes such as terminal, desktop app, web, and a VS Code plugin. The terminal is the original mode and power users work there. Some features like memory and hooks only work in the terminal. The GUI is useful for things like viewing a git diff.

## What is context
Context is all the information needed to understand something correctly. In programming context comes from many places.
- The full codebase.
- The PRD or spec document with the requirements.
- Jira tickets or GitHub issues.
- Slack messages and team discussions.
- Your previous chats with the AI coding tool.
- The Git repository history.

## What is a context window
The context window is the maximum amount of context a model can hold at once. It is measured in tokens. An LLM cannot handle infinite context, so every model has a hard limit.

## Key facts about Claude Code's context window
1. The context window is around 200k tokens for Sonnet models. The newer Opus 4.6 is said to have 1 million tokens.
2. Every new session gets a fresh context window.
3. Tokens are used by both your messages and Claude's replies. Input tokens are your prompts, pasted code, and images. Output tokens are Claude's replies, generated code, and tool outputs.
4. Claude's output is roughly six times larger than your input, so replies fill the window much faster than your prompts do.

## Why long sessions are expensive
LLMs are stateless. They have no memory. Every new message resends the entire conversation history.

Example with 100 tokens per message and 100 per reply:
- Turn 1 uses 200 tokens.
- Turn 2 uses 400 tokens.
- Turn 3 uses 600 tokens.
- By turn 10 you have used 2000 tokens.

**The lesson:** building four features in one long session costs about four times more tokens than building each feature in its own session.

## What already fills the context window
You get 200k tokens but only around 150k are usable. It is like buying 8 GB of RAM and getting about 6 GB of usable RAM.

| Item | Rough size |
|---|---|
| System prompt | about 6000 tokens |
| Tool schemas | about 8000 tokens |
| CLAUDE.md file | small but always loaded |
| Conversation history | grows over time |
| Tool outputs | grows over time |
| MCP tool schemas | depends on your setup |
| Skills definitions | small |
| Auto compaction buffer | about 33,000 tokens reserved |

Run `/context` inside a session to see this breakdown live on your own machine.

## Why the context window matters
1. **Cost.** You pay per token. Managing context means managing cost.
2. **Workflow.** One feature per session uses about four times fewer tokens than four features in one session.
3. **Quality.** Response quality drops as the window fills. At 120k or 130k tokens used the answers are noticeably worse than at 20k or 30k.

## How Claude Code solves it: auto compaction
- It triggers automatically when usage reaches about 75 to 92 percent of the window.
- Claude summarises the whole conversation history.
- The summary is stored in the reserved 33k token buffer.
- The old conversation history is freed.
- This repeats until the 33k buffer itself is full. After that Claude refuses to continue and you must start a new session.

## How you solve it: /compact
`/compact` does the same thing as auto compaction but you control when it happens.

**Why manual is better**
- Auto compaction can fire in the middle of a feature.
- Summaries are lossy, so some details are always lost.
- Losing details mid feature can break your work.

**How to use it**
1. Run `/context` from time to time to check usage.
2. When you reach about 70 to 75 percent, run `/compact`.
3. Make sure you are not in the middle of an important task.
4. Press Ctrl and O to view the generated summary.

In the demo the messages dropped from 7.5k tokens to 3.4k tokens after one compact.

## Other escape routes
- **Trigger a subagent.** Each subagent has its own fresh 200k token context window. Hand the next task to a subagent. Subagents also return only a summary to the main agent, so they save tokens.
- **Run `/clear`.** This deletes the conversation and takes you back to the start of the session.
- **Start a new session.** This is cleaner and is the instructor's preference.

## Best practices
1. Develop one feature in one session.
2. Use `/compact` proactively and not reactively.
3. Write focused and specific prompts. Vague prompts waste tokens.
4. Use subagents for isolated and exploratory work.
5. Create a `.claudeignore` file. It works like `.gitignore`. List large files such as build files and virtual environment files so they never enter the context. Claude does not follow it perfectly yet but it is expected to improve.
