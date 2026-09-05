# Video 2: How to Setup Claude Code on your System

**Duration:** about 27 minutes
**Link:** https://www.youtube.com/watch?v=YjLF6jTyAVk

## Main idea
This video sets up everything needed for the playlist. You install Claude Code, set up the starter project, create a GitHub repository, and learn a free way to run Claude Code.

## Step 1: Install Claude Code
1. Search for Claude Code on Google and open the official page.
2. Read the home page once to understand the tool.
3. Copy the install command shown on the home page.
4. Paste it into your terminal or command prompt and press enter.
5. Wait for the install to finish.

## Step 2: First run
- Type `claude` in the terminal to start it.
- The first run asks if you trust the folder and its files. Answer yes.
- It may ask you to log in. A browser page opens and you click authorize.

## Cost note
- Claude Code is paid software. The plan costs around 2000 rupees per month.
- You need a Claude account and an upgraded plan.
- A free workaround is shown at the end of the video.

## Step 3: Set up the project
1. Download the starter project zip from the Google Drive link in the video description.
2. Extract the folder to your desktop.
3. Open the folder in VS Code.
4. Open a new terminal inside VS Code and type `claude`.

## Step 4: Bash mode
Inside a Claude Code session you can run normal shell commands.
- Press `!` (shift and 1) to enter bash mode.
- Type your shell command and run it.

**Why bash mode matters**
- Commands run in bash mode become part of the Claude conversation history.
- You can later ask Claude questions about those commands, for example which libraries were installed.
- If you run the same commands in a separate terminal window then Claude knows nothing about them.

## Step 5: Project environment setup
Run these commands in bash mode.
1. `python3 -m venv venv` to create a virtual environment.
2. `source venv/bin/activate` to activate it.
3. `pip install -r requirements.txt` to install dependencies.
4. `python3 app.py` to run the Flask app.
5. Open the browser at the local URL. The project uses port 5001.

## What the starter project has
- A finished landing page.
- A registration page UI.
- A login page UI.
- The login and registration logic do not work yet.

**Why start from a partial project**
In a real company you join a project that is already partly built. Starting from a partial project simulates real work.

## Step 6: Create the GitHub repository
1. Go to github.com and create a new repository.
2. The repository in the video is named `Spendly` with the description that it is an expense tracking application built using Claude Code.
3. Make sure Git is installed on your machine.
4. Run these commands in bash mode.
   - `git init`
   - `git add .`
   - `git commit -m "initial commit"`
   - `git remote add origin <your repo url>`
   - `git push origin main`

Push every new feature to this repository. It helps at deployment time and it is good practice.

## Step 7: Understand the project with Claude
Three questions the instructor always asks on a new codebase.
1. **What does this project do?** Claude reads the files and explains the purpose.
2. **What tech stack does this project use?** In this project the answer is Python 3.11 with Flask, SQLite for the database, Jinja templates, vanilla CSS, Google Fonts, and pytest with pytest-flask for testing.
3. **Explain the project structure to me.** Claude gives a folder hierarchy and the key design decisions.

## How to run Claude Code for free
Claude Code is paid because it uses Anthropic models such as Opus, Sonnet, and Haiku. You can swap in open source models instead.

**Method 1: Ollama cloud models**
1. Install Ollama using the command on the Ollama home page.
2. Exit your Claude session with `/exit`.
3. Run `ollama launch claude` in the terminal.
4. Pick one of the offered cloud models. Qwen 3.5 is good at coding.
5. Connect your device when asked.
6. Claude Code opens and runs on the chosen model.

**Limitation:** Ollama cloud usage is tracked and limited. A single message can use a few percent of your quota. You will run out quickly.

**Method 2: Local models**
1. Open the Ollama models page and pick a coding model such as Qwen 3 Coder or Qwen 2.5 Coder.
2. Download it with `ollama pull <model name>`.
3. Pick a size that fits your RAM. With 16 GB of RAM a 14 GB model is possible but slow.
4. Run `ollama launch claude` and select the local model from the list.

**Limitation:** local models are slow unless you have a good GPU and plenty of RAM. Quality is also lower.

**Other options mentioned:** OpenRouter, and a free competitor tool called OpenCode.

The instructor still recommends paying for the real plan if you can afford it.

## How to start work from now on
- Paid plan: open the terminal and type `claude`.
- Ollama method: type `ollama launch claude`.
