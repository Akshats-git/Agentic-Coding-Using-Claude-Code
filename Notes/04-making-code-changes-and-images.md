# Video 4: Making Code Changes using Claude Code and Adding Images as Context

**Duration:** about 22 minutes
**Link:** https://www.youtube.com/watch?v=-Lt-ntUDj-g

## Main idea
This is the first hands on video. The goal is to improve the landing page of the expense tracker website.

## Three things covered
1. How to change existing code with Claude Code.
2. How to create new website pages.
3. How to use the multimodal ability of Claude Code by giving it an image.

## The plan for the landing page
1. Replace the hero section with a new design taken from an image.
2. Add a "See how it works" button that opens a popup with a YouTube video.
3. Add two footer links called Terms and Conditions and Privacy Policy.
4. Create the actual pages for both links.

## The working flow used
1. Start a new session with `claude`.
2. Rename the session with `/rename landing page improvements`.
3. Make the changes through prompts.
4. Commit at every milestone.
5. Push to the GitHub repository at the end.

## Important tip: write prompts in advance
Do not type prompts by hand inside Claude Code.
- You may make a mistake or forget an important detail.
- Instead plan what you want, write it in your own words in a chatbot such as ChatGPT or Claude, get a refined and polished prompt, then paste that into Claude Code.
- Keep all prompts for a feature saved in one file.

## The @ mention
Use the `@` symbol before a file path to tell Claude exactly which file to work on.
- Example: `Add two links to the footer in @templates/base.html`
- Without `@` Claude may pick the wrong file.
- The `@` mention makes the change deterministic.
- You do not need `@` for a file that does not exist yet.

## Task 1: footer links
The prompt said to add two plain text links called Terms and Conditions and Privacy Policy, with no special styling, pointing nowhere for now, and to change nothing else on the page.

Claude showed the new lines in green, asked permission, and made the edit after approval.

Then a commit was made with `git add` and `git commit -m`.

**Note:** Claude Code is smart enough to handle a shell command even if you forget to switch to bash mode with `!`.

## Task 2: the Terms and Conditions page
The prompt asked Claude to create the page, add a new route in `@app.py`, create a template with generic terms content suitable for a personal expense tracking app, and include specific sections.

Claude did three things.
1. Added a new Flask route in `app.py`.
2. Created a new template file `terms.html`.
3. Updated the link in `base.html`.

The first result looked ugly. A follow up prompt fixed it: "Also make sure the appearance of @templates/terms.html matches the website's theme." Claude then edited the CSS.

## Task 3: the Privacy Policy page
The same prompt pattern was used. This time the theme instruction was included from the start, so the page matched the site straight away.

Commit after each finished page.

## Task 4: the hero section from an image
This shows the multimodal ability of Claude Code.

**Steps**
1. Copy the design image. It can come from your design team, from Figma, or from anywhere online.
2. Press Ctrl and V inside the Claude Code prompt. The image is pasted and a link appears.
3. Add the prompt along with the image.

The prompt used was: "Modify only the hero section in @templates/landing.html and @static/css/landing.css to match this image exactly. Do not touch any other part of the page."

Claude generated the code and the new landing page matched the design image very closely.

## Task 5: the video popup
The prompt listed clear requirements.
- Clicking "See how it works" opens a modal overlay.
- The modal contains an embedded YouTube video.
- Use any placeholder YouTube URL for now.
- The video must be playable inside the modal.
- Clicking the close button or clicking outside closes the modal.
- When the modal closes the video must stop playing and not continue in the background.
- No new libraries or dependencies. Use vanilla JavaScript only.
- Do not modify any other part of the project.

This worked on the first try. The lesson is that clear and detailed requirements give better results.

## Finishing up
1. `git push origin main` to send everything to GitHub.
2. Check the repository in the browser to confirm the new files are there.
3. Run `/usage` to check token usage. This session used about 14 percent.
4. Run `/exit` to close the session.

## A mistake that happened
A wrong CSS file name was given in one prompt, so the project ended up with two CSS files. The fix is simple. Ask Claude to merge the two files and then commit.

**The real lesson:** you give a prompt, Claude gives an output, and the output is not always correct. You improve it step by step inside the same session.

## Closing note
This simple workflow is fine for small edits. It is not the right way to build complex features. Later videos cover plan mode, spec creation, branches, and pull requests.
