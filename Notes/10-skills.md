# Video 10: Claude Code Skills Full Guide

**Duration:** about 50 minutes
**Link:** https://www.youtube.com/watch?v=JN7QCdvJwwM

## Main idea
Skills give Claude domain specific expertise. They work in Claude Code, in the regular Claude chatbot, and in Claude Cowork, so the concept applies across the whole Claude ecosystem.

## The problem skills solve
Claude, ChatGPT, and Gemini are general purpose language models. They can reason, write, and code across many domains. The gap is between general capability and reliable high quality output for a specialised task.

**Example: making a PowerPoint.** Claude knows what a presentation is, how to structure slides, and which Python library to use. But it does not know your company's layout rules, font choices, or when to use a chart instead of a table. It has the general capability but not your specialised skill.

The same gap shows up in web design, data analysis, document writing, and code review.

## Why a long prompt does not fix it
Five problems with putting all the instructions in a detailed prompt.
1. You have to retype it every time, and mistakes creep in.
2. If you save it as a system prompt it sits in your context window forever and eats space even when unused.
3. You cannot bundle resources such as reference images or design files with a prompt.
4. Prompts are personal. They are hard to share, version, and improve as a team.
5. Prompts do not compose. Combining three specialised tasks into one giant prompt confuses the model and performance drops.

## What a skill is
Skills are reusable file based resources that provide Claude with domain specific expertise such as workflows, context, and best practices. They turn a general purpose agent into a specialist.

In simple terms a skill is a folder holding files that tell the model how to carry out a specialised task. Unlike prompts, skills load on demand.

## The folder structure
```
project/
  .claude/
    skills/
      my-skill/
        SKILL.md          (required)
        scripts/          (optional, e.g. Python scripts)
        templates/        (optional, e.g. design guidelines, reference images)
```

`SKILL.md` is required. Without it the skill will not work.

## What SKILL.md contains
**1. YAML front matter at the top.** Two fields.
- `name` is how Claude identifies the skill.
- `description` is the most important part. Claude reads it to decide when to load the skill. Write the trigger clearly, for example "whenever the user asks to build any kind of PowerPoint presentation, load this skill".

**2. Markdown body.** All the detailed instructions, coding patterns, common mistakes, validation steps, and links to any supporting files in the other folders.

## Progressive disclosure
This is how skills load. The core idea is: do not present information until the moment it is needed.

**Level 1.** The YAML front matter of every skill loads into context at the start of a session. It is tiny, so ten skills means ten short descriptions. Claude always knows which skills exist and when to trigger them.

**Level 2.** When your message matches a skill, Claude loads the body of that skill's `SKILL.md`.

**Level 3.** If the body references a script or resource, Claude fetches that file too.

This is why skills do not burn your context window the way a system prompt does.

## Two types of skills by scope
| Type | Location | Availability |
|---|---|---|
| Personal skills | `.claude/skills/` in your home directory | All your projects |
| Project skills | `.claude/skills/` in the project | Only that project, and shareable with your team through Git |

## Three ways to create a skill
**1. Manually.** Make the folder, write `SKILL.md`, and describe the workflow in detail. Not recommended for beginners because you do not yet know the format or what works.

**2. Using Claude, which is recommended.** Open the Claude chatbot, click the plus icon, go to Skills, and pick **Skill Creator**. Skill Creator is itself a skill whose job is to build other skills.

**3. From community sources.** Search for "Claude skills marketplace" and you will find sites such as skillsmp with skills for marketing, data, and coding.

**Security warning:** read a community skill fully before using it. There have been reports of a skill leaking a project's API keys. A safer option is Anthropic's own public skills repository.

## The four steps to build a skill
1. **Identify the need.** Only build a skill for a specialised task you repeat often.
2. **Create the directory** with `SKILL.md` and any supporting files.
3. **Test the skill.** This is an important step and a large topic on its own.
4. **Iterate and improve.** No skill is perfect on the first try. Expect four or five rounds.

## How skills fix all five prompt problems
| Prompt problem | How skills solve it |
|---|---|
| Retyping every time | The file loads automatically when needed |
| Context window burn | Only the short description is always loaded |
| Cannot bundle resources | Folders can hold any files, code, or images |
| Cannot share or version | The folder goes into Git and the team collaborates on it |
| Cannot compose | One skill's SKILL.md can link to another skill |

## The practical part: the profile page
**The goal.** After login the user still lands on the home page. There should be a profile page or dashboard instead.

**The plan for the video**
1. Build the profile page without a skill.
2. Build a frontend design skill.
3. Build the profile page again with the skill and compare.

**The profile page spec** replaced the `/profile` stub with a fully designed page showing static hardcoded data.
- A user info card with the user's details.
- Summary stats showing total spend, number of transactions, and the top spending category.
- A transaction history table listing all expenses.
- A category breakdown showing spend per category.

All the data is hardcoded for now. It does not come from the database.

**First attempt without the skill.** Ran `/create-spec profile-page-design`, replaced the generated spec with the prepared one, went into plan mode, and implemented. Claude also generated and ran six pytest tests on its own, which was not asked for. The lesson is to state clearly in the spec that you do not want tests.

## Creating the frontend design skill
The instructor used the Skill Creator in the Claude chatbot.
1. Opened Claude, went to Skills, and selected Skill Creator.
2. Chose "create a new skill from scratch".
3. Chose to describe the skill in his own words.
4. Answered three questions in detail: what the skill does, when it triggers, and what success looks like.
5. Claude also read the GitHub repository to understand the code structure.
6. Claude asked three follow up questions: should the skill produce Flask, Jinja2, and vanilla CSS (yes, stay true to the project), which icon library to prefer, and whether to test it on real prompts before finalising (no).
7. Claude produced the finished skill.

Then the markdown was copied into `.claude/skills/frontend-design/SKILL.md` in the project.

## Rewinding the work
To rebuild the page with the skill, the earlier work had to be undone. The `/rewind` command goes back in your code and conversation.
1. Run `/rewind`.
2. Choose how far back to go. The instructor went back to the very start of the session.
3. Choose whether to restore only the conversation, or the code and the conversation together.

The generated spec file and all the code progress were rolled back.

## Second attempt with the skill
**Important:** after creating a new skill you must restart Claude Code before it can be used, exactly like with commands.

In plan mode Claude printed "Excellent, phase one complete. Now let me invoke the frontend design skill as requested for design guidance" and the skill loaded automatically during planning.

**The result.** A mild improvement. Claude decided on its own that the category breakdown could sit alongside the transaction table instead of taking a full card. The fonts looked similar and the icons did not get used. The instructor was honest that the difference was small because the application is very basic. The difference shows on properly complex tasks.

## Two more small fixes
1. "Make sure the logged in user automatically gets redirected to the profile page." This worked, and signing out then trying to reach the profile page correctly redirected back to login.
2. A snapshot was taken and then: "Don't want to show this welcome message after login." Fixed.

Then the usual Git flow: commit, push, pull request, merge, switch to main, pull, and delete the branches. Two branches existed because the whole process was run twice.

## Important news: commands and skills have been merged
Anthropic has merged the two concepts.

- Going forward the `commands` folder will not exist. Everything lives in the `skills` folder.
- A command is built exactly like a skill: a folder, a `SKILL.md` file, and its resources.
- Both skills and commands can be invoked with `/`.
- The structure was already identical. Both are a Markdown file with YAML front matter, a description, and a detailed body. Anthropic saw no reason to keep two formats.

**To make a skill behave like a command only,** add this flag to the YAML front matter:

```yaml
disable-model-invocation: true
```

This stops Claude from picking up the skill on its own, so only you can invoke it.

**Anthropic's recommendation:** do not create commands. Create only skills.

The playlist project keeps its existing commands folder, but future projects will not have one.
