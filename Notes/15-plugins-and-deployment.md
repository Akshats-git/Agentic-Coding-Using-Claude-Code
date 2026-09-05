# Video 15: Plugins in Claude Code and Deploying the Project

**Duration:** about 35 minutes
**Link:** https://www.youtube.com/watch?v=4lfcbeihdJk

## Main idea
This is the final video of the playlist. It explains plugins and finishes the project by deploying it.

## Why plugins are needed: the story of Rahul
Rahul is a senior data scientist at a fintech company. He builds credit risk models, which are machine learning models that look at a customer profile and decide whether to give them a loan. These are critical models. Rejecting a good customer costs money and approving a bad one costs money too.

Rahul is an avid Claude Code user. He has shaped his whole workflow around agentic coding.

### What Rahul built for himself
**Two skills**
1. **An EDA skill.** A generic "do EDA on this dataset" prompt gives generic output. Rahul needs EDA suited to credit risk modelling, so he wrote a detailed skill file: get the dataset shape, get every feature's data type, find the percentage of missing values per feature and plot it as a heatmap, plot distributions for numerical features, measure skewness and flag anything above 1.5 or below -1.5, flag categorical features with too many categories, measure target leakage by flagging features highly correlated with the target, and produce a complete summary.

2. **A feature engineering skill.** In credit risk modelling raw features like income or loan amount are not that useful. Derived features such as the income to loan amount ratio are far more meaningful. His skill tells Claude: create derived features from date time fields such as days since last payment or days since account opened, never one hot encode high cardinality features but use target encoding with five fold cross validation instead, and run a VIF check at the end flagging any feature above 10 for multicollinearity, then produce a report.

This knowledge comes from Rahul's own domain experience.

**A custom slash command.** `/model-eval` runs a whole series of tasks at once: print a confusion matrix in the company theme colours, generate a classification report with precision, recall, and F1, plot the ROC curve, print feature importances using SHAP values, and produce a detailed one page summary of how the model performed.

**A hook.** A PostToolUse hook that runs data science specific checks after every code generation.
- Reject `df.dropna()` used without column names, because it can delete far too much data.
- Never fit a scaler or encoder on the whole dataset, only on the train set, otherwise data leakage happens.
- Never use hardcoded paths. Use programmatic paths so the code works on someone else's machine.
- Do not use accuracy as a metric, because credit risk data is usually imbalanced.

**An MCP server.** Connected to his company's experiment tracker, so he can log trained models and look up what models other teams built in the past without opening a separate dashboard.

Six months of personalisation roughly doubled his productivity.

### The problem
What about the rest of his team? New junior data scientists join without his domain knowledge. Ideally they should get his exact agentic coding workflow as it is.

**The manual approach fails.** Rahul would have to tell each junior: copy these skill files into your `.claude/skills` folder, copy this hook file into your `settings.json`, copy this custom command file, copy this MCP JSON content into your project folder. This is manual and error prone. A junior might copy the skills fine but fail at the custom command or the hooks.

**The better approach** is to package the entire workflow into one entity and hand that over. That entity is a **plugin**.

## What a plugin is
A plugin in Claude Code is a folder where you put everything you have created: your skills, MCP tools, hooks, custom slash commands, and **subagents**. You package it and give it to someone. When they install it on their machine, an exact replica is created and they get the same workflow.

**Note:** the instructor forgot to mention subagents in the slide and corrected it later. Subagents are distributed exactly like skills, in an `agents` folder inside the plugin.

## The plugin folder structure
```
my-plugin/
  skills/            your skill files
  hooks/             your hook files
  commands/          your custom slash commands
  agents/            your subagents
  mcp.json           your MCP tool configuration
  .claude-plugin/
    plugin.json      the manifest file
```

**`plugin.json` is required.** Without it the folder is not a valid plugin. It holds the plugin name, version, a short description, author information, repository, and licence.

## What a marketplace is
A marketplace is a place where you store multiple plugins so anyone on your team can access them.

**The analogy:** app stores and apps. The app store is the marketplace and each app is a plugin. Multiple app stores exist (Google, Apple, Xiaomi, Samsung), and multiple marketplaces exist too. You install the marketplace first, then install a plugin from inside it.

**Technically** a marketplace is just a GitHub repository containing a `marketplace.json` file, stored in a `.claude-plugin` folder. That file holds the marketplace name, the owner information, and the list of plugins with their details.

**Two types**
1. **Official marketplace.** Anthropic's own, preinstalled in Claude Code. It had 172 plugins, including Supabase, Vercel, and Figma.
2. **Third party marketplaces.** Built by companies or individuals. You must install these yourself before you can install their plugins.

## Installing plugins and marketplaces
1. Start Claude Code and run `/plugin`.
2. You get three tabs: **Discover**, **Installed**, and **Marketplaces**.
3. To add a third party marketplace, go to Marketplaces and choose **Add marketplace**, then paste the GitHub repository URL and press enter.
4. In the demo, adding a second marketplace with 12 plugins made the Discover tab show 184 plugins instead of 172.
5. Go to Discover and select the plugin you want to install.

## The last feature: delete expense
1. `/create-spec 9 delete-expense`. The branch and spec were created and reviewed.
2. Plan mode: "Read this file and come up with an implementation plan."
3. Implemented and tested. Every transaction now has a Delete button with a confirmation step. Deleting updated the totals correctly.
4. Testing and code review were skipped in the video for time.
5. `/ship-feature` handled everything: pull request created, merged, branch deleted, back on main.

**The website is now complete** with login, registration, profile, add transaction, edit transaction, delete transaction, and filtering.

## Deployment: Vercel did not work
The plan was to deploy to Vercel using its plugin. **Vercel is more suitable for frontend frameworks such as Next.js.** It runs Flask applications as serverless functions, where each request triggers a new function that completes and shuts down. The website's functionality would not work properly.

## Deployment with Railway
Railway is good for Flask deployments and has its own Claude Code plugin.

**The steps**
1. Create a Railway account. It is recommended to sign in with your GitHub account because that is where your project lives.
2. Install the Railway CLI by pasting its install command in your terminal.
3. Run `railway login`. Say yes to opening the browser and authorise.
4. Verify with the check command. It printed "Logged in as campusx <email>".
5. Install the Railway marketplace with the given command. Check it under Marketplaces.
6. Install the Railway plugin, either by browsing or by running the command. It asks whether to install for the user scope or the project scope. The third option, "install for you in this repo only", was chosen.
7. Give the prompt: **"Deploy this Flask application to Railway and give me a public URL."**

**What the plugin did on its own.** It authenticated, found no linked project, added gunicorn to requirements, created a Procfile, wrote a list of deployment tasks, and executed them step by step.

**The result.** The app deployed successfully. A new account was created on the live site, an expense was added, edited, and deleted, and the Analytics coming soon page loaded. Everything that worked locally worked on the server.

**An important caveat.** Railway warns that SQLite is ephemeral. Every time you redeploy, your database is wiped. Using PostgreSQL or MySQL avoids this problem.

**Why this shows the value of plugins.** Doing this by hand, from creating an account through to a complete deployment, would easily take half an hour on a new platform. One plugin install and one prompt handled all of it.

## Some good plugins to explore
- **Superpowers.** Very useful, improves your software development workflow.
- **Frontend Design.** Improves the frontend designs Claude generates.
- **Context7.** Gives you up to date documentation for all libraries.
- **Code Simplifier.** Simplifies your code for better readability.
- **Skill Creator.** Used to create new skills.
- **GitHub.**
- **Playwright.** For browser automation.

Do not install every plugin. Install only the ones that fit your workflow.

## Closing
The playlist took about one and a half to two months. The instructor's goal was to give an overview of agentic coding, the new way of building software, so you can implement it in your own workflow.

He also mentioned that organised notes for this playlist, around 150 pages, are available to purchase from the CampusX website. The link is in each video's description.
