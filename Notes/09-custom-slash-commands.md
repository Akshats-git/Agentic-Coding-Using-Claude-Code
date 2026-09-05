# Video 9: Claude Code Custom Slash Commands

**Duration:** about 46 minutes
**Link:** https://www.youtube.com/watch?v=ep2P9hvmvzY

## Main idea
This video has two goals. Learn to build your own slash commands, and add the login and registration features to the project.

## What custom slash commands are
In simple words they are saved prompts. You store a prompt somewhere and it runs automatically when you type the command name, just like a built in slash command.

**When to use them**
For any repeatable workflow you run again and again in your project.

**How to create one**
1. Write a Markdown file describing what the command does.
2. Save it inside a `.claude` folder.
3. Claude Code picks it up automatically as a slash command.

**The file name becomes the command name.** A file called `seed-user.md` becomes `/seed-user`.

## Two types by scope
| Type | Where the file goes | Where it works |
|---|---|---|
| Project scoped | `.claude/commands/` inside the project | Only in that project |
| User scoped | `.claude/commands/` in your home directory | In every project on your machine |

## Example ideas for custom commands
- `/review` runs a code review on the file you just wrote.
- `/commit` looks at your changes and generates a Git commit message.
- `/test` runs your test suite.
- `/security-scan` checks the codebase for vulnerabilities.

## Command 1: /seed-user
**Purpose:** add one dummy user to the users table. This is needed because you need some data on screen while building the UI.

**What the file contained**
- **Description.** "Create a single dummy user in the database." This description is what shows next to the command when you type `/`.
- **Allowed tools.** Read access to files, and Bash access limited to commands starting with `python3`. It cannot run git commands.
- **Instructions.** Read `database/db.py` to understand the users table. Write and run a Python script that generates a realistic random Indian user using common Indian names across regions. Generate name, email, and a password of 123 which gets encoded, plus a created_at timestamp. Check whether the email already exists and regenerate until it is unique. Insert the user using the `get_db` function. Print the user's details at the end.

**Important:** you must exit and restart Claude Code before a new command shows up.

The command worked and added a user named Rohan Kulkarni with an email and an encrypted password.

## Command 2: /seed-expense
**Purpose:** add dummy expenses. This command takes input, so it is more flexible.

**Three inputs**
1. The user id the expenses belong to.
2. How many expenses to add.
3. How many months back to spread them across.

**The key new idea: `$ARGUMENTS`**
`$ARGUMENTS` is a variable. Everything the programmer types after the command name is stored in it. Inside the file you tell Claude to extract the values from it.

**What the file contained**
- A hint line showing the expected inputs so they appear when you type the command.
- Instructions to extract user_id, count, and months as integers from `$ARGUMENTS`, and to show the correct input format if anything is missing.
- Step 2: verify the user exists in the users table.
- Step 3: generate and insert the expenses, spread across the requested months, with amount ranges per category. For example food between 50 and 800 rupees and health between 100 and 2000 rupees.
- An instruction to distribute categories roughly proportionally.

Running `/seed-expense 2 5 3` added five expenses for user id 2 spread over the last three months.

## Command 3: /create-spec
**Purpose:** automate the spec document creation that was done by hand in video 8.

**What the file contained**
- **Description.** "Create a spec file for the next Spendly feature."
- **Arguments.** A step number and a feature name. For example `2 registration`.
- **A role instruction.** "You are a senior developer planning a new feature for the Spendly expense tracker. Always follow the rules in CLAUDE.md."
- **Step 1.** Parse the arguments into step number, feature title, and feature slug. If they cannot be inferred, ask the user to clarify before proceeding.
- **Step 2.** Research the codebase. Read CLAUDE.md, `app.py`, the database file, and all existing files in the specs folder to see how much is already built.
- **Step 3.** Write the spec with an exact structure: feature title, an overview paragraph explaining what the feature does and why it exists at this stage of the roadmap, what it depends on, routes to implement, database changes needed, UI templates to create or modify, other files to modify, new files to create, dependencies, rules of implementation, and acceptance criteria.
- **Step 4.** Save it to `.claude/specs/<step number>-<feature name>.md` and tell the user it is done.

## Building the registration feature
1. Run `git status` and commit any pending changes.
2. Create the branch with `git checkout -b feature/registration`.
3. Run `/create-spec 2 registration`. Claude generates the spec.
4. Review the spec carefully. The instructor recommends pasting it into ChatGPT or Claude for a proper review.
5. Enter plan mode and prompt: "Read .claude/specs/02-registration.md and create a detailed implementation plan for the same. Don't write any code."
6. Approve the plan with manual edit approval so you see each change.
7. Claude changed `db.py`, added a secret key and implemented the register route in `app.py`, and updated `register.html`.

**Validating against the acceptance criteria**
- Visiting `/register` shows the registration form. Works.
- Submitting valid fields creates a user and redirects to login. Works, confirmed in the database.
- Submitting mismatched passwords shows "Passwords do not match". Works.
- Submitting an already registered email shows "Email already registered". Works.
- Submitting with an empty field is blocked. Works.
- The password is stored as a hash. Works.
- No duplicate user is created on repeated submission with the same email. Works.

Then commit, push to `feature/registration`, create a pull request, merge it, delete the branch, switch to main, and pull.

## Improving /create-spec
Creating and switching branches by hand every time is repetitive, so those steps were moved into the command itself.

**The updated steps in `create-spec.md`**
1. Check whether the working directory is clean by running `git status`. If there are uncommitted or unstaged changes, stop and ask the user to commit first.
2. Parse the arguments as before.
3. Check whether the branch name is already taken. If it is, attach a number to make it unique.
4. Switch to the main branch and pull the most recent code.
5. Create the new branch and switch to it.
6. Continue with research, writing the spec, saving it, and reporting to the user.

## Building the login and logout feature
1. Run `/create-spec 3 login-and-logout`. The command stopped because of uncommitted changes, which proved the new check works.
2. After committing, the command created the branch, switched to it, and wrote the spec automatically.
3. The spec covered converting the login stub into a working POST handler, implementing logout to clear the session and redirect to the landing page, three routes for GET login, POST login, and GET logout, no database changes, and modifications to `login.html`.
4. Plan mode generated the implementation plan.
5. Claude implemented the login route, the logout route, and the navigation bar changes in `base.html`.

**Validation found one bug.** A logged in user could still visit `/login` and `/register`. This was not in the spec. The fix was a follow up prompt: "I am able to access /login and /register even when I am logged in. This should not happen." Claude fixed it and the redirect then worked correctly.

**The lesson:** even with a spec you sometimes miss things. You improve iteratively.

Then the usual flow: commit, push, pull request, merge, delete branch, switch to main, pull.

## Note on pull requests
In a real team a senior developer or tech lead studies all the changes before merging the pull request. Here the instructor writes all the code, so merging directly is fine.

## Coming next
The next video builds the profile page for a logged in user.
