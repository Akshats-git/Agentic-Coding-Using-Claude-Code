# Video 11: Claude Subagents and How They Solve Context and Token Cost Problems

**Duration:** about 48 minutes
**Link:** https://www.youtube.com/watch?v=aZCU_wTXwfo

## Main idea
Subagents are covered across two videos. This one is the theory. The next one builds custom subagents.

## The root problem
LLMs are stateless. They have no memory of past turns.

Example without history:
- You ask "What is the capital of France?" and get "Paris".
- You then ask "What about Germany?" and the model replies "In what context?" because it forgot.

**The fix chat apps use:** send the entire conversation history with every new message. This works fine for chat apps but fails badly for coding agents.

## Why it fails for coding agents
Imagine a codebase with 15 to 20 files, roughly 30,000 tokens.

- **Turn 1.** You say "Analyse my codebase and build an auth system." The agent loads the whole 30,000 token codebase and returns a plan.
- **Turn 2.** You say "Now implement the JWT middleware based on the plan." You must resend the 30,000 token codebase plus the previous conversation. That is about 32,000 tokens.
- **Turn 3.** You say "Add rate limiting and refresh token rotation." Again the full codebase, plus about 5000 tokens of history, plus all the generated code. That is about 39,000 tokens.
- **By turn 8** a single turn is around 76,000 tokens, and you have already spent more than one dollar.

**The waste:** you only needed to send the codebase once. It went in on turn one and the agent understood it. Every resend after that is pure waste.

## Two problems this creates
1. **Context window overflow.** The window fills fast and every turn costs more.
2. **Lost in the middle effect.** When the context window is very full, LLMs focus on the earliest and latest tokens and forget the middle ones. Answer quality drops. This is a well studied effect.

## What subagents are
Subagents are specialised AI assistants that run in their own isolated context windows. They do the heavy lifting in a separate space and hand back only what matters.

**How it works**
1. You talk to the main agent, which is Claude Code.
2. The main agent spawns a subagent, either on its own or because you asked.
3. The subagent gets a fresh context window.
4. It does its specialised work there.
5. It returns only the result to the main agent.
6. Its context window is then destroyed.

## Worked example
You say "Add auth to my Express app."

Claude thinks: "I will analyse your codebase first. Let me spawn a subagent for that."

The subagent gets the full codebase and the prompt "Analyse this codebase and come up with an implementation plan". It has the same model, the same system prompt, and its own context window. It burns 30,000 tokens internally and returns a 500 token summary:

> Found 20 files. Key findings are Express with Prisma stack, 12 routes, no existing authentication, sessions or middleware, and Redis already configured for caching.

The subagent's context is destroyed. The main agent continues with just that small plan.

**The saving:** instead of sending 30,000 tokens every turn, you send a 2000 token plan. That is roughly 28,000 tokens saved per turn, which means a longer conversation and lower cost.

**A useful analogy:** subagents are like functions in programming. You do not care what happens inside. You give an input and get an output back.

## Four advantages of subagents
1. **Context isolation.** Each subagent gets a fresh context window.
2. **Specialisation.** You can build a research agent, a coding agent, or a security auditor. Each can have its own system prompt, skills, and tool access, and you can deny tools it does not need.
3. **Modularity.** Different subagents for different stages of the software lifecycle: one to analyse code, one to implement plans, one to review code, one to run tests. This is the architecture experienced coders now follow.
4. **Parallelism.** Independent tasks can run at the same time. Example: running exploratory data analysis on three datasets at once instead of one after another. Without subagents a single main agent works only step by step.

## Six common use cases
1. **Codebase exploration.** Without a subagent the codebase eats your context and costs money every turn. Claude Code already does this automatically whenever you ask it to explore.
2. **Code review.** The agent that wrote the code is not good at reviewing it. It knows what trade offs it made and what it rejected, so it carries an inherent bias. A separate agent gives a better review.
3. **Testing.** Same logic. The agent that wrote the code does not generate or run the best test cases for it.
4. **Multi stage pipelines.** When the output of one task is the input to the next. For example one agent writes the API contract, a second writes the code, and a third tests it. The handoff between agents works well.
5. **Parallel independent tasks.** For example developing an auth service, a payment service, and a user service at the same time because they live in different files.
6. **Security audits.** A separate agent avoids the same self review bias.

## Two types of subagents

### Built in subagents
These come with Claude Code. The three main ones are:
- **Explore subagent.** Triggered when you ask Claude to explore any codebase or part of it. It reads, understands, analyses, and returns a summary.
- **Plan subagent.** Triggered when you use plan mode to generate an implementation plan. It does the heavy lifting.
- **General purpose subagent.** Used for read and write tasks when Claude decides it needs a subagent.

### Custom subagents
Ones you build for your own needs. Same two scopes as skills.
- **User level agents** live in the `.claude` folder in your home directory and work in every project.
- **Project level agents** live in the project's `.claude` folder and work only there.

## What you configure on a custom subagent
- Which tools it can access. An explorer gets read tools, a coder gets write tools, a researcher gets web search.
- A system prompt explaining its job.
- Which model it uses. One subagent can use Opus while another uses Sonnet.
- Its permissions.
- Which hooks apply to it.
- Which skills it can access.

**Two examples**
- A **security reviewer** with Read, Grep, and Glob tools, running on Opus, with the prompt "Review for injection, authorization bypass, and data exposure vulnerabilities".
- A **research agent** with Read, Grep, WebSearch, and WebFetch tools, running on Sonnet because exploration needs less reasoning power.

## Two ways subagents get triggered
1. **Implicitly.** Claude recognises that the task needs a subagent and delegates on its own. This happens most of the time.
2. **Explicitly.** You tell Claude which subagent to use for the task.

## The practical demo
**The goal.** The profile page UI was built in video 10 but shows hardcoded dummy data. Connect it to the backend so the logged in user sees real data from the database.

**The observability tool.** To make subagent activity visible, the instructor used a GitHub library called **agents-observe**. It is a real time observability dashboard that shows which subagent is running and what it is doing. It is built on hooks. Start it with `observe start` and open the URL it gives you.

**Demo 1: the explore subagent**
1. Ran `/context` first. Free space was 147.5k tokens.
2. Prompted: "Explore the codebase and tell me what this is all about. Also tell me what features have been already developed and which features are yet to be developed."
3. The dashboard showed a new subagent named claude-explore start up, using the Read tool on many files.
4. When it finished it showed "subagent stopped" and control returned to the main agent.
5. Ran `/context` again. Free space was still 142k tokens. If the whole codebase had loaded into the main context this number would have dropped far more.

**Demo 2: the plan subagent and parallel work**
1. Ran `/create-spec 5 backend-routes-for-profile-page`. The main agent handled this alone with no subagent, because it only had to read a few files.
2. The generated spec was replaced with the prepared one.
3. The plan mode prompt asked Claude to read the spec, come up with an implementation plan, and split the implementation work across three parallel subagents. Subagent one handled the summary stats, subagent two handled one table, and subagent three handled the other table.

**An honest caveat from the instructor:** this is not a good idea in real work. Parallel subagents should work on different files. Here all three worked on one file, which forced the main agent to do extra integration work. It was done only as a demonstration.

**What the dashboard showed**
1. Two explore subagents started first, one for the test structure and one for the templates. Planning requires exploring first.
2. Both stopped and returned findings to the main agent.
3. A claude-plan subagent started and built the implementation plan.
4. The plan subagent stopped and the main agent wrote the final plan file.
5. After approval, three general purpose subagents started and worked in parallel.
6. They finished one by one and the main agent reported "All three look correct, now integrating."

**Testing the result.** The page showed zeros because the logged in user had no expenses. The `/seed-expense` command was used with user id 3 to add three expenses over the last six months, then two more. The page then showed all five with the correct total.

Then the usual wrap up: commit, push, pull request, merge, delete branch, switch to main, pull.

**A small hiccup:** the branch name got split by a line break during the push. The instructor simply asked Claude "Can you resolve the issue and push?" and it fixed it.
