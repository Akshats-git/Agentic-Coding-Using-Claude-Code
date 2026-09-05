# Video 7: Spec Driven Development in Claude Code

**Duration:** about 28 minutes
**Link:** https://www.youtube.com/watch?v=AjKFApDdffA

## Main idea
Spec driven development is one of the core pillars of agentic coding. This video explains the difference between vibe coding and agentic coding.

## The problem with vibe coding
Vibe coding is a modern style of programming. Instead of planning everything up front you build software by talking to an AI assistant in a fast, conversational, and experimental way.

**How it goes wrong**
You lose a lot of control. The AI makes crucial decisions on your behalf.

Example: you say "build me a user authentication system". The AI now has to decide many things by itself.
- Which framework should it use.
- Should it use JWT or sessions.
- What password rules apply, such as minimum eight characters, numbers, letters, and special characters.
- What happens after three wrong password attempts.

If the AI picks answers you did not want, you find out only after the whole thing is built. Then you go back and change it.

**The core flaw:** you get code fast, but you may not get the right code. You end up in a loop of corrections and patches.

## What spec driven development is
Spec driven development is a software development approach where a detailed specification document is written before any code is written. The spec acts as a single source of truth for what the system should do, and all development flows from it.

You keep the control. You answer the crucial questions in advance and hand them to the AI in a document so the AI has nothing left to guess.

**Philosophically the two are opposites.**
- Vibe coding: lose control, get faster code.
- Spec driven development: code slower, keep full control.

## What a spec document contains
The exact format varies by company and developer, but these six parts are always there.

1. **Problem statement.** Why are you building this feature. The "why" part.
2. **Functional requirements.** Exactly what the feature will do.
3. **Input and output behaviour.** What the user does and what the system returns.
4. **Constraints.** Limits on the system such as performance and screen sizes.
5. **Edge cases.** Where the feature can fail and how to handle each case.
6. **Acceptance criteria.** The checklist that decides whether the work is complete.

## Example spec: a chat history sidebar
**Problem statement.** Users create multiple conversations over time. There is no easy way to revisit or continue past chats. This makes it hard to find previous discussions, resume earlier conversations, and manage multiple chat threads.

**Functional requirements.** Display a sidebar with a list of past chats. Show a short readable title for each chat. Generate the title automatically from the user's first message. Allow users to click any chat to open it.

**Input and output.** Input is the user clicking a past conversation. Output is that conversation opening.

**Constraints.** The sidebar should load within a second. It should work on standard laptop screens. Titles should be short and readable. The system should handle a reasonable number of chats smoothly.

**Edge cases.** No chats exist yet, so show "No chat history yet". A chat cannot be loaded, so show an error message. A very long first message, so use only the first part for the title.

**Acceptance criteria.** The user can see a list of past chats. The correct conversation is displayed every time. A new chat appears automatically after first use.

## The technical design plan
The spec document is the "why" and "what". It is non technical. You also need a "how" document.

The technical design plan contains:
- The objective.
- The chosen tech stack with reasons. For example React for the UI, FastAPI for high concurrency chat, and a relational database for storage.
- A high level architecture diagram showing frontend to backend to database.
- The data model for the database.
- Example code or boilerplate so coders are not confused.
- Core design decisions.
- Functional flows showing which API each action hits.
- A development plan.

**Who writes what in a real company**
- Product managers write the spec document with the engineering team.
- The engineering team writes the technical design plan for the developers.

**Why two separate documents**
If you ever move to a different tech stack, a spec that mentions technology has to be rewritten completely. A technology free spec can support many technical design plans for many stacks.

## The complete spec driven development workflow
1. Create the spec document.
2. Review the spec document.
3. Create the technical design plan.
4. Review the technical design plan.
5. Extract a set of tasks from the plan. For example database and models, then backend endpoints, then frontend UI components, then integration.
6. Build the tasks. In a company these tasks go into Jira and get split among developers.
7. Validate the finished code against the acceptance criteria in the spec.

## Vibe coding vs spec driven development
| | Vibe coding | Spec driven development |
|---|---|---|
| Starting point | A rough idea in a prompt | A written specification |
| Who decides requirements | The AI | You, the programmer |
| Control | Low, the AI leads | High, you lead |
| Speed | Very fast | Slower at the start |
| Code quality | Unpredictable | Consistent and traceable |
| Good for | Prototypes, exploration, side projects | Serious production systems |
| Failure mode | Too much code you do not understand | The AI over engineers the solution |
| Debugging | Point at the bug and ask the AI to fix it | Compare the output against the spec |
| Need to understand code | No | Yes, you must know the language well |

**Agentic coding** is the wider paradigm. Spec driven development is one part of it. Subagents and other tools are the rest.

## How this playlist will use it
In real companies teams write all three documents by hand. In this playlist Claude will generate them.

1. **Spec document.** Generated by a custom slash command that will be created in a later video. You still review it.
2. **Technical design plan.** Generated using plan mode, which is the next video's topic. You still review it.
3. **Tasks.** Claude creates these automatically from the technical design plan.
4. **Coding.** Either one agent builds the whole feature, or multiple subagents build tasks in parallel.
5. **Validation.** Check the code against the spec. Tests can also be written here.

## The Git workflow wrapped around it
Every feature from the next video onward follows this exact flow.
1. Pull the most recent version of the code from Git.
2. Create a new feature branch and switch to it.
3. Create the spec document and review it.
4. Create the technical design plan and review it.
5. Start coding.
6. Validate against the spec.
7. Commit the changes.
8. Push to GitHub.
9. Create a pull request and merge it into the main codebase.
10. Delete the feature branch.
11. Switch back to the main branch.
