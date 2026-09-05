# Video 12: Claude Custom Subagents, Build Your Own AI Workers

**Duration:** about 47 minutes
**Link:** https://www.youtube.com/watch?v=CBdixlYmtaw

## Main idea
This video builds custom subagents and wires them into the project workflow. Watch video 11 first because the theory is assumed here.

## Why custom subagents are needed
Built in subagents already exist, so why build your own?

**Example: a security audit.** You want to check your codebase for SQL injection risks and exposed API keys. Loading the whole codebase for a one time check is a perfect subagent use case, and a built in agent can do it.

**The gap.** Your company has its own security checklist. The built in agent has generic knowledge of how to do a security audit. It does not know how to do a security audit specialised to your codebase and your rules.

A custom subagent lets you give it exactly the tools you want, a system prompt with your specialised instructions, and access to your skills. You get a tailor made agent.

**The rule:** whenever you need specialisation, build your own subagent instead of using a built in one.

## How to create a custom subagent
A subagent is a Markdown file, just like everything else in Claude Code.

**The file structure**
1. **YAML front matter** with these fields.
   - `name` of the subagent.
   - `description` of what it does. This can be long and detailed.
   - `tools` it can access.
   - `model` it runs on.
   - Optional fields such as skills, hooks, memory, effort level, and colour.
2. **Main body** with detailed instructions on how it should carry out its task. You can put the system prompt here too.

**Where the file goes:** `.claude/agents/<name>.md`

**Two scopes**, same as skills.
- **Project level.** The `.claude` folder in the project root.
- **Personal level.** The `.claude` folder in your home directory.

All subagents in this video are project level.

## Two ways subagents get triggered
1. **Automatically.** Claude reads the `description` field and decides which subagent fits the situation.
2. **Manually.** You trigger it, either by naming it in a prompt or through a custom slash command that starts it behind the scenes.

**What the instructor sees in practice:** most people prefer manual triggering through their own workflows.

## The new workflow for every feature from now on
The old flow was: branch, spec, plan, implement, validate, push, pull request, merge, delete branch.

**Two new steps are added before pushing.**
1. **Testing.** Run the feature through extensive tests, exactly like a real software team.
2. **Code review.** A self review by the programmer before the pull request stage.

### The testing stage
A custom slash command `/test-feature` triggers two subagents **sequentially**.
1. **test-writer** writes test cases for the current feature.
2. **test-runner** runs the test cases the writer produced.

**Why two agents and not one?** Having the same agent write and run its own tests is weaker. Splitting the work gives a better result.

The main agent then shows a final summary of the testing stage.

### The code review stage
A custom slash command `/code-review-feature` triggers two subagents **in parallel**.
1. **security-reviewer** analyses the code for security threats and hacking risks.
2. **quality-reviewer** checks whether the code follows good practices.

They run in parallel because the two tasks are independent of each other. The main agent merges both results into one summary.

## Building the test-writer subagent using Claude
1. Start Claude in the project and rename the session to "custom subagents".
2. Run `/agents`. This shows your current agent library.
3. Go to the Agents tab and click **Create new agent**.
4. Choose **project level**.
5. Choose **generate the agent file using Claude** rather than manual config. This is the right choice the first time.
6. Give the description. The one used was: "Use this agent to write pytest test cases for Spendly features. Invoke after implementing any feature to generate tests based on feature specs, not the implementation."
7. Choose the tools. Read only tools plus Edit tools, because it needs to write test files.
8. Choose the model. Sonnet was selected.
9. Choose a colour. Red was selected. This is how the agent appears when it runs.
10. Choose memory. None was selected here.
11. Claude generates the full description and system prompt, and creates `.claude/agents/<name>.md`.

**Important:** review the generated file extensively. Read it yourself, or paste it into Claude or ChatGPT with your project context and ask how correct it is. The instructor replaced the generated file with a version he had already tested.

**What the test-writer subagent does**
- Reads the spec file after a feature is built.
- Understands what the feature was supposed to do.
- Creates a `tests/` folder in the project with a `test_<feature>.py` file.
- Tests the happy path, validation, HTTP semantics, edge cases, and that logged out users cannot access protected pages.

**The key rule in its "won't do" section:** test cases are based on the **spec file** and not on the generated code. The generated code might be wrong. The spec is always right.

## Building the test-runner subagent manually
Two ways were shown.

**Through the UI.** `/agents` then Agents tab then Create new agent then project then **manual configuration**. Here you set the name, the system prompt, exactly when Claude should use it, and which tools it gets.

**Directly in the folder.** The instructor created `.claude/agents/spendly-test-runner.md` by hand and pasted in the markdown he had already prepared.

**Note:** this agent has **no write access** because it only runs tests. Its colour is green.

**What the test-runner does**
1. Verifies tests exist for the current feature.
2. Runs them.
3. Generates a report with four layers of analysis: a pass and fail summary, warning flags, a deep dive on each failure, and recommendations.

The final report has a summary table, a per failure breakdown, warnings and flags, recommendations, and a final verdict.

## The /test-feature command
Created at `.claude/commands/test-feature.md`.
- **Description.** "Writes and runs tests for a specific Spendly feature. Pass the spec name as argument."
- It needs the spec name to work.
- **Step 1:** write tests using the test-writer agent.
- **Step 2:** run tests using the test-runner agent.
- **Final output:** a summary table in a given format.

## The code review agents and command
Two more agent files were created: `spendly-security-reviewer.md` and a code quality reviewer. That makes four agents in total.

A `code-review-feature.md` command orchestrates them.
- **Step 1:** parallel review, running the security reviewer and the quality reviewer at the same time.
- **Step 2:** build a unified report from both.
- **Step 3:** ask for approval before making any changes based on the review.
- Plus the rules to follow through the process.

**Remember:** exit and restart Claude Code so the new agents and commands appear.

## The feature built in this video: a date filter
**The goal.** Add a date range selector to the profile page. When the user picks a range, everything on the page updates to that range: total amount spent, number of transactions, top category, recent transactions, and the by category chart.

**The steps taken**
1. Commit the subagent work first.
2. Exit and start a new session named "date filter". Building the feature in the same session where the agents were made is not a good idea.
3. Run `/create-spec 6 date-filter-for-profile-page`. A new branch was created and the spec was written.
4. Read the spec. It was correct as generated.
5. Plan mode: "Read the spec document and come up with an implementation plan." Claude launched two explore subagents in parallel first, then planned.
6. Approve and implement. Four files changed.
7. Manual check in the browser. The date filter worked, with quick pills for this month, last three months, and last six months, plus a custom date range. Everything on the page updated correctly.

**Testing.** Started the observe dashboard, then ran `/test-feature 06-date-filter-profile`.
- The spendly-test-writer agent started, studied the changed files and the spec, and created the `tests/` folder with the test file.
- Then the test-runner started automatically.
- Result: 76 test cases, 73 passed and 3 failed. The report noted "all three are test issues, not implementation bugs".
- If real bugs appear, you prompt Claude to fix them and test again. It is an iterative process.

**Code review.** Ran `/code-review-feature 06-date-filter-profile`.
- The message read "Launching both reviewers in parallel now."
- The yellow agent was the security reviewer and the other was the quality reviewer, both visible working at the same time on the dashboard.
- The unified verdict requested one change: "The f-string SQL pattern must be fixed before committing. It violates CLAUDE.md's explicit rule and undermines the no injection guarantee. Everything else is either working correctly or a suggestion."
- The instructor approved the fix, Claude made the change, and the feature still worked correctly afterwards.

**Wrap up.** Commit, check the branch name, push, create a pull request, merge, delete the branch, switch to main, and pull.

**A note on pull requests in real teams:** this is where the team lead reviews what was pushed and decides whether to merge. Since the instructor is the only developer here, he merges directly.

## Coming next
The next feature is adding expenses.
