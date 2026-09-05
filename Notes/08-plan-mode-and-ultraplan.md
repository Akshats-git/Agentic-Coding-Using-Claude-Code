# Video 8: Plan Mode and Ultraplan Mode in Claude Code

**Duration:** about 38 minutes
**Link:** https://www.youtube.com/watch?v=yz-7Oczvg34

## Main idea
This video does two things. It builds the first real feature of the project, which is the database setup, and it teaches plan mode.

## The goal of this video
Set up the database for the expense tracker. Three things get done.
1. Create the required tables.
2. Insert some dummy data.
3. Write functions inside `database/db.py` that later features will use.

This is a foundational feature. Nothing else can be built without it.

## The full workflow followed
1. Start a new Claude Code session and rename it to "database setup".
2. Run `git pull origin main` to get the latest code.
3. Create and switch to a new branch named `feature/database-setup`. All future branches follow the `feature/<name>` pattern.
4. Write the spec document.
5. Save the spec in `.claude/specs/database-setup.md`. All future specs go in this folder.
6. Enter plan mode and generate the technical plan.
7. Review the plan and implement it.
8. Validate the result against the acceptance criteria.
9. Commit the changes.
10. Push the branch to GitHub.
11. Create a pull request and merge it.
12. Switch back to main, pull the merged code, and delete the feature branch.

## What the spec document contained
**Database schema with two tables**
- `users` table with columns id, name, email, password_hash, and created_at.
- `expenses` table with columns for user, amount, category, date, description, and created_at.
- Each column has a defined type and constraints.

**Three functions in `database/db.py`**
- `get_db` opens a database connection and returns it.
- `init_db` creates the tables, and only if they do not already exist.
- `seed_db` adds dummy data to the database.

**Changes in `app.py`** to import and use those three functions.

**Eight expense categories** such as food, transport, and bills.

**Rules for implementation**
- Do not use an ORM such as SQLAlchemy. Write the database code by hand.
- Use parameterised queries only.
- Dates must follow a fixed format.
- Error handling behaviour is described.

**Acceptance criteria** listing what must be true for the feature to count as done.

## What plan mode is
Plan mode is a mode of operation in Claude Code.
- Claude starts multiple agents that explore, read, and understand your code.
- **Claude cannot write anything in plan mode.** It can only read.
- The goal is to build a detailed plan for your task.
- Only after you approve the plan does Claude implement it.

## How to enter plan mode
- Press Shift and Tab twice. You will see "plan mode on".
- Or type `/plan` and press enter.

## The prompt used
The prompt told Claude to read the spec document, look at the existing files, generate an implementation plan, and save the plan to `.claude/plans/`.

**Note:** the save did not actually work in the demo, but the plan was reviewed directly on screen.

## What happened
Claude explored the codebase, produced a plan, and after approval it made all the edits. It also ran its own tests with pytest and reported that all checks passed, with one demo user and eight expenses created across all categories.

## Validating the result
The instructor went back to the acceptance criteria and checked each one.
- The database file was created.
- Both tables exist with the correct schema.
- A demo user exists with email, password hash, and created_at.
- Eight expenses exist for the demo user.
- The app starts without errors.
- The `db.py` file has all three functions and uses parameterised SQL queries.

## Improving plan mode: three settings

### 1. Model selection
Use `/model` to switch. For complex planning across many files, switch from Sonnet to Opus. Opus burns many more tokens but produces a much more solid plan.

**The common pattern:** Opus for planning, Sonnet or Haiku for coding.

You need Opus only for large codebases, mass refactoring, or deep architectural decisions. This playlist's project does not need it.

### 2. Extended thinking
Normally Claude starts printing its answer token by token as soon as it sees the question. That fails on complex tasks.

**The analogy:** in an interview a simple question can be answered straight away. A puzzle or a coding question is better answered after working it out on a whiteboard first.

Extended thinking gives Claude a scratchpad. There is a reasoning phase before the response phase. Claude reasons on the scratchpad, then writes the answer based on that reasoning.

**How to turn it on:** run `/config` and set thinking mode to true. Press space to toggle and then enter. Setting it to false saves tokens.

**Recommendation:** always keep extended thinking on when using plan mode. Plan quality improves noticeably.

### 3. Effort level
Effort level is the token budget for thinking. You cannot let the model think forever because thinking burns tokens.
- Low effort gives it around 500 to 1000 tokens to reason with.
- High effort gives it 5000 to 10000 tokens.

**The four levels** are low, medium, high, and max. Max comes with Opus and gives unlimited thinking tokens. There is also an auto setting.

**How to change it:** type `/effort` and pick low, medium, high, max, or auto.

**Recommendation:** the instructor uses auto. Medium to high is a good manual setting. Be careful because tokens burn fast here.

## Ultraplan
Ultraplan is a superior alternative to plan mode. It is a very new feature.

**How it works**
1. You type `/ultraplan` followed by your prompt, or use the word "ultraplan" anywhere in your prompt.
2. Anthropic starts a container in the cloud running an Opus 4.6 instance.
3. The plan is built in the cloud and not on your machine.
4. You get a link to monitor progress in Claude Code for Web.
5. You can edit the plan in the web editor.
6. When satisfied you choose one of two options.
   - Approve the plan and start coding in the web.
   - Approve the plan and teleport it back to your terminal.
7. Back in the terminal you can implement it here, implement it in a new session, or cancel.

**When to use it:** only when you are not satisfied with the output of regular plan mode, or when the feature is very complex. Otherwise it is overkill. It costs more tokens than regular plan mode.

## Coming next
The next video covers custom slash commands and adds the login and registration feature.
