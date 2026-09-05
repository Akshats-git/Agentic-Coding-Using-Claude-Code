# Video 14: Hooks in Claude Code, Full Theory and Practical Use

**Duration:** about 65 minutes
**Link:** https://www.youtube.com/watch?v=oo1oADOiVmM

## Main idea
Hooks let you enforce rules on Claude Code's behaviour. This video follows the why, what, and how structure.

## Understanding Claude Code more deeply
**The user facing definition.** Claude Code is a terminal based AI coding agent. It is a Claude LLM with a layer of tools and memory on top. The tools are Read, Write, and Bash. Memory is added because LLMs are stateless. Its core feature is that it is agentic, meaning it pursues a task autonomously and only talks to you when it needs you.

**The system design definition.** Claude Code is a **coding harness**.

## What a harness is
A harness is a set of straps and equipment used to control and direct the power of something strong.

**The horse analogy.** A horse has speed, agility, and pulling power. You cannot just tie a cart to a horse with rope. When the horse runs fast the cart must stay stable and the people inside must not get hurt. So you build a whole system of straps and equipment that connects the horse to the cart properly.

**The core idea:** raw power becomes useful only when controlled through a structured interface.

## What a coding harness is
A coding harness is a piece of software used to convert a raw LLM into a reliable software engineering system.

**The LLM is the horse.** It has raw power, knowledge, and intelligence. It also has problems.
- It is unpredictable and can hallucinate.
- It is stateless and does not remember past interactions.
- It is non deterministic, so the same prompt can give different outputs.
- It is disconnected from the real world. It only understands text.
- It is unable to act safely on its own.

**What the coding harness does**
- Reads your file system so it can give the LLM context.
- Displays terminal output.
- Manages your conversation history and stores sessions.
- Tracks context window usage and provides `/compact` and `/clear`.
- Sends API requests to Anthropic, because the LLM lives on their servers.
- Parses and executes the model's tool calls.
- Implements the safety and permission module that asks you before running commands.
- Handles memory management, CLAUDE.md, and subagent memory files.
- Provides all the slash commands.
- Spawns multiple subagents in parallel.
- Provides extensibility through MCP and plugins.

## How the harness and the LLM work together
**Example:** you type "Explain the project".
1. The coding harness picks up the prompt plus CLAUDE.md and sends it via API to the LLM on Anthropic's servers.
2. The LLM generates a command: "I want to read app.py".
3. The coding harness receives that text command, opens `app.py`, copies its content, and sends it back via API.
4. The LLM now has the file content.

**The analogy:** mind, brain, and body. The LLM is the brain where the thought happens. The coding harness is the body and nervous system that turns the thought into action.

## Side note: harness engineering
The term harness is becoming popular and a new field called harness engineering is emerging. Other harnesses include:
- **OpenCLA.** A personal agent harness, not a coding one. It runs long running tasks and connects to things like WhatsApp. It went viral about a month ago.
- **Hermes Agent.** Also a personal agent harness. Its advantage is that it is self learning. It stores how it completed a task and learns from it each time, adapting to you over time.
- **Pi.** A lightweight coding harness like Claude Code.

## The problem hooks solve
The relationship between the LLM and the coding harness is a boss and employee relationship.
- **The LLM is the boss.** It gives the tasks.
- **The coding harness is the employee.** It faithfully executes whatever instruction it gets.
- **The harness is deterministic.** Same input, same output, every time, like any software system.
- **The LLM is probabilistic.** The same input does not guarantee the same output. The boss is a bit moody.

**The danger.** The LLM might order the harness to:
- Delete some files.
- Make changes to the `.env` file where your API keys are.
- Write f-string SQL queries instead of parameterised queries.

The harness will blindly and faithfully execute all of it, every time.

## Why CLAUDE.md instructions are not enough
You do write these rules in CLAUDE.md. So why do they fail?

When your context is fairly full and you are in the middle of a complex task, such as a complex query or large scale refactoring, the LLM tends to override or forget your instructions.

**About 98 percent of the time your instructions are followed. There is an outside 2 percent chance the LLM goes off track.** That 2 percent is what hooks handle.

## Two concepts you need first

### The agent loop
A task is never solved in one shot. It is a multi step process.

**Example:** "I want to add a `/delete` API endpoint to my website."
1. The prompt goes to the harness and then to the LLM.
2. The LLM decides to read `app.py` and sends a structured tool call back.
3. The harness opens the file, copies the content, and sends it to the model.
4. The LLM decides it needs to see `schema.sql` to understand the database structure. Another tool call.
5. The harness reads it and sends the content back.
6. The LLM writes the `/delete` code and sends it to the harness.
7. The harness opens the file and pastes the code in, then returns the result.
8. The LLM decides to test the endpoint and sends a test command.
9. The harness runs the tests and returns the result.
10. All tests pass, so the LLM says "done" with no tool call and the loop ends.

Every coding harness implements an agent loop.

### The session lifecycle
The session lifecycle is the full life span of one Claude session, from the moment you launch it to the moment you close it.

**The flow**
1. Session starts when you type `claude`.
2. The user submits a prompt.
3. An agent loop starts for that task and runs until the task is complete.
4. Either the user submits a new prompt, which starts a new agent loop, or the session ends with `/exit`.

So the agent loop is the inner loop and the session lifecycle is the outer loop. The coding harness controls the whole thing.

## The events
Because the harness controls the lifecycle, its creators defined **events** at every stage. The Claude Code documentation hooks page shows the complete lifecycle.

**The main events to remember**
- SessionStart
- UserPromptSubmit
- PreToolUse
- PermissionRequest
- PostToolUse
- PostToolBatch
- TaskCreated and TaskCompleted (for subagents)
- SubagentStart and SubagentStop
- Stop
- TeammateIdle
- PreCompact and PostCompact
- SessionEnd

## What hooks are
Hooks are custom scripts written by the programmer that the harness automatically executes at specific events during a session's lifecycle.

**Why they work where CLAUDE.md fails.** A CLAUDE.md instruction works 98 percent of the time. A hook is a software system, so the script executes 100 percent of the time the event fires. Hooks bring determinism into a probabilistic system.

## Worked example
1. You write a script that says: if the LLM tries to run `rm spendly.db`, abort the operation.
2. You configure that script on the PreToolUse event.
3. The user prompts "Clean and organise my codebase".
4. The LLM decides `spendly.db` is not important and tries to delete it.
5. Before the harness runs the command, the hook script runs.
6. The script sees the delete, blocks it, and returns exit code 2.
7. The harness does not run the command. It creates an error message and sends it to the LLM.
8. The LLM reads the error, understands it asked for the wrong thing, and sends a correct command instead.

## Six use cases for hooks

### 1. Auto formatting
Formatting connects directly to code readability, and readability connects to maintainability.

**Why it matters with Claude Code.** You build one file across many sessions. Claude might put spaces around equals signs in one session and not in another. A single file starts to look like five different programmers wrote it.

**The fix.** Connect a formatter such as **black** for Python to the **PostToolUse** event. Every time Claude finishes a piece of code the formatter triggers automatically.

This is the most common use case you will see in tutorials.

### 2. Linting
**The difference:** formatting is about style, linting is about catching actual problems. A linter reads your source code and finds bugs, bad practices, and suspicious patterns without running the code.

**What a linter catches**
- Unused imports.
- Undefined variables.
- Unreachable code, such as a line after a return statement.
- Bare except clauses that catch everything.

Connect a linter to PostToolUse.

### 3. Blocking dangerous shell commands
A script on PreToolUse that checks whether a shell command about to run would delete a sensitive file.

### 4. Protecting sensitive files
Same approach. Stop edits to files like `.env`.

### 5. Notifications
**The scenario.** You start a large refactor that takes five to ten minutes. You switch to another tab to do something productive and lose track of when Claude finishes.

**The fix.** Connect a notification service such as notify.sh as a hook on the **Stop** event. You get a push notification on your phone when Claude finishes.

### 6. Telemetry
Understanding how Claude Code is working internally. The **agents-observe** dashboard from video 11 is built exactly this way. It catches SubagentStart and SubagentStop events through hooks and displays them in a UI.

### The instructor's own use case
He puts a hook on **SessionStart** that generates a summary of what has been covered in the project so far, which features are built, which are pending, the previous session's summary, and the current state of the code. The summary is in front of him as soon as a new session starts.

## How to create a hook
Hooks are created in `.claude/settings.json` as JSON.

**A hook has three parts**
1. **Event.** Which event triggers this hook. For example PreToolUse.
2. **Matcher.** A filter that narrows down when the hook runs. Without it the hook would run before every single tool use. If you are protecting files from deletion, you only need it on Write or Bash, not on Read.
3. **Action.** What runs when the hook triggers. Usually a script file.

## Exit codes
- **Exit code 2** means abort. Stop the operation and do not go forward.
- **Exit code 0** means all OK, go ahead.

The exit code goes back to the coding harness, which then either blocks or runs the command.

## How the script receives data
The coding harness sends a JSON payload to the hook script through stdin. It contains which tool is about to run and the exact command.

Inside the script you read it with `json.load` from stdin, extract `tool_input`, then `command` from within it, and check it against your rules.

## The live demo
The instructor created `.claude/settings.json` with a hook.
- **Event:** PreToolUse.
- **Matcher:** Bash commands.
- **Action:** if Claude tries to delete `spendly.db`, print "Blocked, you cannot delete the database file" and exit 2.

He also made a backup copy of the database in case the hook failed.

He then started a new session and typed "Delete the spendly.db file".

Claude said "I found spendly.db, deleting it now." Then the hook triggered before the delete ran. Claude's response:

> A preconfigured hook is blocking deletion of spendly.db. It looks like there is a safety rule in your Claude Code settings that prevents deleting that file. You would need to either run `rm spendly.db` directly in your terminal or update or remove that hook first.

## The two hooks added to the project
**Hook 1: code formatting**
- Event: PostToolUse.
- Matcher: Write or Edit. Not Read, not Bash.
- Action: run the black formatter on whatever files were created or edited in that turn.
- **Requirement:** black must be installed in your project. Run `pip install black`.

**Hook 2: protecting sensitive files**
- Event: PreToolUse.
- Matcher: Bash.
- Protects `spendly.db`, the `.env` file, and the migrations folder. The last two do not exist yet but will be protected if they appear later.

**A note:** you will not visibly see these hooks working. The formatter may not change anything because the generated code is often already well formatted, and Claude is unlikely to try deleting a sensitive file. This is safeguarding. Set up this process at the start of a project.

## The feature built in this video: edit expense
The site could view and add expenses but not edit an existing one.

1. New session renamed to "edit expense".
2. `/create-spec 8 edit-expense`. A new branch and spec were created. The spec was reviewed and was correct.
3. Plan mode: "Read the file at this path and come up with a detailed implementation plan."
4. Implemented.
5. Tested manually. Every transaction now has an Edit option, the form is prefilled, and saving shows "Expense updated" and updates the page. Two edits were tried, an amount change from 400 to 500 and a date change on a Netflix subscription. Both worked.

The instructor skipped `/test-feature` and `/code-review-feature` in the video to keep it shorter, but says you should run the entire flow on your own machine.

## Fixing the GitHub token and automating the Git flow
The GitHub MCP flow failed in video 13 because the token lacked permissions. The fix:

1. Edit the token on GitHub.
2. Change repository access from public repositories to **only select repositories** and pick the Spendly repository.
3. Under Add permissions, add **pull requests, issues, contents, and commit statuses**, each with **read and write** access.

## The /ship-feature command
A new custom command that runs the entire release flow in one go.

**What it does**
1. Commits all progress with a proper commit message.
2. Pushes the current branch to GitHub.
3. Creates a pull request through the GitHub MCP server, with the title, description, and definition of done filled in.
4. Merges it, using squash merge.
5. Deletes the remote branch.
6. Switches to main locally.
7. Pulls all the updated code from Git.
8. Deletes the local branch.

**The demo.** Before running it the repo had 30 commits and no pull requests. Claude studied the diff to write the commit message, created the pull request automatically, merged with squash, and deleted the branch. Afterwards the repo had 31 commits, no open pull requests, and only the main branch remained.

## The new complete workflow
1. `/create-spec`
2. Plan mode
3. Implementation
4. `/test-feature`
5. `/code-review-feature`
6. `/ship-feature`

You no longer control anything manually.

## Coming next
The delete expense feature, which is the last functionality the website needs.
