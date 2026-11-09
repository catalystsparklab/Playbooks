# Beginner's guide to using local models and agents

This guide walks you through installing Ollama and running a local AI model, connecting that model to VS Code with the Cline agent, and using Git to track your projects.

---

## Part 1: Install and Run Ollama

Ollama is a free tool and model engine that downloads and runs AI models on your own computer, so your code and prompts stay local.

### Check Your Computer
- Confirm your operating system (Windows, macOS, or Linux).
- Aim for at least 16 GB of RAM. The model needs about 7 GB of storage, plus extra space for other files.
- A dedicated GPU (NVIDIA or Apple Silicon) will make responses faster, but it is not required.

### Install Ollama
- Go to the official Ollama website and download the installer for your system.
- Run the installer and follow the prompts.
- Open a terminal and run `ollama --version` to confirm the install.
- Update to the latest version, since older versions may have bugs with some models.

### Download and Run the Model
**For the sake of accessibility,** this guide uses a smaller model, `qwen3.5:9b`. This model works for 8-12GB setups. It balances performance and speed with a smaller size footprint.
**ONLY USE LARGER MODELS (9B+) if you have 12GB+ of VRAM on your GPU. You can use system RAM to use for running the context window, while the model still lives in GPU memory, but this will slow down the model's output significantly.**

- Download the model with `ollama pull qwen3.5:9b`.
- Test it with `ollama run qwen3.5:9b`, then type a short question.
- Type `/bye` to exit.

### Confirm the Local Server
- Ollama runs a local server at `http://localhost:11434`. You can check the server is running by going to `http://localhost:11434` in a browser. It should say "Ollama is running."
- Check that the model is installed with `ollama list`.


### Troubleshooting
- "Command not found": restart the terminal, or check that Ollama is in your system PATH.
- The model prints code or tool text instead of acting: update Ollama, or ask your teacher about installing an older version.
- Slow responses: close other heavy programs, or try a smaller model.

---

## Part 2: Configure VS Code to Use the Cline Agent

Cline can read your project files, plan changes, and run commands, but it asks for your approval before each action. Connecting it to Ollama keeps the agent running on your computer.

### Install VS Code and Cline
- Download and install Visual Studio Code (if you don't already have it)
- Open the Extensions view (`Ctrl+Shift+X` or `Cmd+Shift+X`).
- Search for "Cline" and select **Install**.
- Click the Cline icon in the left sidebar.

### Connect Cline to Ollama
- Open Cline's settings (the gear icon in the Cline panel).
- Set **API Provider** to **Ollama**. **It should automatically find the model you just downloaded and give it a default context window of 32768 (32K token context window.)**

#### Manual config
- Set **Base URL** to `http://localhost:11434`.
- Set **Model** to `qwen3.5:9b`.
- Set the **Context Window** to match the value in your Modelfile, such as 32768.
- If the model does not appear, confirm it with `ollama list` in your terminal to make sure the model was pulled

### Test the Agent
- Open a small project folder in VS Code.
- Before sending a message to Cline, look above the chat bubble at the "*Auto Approve*" button. Click on it and make sure "Executing Comamnds" and "Edit Files" are turned **OFF**. This prevents the agent from editing your files without permission.
- Give Cline a small, clear task, such as "Add a comment to the first function in this file." If you just want a suggestion for your code, you can prompt something like, "This function at line 'x' is having syntax errors and I don't know why. Take a look at it and describe what you htink might be wrong. I only want a suggestion so don't try to edit the file."
- Read the plan Cline proposes, then approve or reject each file edit and terminal command.
- If Cline stops after one or two steps, check that the context window isn't full. (There is a status bar at the top of the chat interface that shows how full the context is.)

### Good Habits
- Start in Plan mode to see what the agent will want to do before it makes changes.
- Keep tasks small and specific.
- Read all code the agent writes, and make sure you understand it before accepting it.
- -***Never paste passwords, API keys, or private school information into prompts!***-

---

## Part 3: Using Git on Your Projects

Git tracks changes to your files so you can see your history, undo mistakes, and work safely with others.

Below is a guide for how you can set it up manually, but there's also a guide for having your agent do it!

### Agent Setup

Copy everything inside the box below into Cline's chat box, with your project folder open in VS Code. Cline will ask you to approve each file write and terminal command, so review each one before approving.

```text
You are helping a high school student set up a local AI coding environment and version control in this project. Work step by step, explain each step briefly, and wait for my approval before running any command that installs software, deletes files, or changes Git history.

## Goal
Initialize Git in this project and make a first commit.

## Initialize Git
1. Run `git --version`. If Git is not installed, stop and tell me to install it from git-scm.com.
2. Check whether this folder is already a repository by running `git status`. If it is, do not run `git init` again. Tell me and continue.
3. If it is not, run `git init`.
4. Check your identity with `git config user.name` and `git config user.email`. If either is empty, ask me for my name and email, then set them for this repository only (without `--global`).
5. Create a `.gitignore` file if one does not exist. 
## Rules
- Never run `git push`, `git reset --hard`, `git push --force`, or `rm` without my explicit approval.
- Never commit secrets, passwords, or API keys. If you find one in a tracked file, tell me and stop.
- Keep explanations short and use plain language that a high school student can follow.
- When you finish, give me a short checklist showing which steps passed and which need my action.
```

### Manual Setup

#### Install and Set Up Git
- Download Git from the official website and install it with the default options.
- Set your name and email:
  - `git config --global user.name "[Your Name]"`
  - `git config --global user.email "[your email]"`

###3 Start a Repository
- Open your project folder in a terminal.
- Run `git init` to create a repository.
- Create a `.gitignore` file to exclude files you don't want tracked, such as large model files, `.env` files, and secrets.

#### The Basic Workflow
- Check the status of your files with `git status`.
- Stage changes with `git add [file name]` or `git add .`.
- Save a snapshot with `git commit -m "[short description]"`.
- Review your history with `git log`.

#### Branches
- Create a branch for a new feature with `git switch -c [branch name]`.
- Switch between branches with `git switch [branch name]`.
- Merge finished work back into the main branch.

#### Connect to GitHub (Optional)
- Create a free GitHub account and a new empty repository.
- Link your local project with `git remote add origin [repository URL]`.
- Upload your work with `git push -u origin main`.

#### Common Mistakes
- Committing secrets or large files.
- Writing vague commit messages such as "stuff."
- Forgetting to pull changes before pushing.

---

## Next Steps
- Use Cline to help with one feature, then track that feature in Git on its own branch.
