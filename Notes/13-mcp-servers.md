# Video 13: Claude and MCP Explained

**Duration:** about 55 minutes
**Link:** https://www.youtube.com/watch?v=Q38npqiDxMI

## Main idea
This video covers three things. What MCP is and how to use it in Claude Code, ten useful MCP servers, and adding a new feature to the project with MCP helping.

## What MCP is
MCP stands for Model Context Protocol. It is a standardised way to connect any external tool to your LLM.

- Anthropic released it about one and a half years ago.
- It is now used by all the big players to connect their tools to any LLM.
- **Before MCP** you wrote custom, non standardised code for every connection. If the service provider changed their API, your connection code broke.
- MCP is an open standard that acts as a universal connector between Claude Code and external tools, services, and data sources.

The instructor has a separate playlist with eight in depth videos on MCP if you want the deeper technical details.

## Why MCP matters in Claude Code
Claude Code's built in tools are limited.
- Read tool to read files.
- Write tool to create and write files.
- Bash tool to run terminal commands.

It cannot read your GitHub repositories, fetch a document from your Drive, pull pending tickets from Jira, or read active conversations in Slack. MCP gives it all of that. Each MCP server brings extra context about your workflow into Claude Code without you having to explain it.

## Server 1: a SQLite database server
**The plan was DBHub**, a zero dependency and token efficient MCP server that can connect to multiple databases at once. It failed to start because the project uses a local SQLite database, which DBHub does not support in this setup.

**Use `/mcp`** to see the list of MCP servers you have and their status. DBHub showed as "failed" and reconnecting did not help.

**The working alternative** was a server called mcp-database-server, which does support SQLite.

**How to add it**
1. Copy the install command and put your database file path in it. Here the path pointed to `spendly.db` inside the project folder.
2. Open a new terminal in your project and run the command.
3. It says "Added stdio MCP server". Stdio is one of the two transport mechanisms, the other being HTTP and SSE. Stdio is used because this is a local setup.
4. Exit Claude and restart it. Run `/mcp` again and the server now shows as connected.

**What you can then do.** Ask questions about the database in plain English with no Python code.
- "List all the tables in the Spendly database." Answer: users and expenses.
- "Can you tell me the schema of the expenses table?"
- "Show total spending grouped by category."

Claude asks permission before using a tool. You can answer "Yes, and don't ask again for sqlite" to grant it going forward.

**Why this matters.** This project only has two tables. On a real project with 15 or 20 tables, understanding which tables exist and how they relate is where this server really helps. It works with MySQL and PostgreSQL too.

## Server 2: the Figma server
**The normal company flow.** The design team creates wireframes and UI/UX components in Figma. They export the designs, the development team studies them, and reproduces them exactly in code.

**With MCP** you skip the manual reading. You give Claude Code the design URL, it reads the design, and it builds the page for you.

**How to add it**
1. Run the given command in your terminal, not inside Claude Code.
2. This installs a **plugin** in your Claude Code setup. A plugin is a package that can contain skills, agents, MCP servers, and hooks. The Figma plugin contains both the Figma MCP server and skills for common workflows.
3. Start a new Claude session, run `/plugin`, go to the Installed tab, and open the Figma plugin.
4. Authenticate with your Figma account. A browser window opens and you log in.

**The demo.** The website needed an Analytics menu item leading to a "Coming Soon" page. A designer had made the design in Figma.

**The prompt used**
> Here is the Figma design for the coming soon page: [link]. Please do the following. Read the Figma design and convert it to a Jinja2 HTML template. Add an Analytics menu item to the nav bar. Create a Flask route in app.py that renders the coming soon page. Protect this route so that only logged in users can access it.

**How to get the link:** right click the design in Figma and choose Copy link.

**The result.** Claude read the design, explored the existing codebase, wrote the code, and ran its own test. The rendered page matched the Figma design very closely, though the font style was not an exact match.

## Server 3: the GitHub server
This one is very useful because it gives Claude the power to both read from and make changes on GitHub.

**How to add it**
1. Go to github.com, then Settings, then Developer settings, then Personal access tokens, then Fine grained tokens, then Generate new token.
2. Verify by email.
3. Give the token a name, set the resource owner, set an expiration date, and choose repository access, either all repositories or only selected ones.
4. **Set permissions.** Read and write for repo, pull requests, and issues.
5. Generate the token.
6. Paste the token into the given command where it says "your actual token here".
7. Run the two commands in your project terminal one by one. The second says "Added HTTP MCP server".
8. Open Claude in a new terminal and run `/mcp`. GitHub now shows as connected.

**Example questions that worked**
- "Which is my most starred repository?" Answer: 100 Days of Machine Learning with 2500 stars.
- "Are there any issues on this repo? If there are, summarise them for me." Answer: 30 open issues grouped by category, covering security issues, broken or missing content, deprecated code, and data leakage and correctness issues.
- "Are there any open pull requests?" Answer: 37 open pull requests with a summary.

You can also close and merge pull requests from here.

## The feature built in this video: Add Expense
**The goal.** A form where the user enters expense details, submits, and the expense is added to the database and appears on the profile page.

**The steps**
1. Rename the session to "add expense".
2. Run `/create-spec 7 add-expense`. This first errored because there were uncommitted changes, so a commit was made for the coming soon page, then the command was rerun.
3. Review the spec. It looked correct.
4. Plan mode: "Read this file and come up with an implementation plan."
5. Approve with auto accept edits. A lot of code was generated across several files.

**A practical note from the instructor:** you cannot actually see everything that is happening. You watch in overview mode and follow which files are changing. That comes with experience.

6. Tested in the browser. The Add Expense button worked and the expense showed up and persisted after refresh.
7. Ran `/test-feature 07-add-expense`. 46 test cases were created and all passed.
8. Ran `/code-review-feature 07-add-expense`. Some suggestions came back and were implemented.
9. Verified the app still worked.

## Automating the Git flow with the GitHub MCP server
The plan was to replace all the manual Git steps with one prompt.

> Commit all changes with an appropriate conventional commit message. Push to the current feature branch. Create a pull request into main with a proper title and description based on the spec. Merge it using squash merge. Switch to main, pull latest, and delete the feature branch locally.

**What happened.** The commit and push worked. Then it failed: "The GitHub token doesn't have PR write access. The branch is pushed, you can create the PR directly."

**The cause.** The pull request permission was not granted when the token was created. The remaining steps had to be done by hand.

**The fix for next time:** create a new personal access token and explicitly grant permission to create and merge pull requests. The instructor said he would show the full automated flow from the next video onward.

## Seven more useful MCP servers

### 1. Context7
Pulls live, up to date documentation for any library or framework directly into Claude's context while you code. You create an account on their website and get an API key.

**Why it matters:** an LLM's knowledge stops at its cutoff date. If a library has released a new version since then, Claude will not know about it. Context7 always gives the latest documentation. Almost everyone uses this one.

### 2. Jira
Jira is a project management tool by Atlassian, widely used by software teams to plan, track, and manage work from bug fixes to full feature development. It is essentially a developer's shared to do list where work is assigned as tickets.

**Example prompts**
- "Read this ticket and implement the feature." All the details pull in automatically.
- "Find all open bug tickets in the Spendly project and fix the highest priority one."

### 3. Notion
Notion is an all in one workspace tool where teams and individuals write documents, manage knowledge, plan projects, and collaborate in one place.

**Example prompts**
- "Read the product requirement doc for the analytics module in Notion and implement the feature in my Flask application."
- "Read the API design document in Notion and scaffold all the endpoints in app.py."

### 4. Slack
Slack is a professional communication platform where teams chat in channels, send messages, share files, and collaborate.

**Example prompts**
- "Push this fix to Git, open a PR, and post the PR link in the code-reviews channel with a summary of what was changed."
- "Check the incidents channel for the latest production error. Read the details, find the bug in the codebase, and fix it."

### 5. AWS
AWS is the world's largest cloud platform with more than 200 services. The instructor has not used this server himself because his recent projects are on Google Cloud Platform, but he has heard it is quite useful.

**Example prompts**
- "Deploy the latest build of Spendly to the EC2 instance and verify it's running."
- "Check CloudWatch logs for the Spendly app from the last 2 hours and find what's causing the 500 errors."

### 6. Docker
Docker packages your application and all its dependencies into a container, a lightweight, portable, self contained unit that runs the same way on any machine.

**Example prompts**
- "Read my Spendly Flask app and generate an optimised Dockerfile for it."
- "My Docker image is 2GB. Analyse the Dockerfile and reduce the image size."

## Managing MCP servers

**To remove a server**
```
claude mcp remove <server name>
```
Start a new Claude session and run `/mcp` to confirm it is gone.

**To see what a server can do**
Run `/mcp`, pick a server, and choose **View tools**. Select any tool and press enter to see its full description.

## The most important warning
**The mistake the instructor made:** he connected every MCP server he came across.

**Why it is a problem.** The description of every MCP tool loads into your context automatically at the start of every new session. A lot of unnecessary context fills up early and the model's performance degrades.

**What to do instead.** Keep your MCP server usage minimal. Keep only the servers you genuinely find useful and remove the ones you do not use regularly. Less description text enters your context and the model performs correctly.
