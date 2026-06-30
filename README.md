# Claude Code Sandbox

A sandboxed development environment for data science with Claude Code, R, Python, and
Quarto. Claude Code runs inside a Docker container and cannot access anything on your
computer outside the container, which limits the damage that could result from prompt
injection attacks (malicious content in files tricking Claude into doing something
harmful).

**What you get:**
- R with Tidyverse, brms, arrow, duckdb, and other common packages
- Python with uv for package management
- Quarto for documents, articles, and presentations
- Claude Code (Anthropic's AI coding assistant)
- Full internet access for web search and APIs
- A designated folder on your Mac for data files that don't go to GitHub
- All your work persists across restarts

---

## How the pieces fit together

It helps to understand what lives where before you start.

**On your Mac:**
```
~/claude-setup/              ← this repo; used to launch the container, not for working in
  .devcontainer/
    Dockerfile
    devcontainer.json
  CLAUDE.md
  README.md

~/claude-data/               ← data files you want Claude to access but not push to GitHub
                               (appears as /data inside the container)
```

**Inside the container:**
```
/root/                       ← your home directory inside the container
  mirror_tsd/                ← a project, cloned from GitHub
  other-project/             ← another project, cloned from GitHub
  .claude/                   ← Claude Code config (CLAUDE.md lives here)
  .ssh/                      ← your SSH keys for GitHub access
  .gitconfig                 ← your git identity (name and email)

/workspaces/claude-setup/    ← the setup repo mounted into the container
                               ignore this; you don't work here

/data/                       ← your ~/claude-data folder from your Mac, mounted in
```

**On GitHub:**
```
claude-setup                 ← this repo (the template)
mirror_tsd                   ← a project repo
other-project                ← another project repo
```

The key things to remember:
- You work in `/root/yourproject` inside the container, not in `/workspaces/`
- `/workspaces/claude-setup` is just the ignition key — it starts the container but you
  don't work in it
- Projects live inside the container, cloned from GitHub. Your Mac's files (other than
  `/data`) are not accessible.
- Everything under `/root/` persists across container restarts because it is backed by a
  Docker volume.

---

## What you need to know about GitHub

GitHub is a website where people store and share code. Think of it like Google Drive but
for code projects. This repository (which is what GitHub calls a folder of files) contains
everything needed to set up your environment.

**Cloning** means downloading a copy of a repository to your computer. You do this once
and then the files live on your Mac.

---

## Prerequisites

Install these before starting:

1. **Docker Desktop** — [download here](https://www.docker.com/products/docker-desktop/).
   Open it after installing and make sure the whale icon appears in your menu bar.

2. **Visual Studio Code** — [download here](https://code.visualstudio.com/).

3. **Dev Containers extension for VSCode** — Open VSCode, click the Extensions icon in
   the left sidebar, search for "Dev Containers" (by Microsoft), and install it.

4. **An Anthropic API key** — Go to [console.anthropic.com](https://console.anthropic.com),
   sign in, click API Keys in the left sidebar, and create a new key. Copy it — you only
   see it once. Save it somewhere safe (a password manager is ideal).

   Note: this is separate from a Claude.ai subscription. You need to add a credit card
   and purchase credits at console.anthropic.com.

---

## First-time setup

Do these steps once. After that, daily use is just opening VSCode.

### Step 1: Install Git

Open Terminal (press Cmd+Space, type "Terminal", press Enter) and run:

```bash
xcode-select --install
```

A dialog will appear asking to install Command Line Tools. Click Install. This installs
Git among other things.

### Step 2: Clone this repository

In Terminal, run:

```bash
git clone https://github.com/YOUR-USERNAME/claude-sandbox.git ~/claude-sandbox
```

Replace `YOUR-USERNAME` with your GitHub username and the repo name if you've renamed it.
This downloads the repository to a folder called `claude-sandbox` in your home directory.

### Step 3: Set your Anthropic API key

In Terminal:

```bash
echo 'export ANTHROPIC_API_KEY=sk-ant-your-key-here' >> ~/.zshrc
source ~/.zshrc
```

Replace `sk-ant-your-key-here` with your actual key. Verify it worked:

```bash
echo $ANTHROPIC_API_KEY
```

It should print your key back.

### Step 4: Create your data folder

This folder is where you put data files that you want Claude to access but don't want to
upload to GitHub (large files, sensitive data, etc.). Inside the container they appear
at `/data`.

```bash
mkdir ~/claude-data
```

### Step 5: Open the container in VSCode

1. Open VSCode
2. Go to **File → Open Folder** and select the `claude-sandbox` folder
3. Press **Cmd+Shift+P** to open the command palette
4. Type "Reopen in Container" and press Enter

VSCode will build the container. **This takes 20-30 minutes the first time** because it
is compiling R packages from source. Subsequent starts take a few seconds. You can watch
the progress by clicking "show log" in the notification that appears.

When it's done, the bottom-left corner of VSCode will show a green bar saying
**Dev Container: claude-sandbox**. You're now inside the sandbox.

### Step 6: Set your git identity

Open a terminal inside VSCode (**Terminal → New Terminal**) and run:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Use the same email address as your GitHub account.

### Step 7: Set up GitHub access from inside the container

You need to generate an SSH key inside the container so it can push and pull from GitHub.

In the VSCode terminal (inside the container):

```bash
ssh-keygen -t ed25519 -C "claude-sandbox"
```

Press Enter three times to accept all defaults and skip the passphrase.

Then display your public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

Copy the entire output (it starts with `ssh-ed25519`).

Now go to GitHub:
1. Click your profile photo (top right) → **Settings**
2. Click **SSH and GPG keys** in the left sidebar
3. Click **New SSH key**
4. Give it a name like "claude-sandbox"
5. Paste the key and click **Add SSH key**

Test it from the container terminal:

```bash
ssh -T git@github.com
```

Type `yes` when asked. You should see: `Hi username! You've successfully authenticated.`

### Step 8: Customize your Claude instructions

A template `CLAUDE.md` file has been copied to `~/.claude/CLAUDE.md`. This is where you
tell Claude about your working style, preferences, and constraints. Open it in VSCode:

```bash
code ~/.claude/CLAUDE.md
```

Edit it to reflect your own background and preferences. Here is what to think about:

**Who you are and what you do**
What is your field? What kinds of projects do you typically work on? What do you
absolutely never use (certain languages, tools, or approaches)?

Example: *"I'm a biologist working on genomics pipelines. I never use Windows-style line
endings or MATLAB."*

**Language and tool preferences**
Which programming languages do you use, and in what order of preference? Within those
languages, which libraries or frameworks do you favor? Are there any you dislike or want
to avoid?

Example: *"I prefer Python. For data work I use pandas and polars; avoid using base Python
loops where vectorized operations are possible."*

**Coding style**
How do you like your code written? Do you want comments explaining the why, not just the
what? Do you prefer short functions or longer ones? Explicit variable names?

Example: *"Write verbose, well-commented code. Prioritize readability over cleverness."*

**What Claude should always ask before doing**
Are there actions you want Claude to check with you before taking? Pushing to git, deleting
files, installing new packages, and making large structural changes to a project are common
ones.

Example: *"Always confirm before installing new packages or pushing to git."*

**Reproducibility and project structure**
How do you organize projects? Do you have a preferred folder structure? How do you handle
random seeds, file paths, and dependencies?

**Your level in each language**
This helps Claude calibrate its explanations. Are you an expert in R but a beginner in
Python? Tell it.

Example: *"I'm fluent in R but still learning Python — explain Python decisions, don't
just write the code."*

The included `CLAUDE.md` is a worked example from a data scientist and economist. Use it
as a reference, keep what applies to you, and rewrite the rest.

---

## Daily use

### Starting a session

Open VSCode. If you see the green **Dev Container: claude-sandbox** bar in the bottom
left, you're already in the container. If not, press **Cmd+Shift+P** and run
**Dev Containers: Reopen in Container**.

### Starting or continuing a project

To clone a project from GitHub (first time):

```bash
cd ~
git clone git@github.com:yourname/yourproject.git
```

Then open it in VSCode: **File → Open Folder** → `/root/yourproject`.

All your cloned projects live under `/root/` and persist across restarts. You only clone
once.

### Using Claude Code

Open a terminal in VSCode and run:

```bash
claude
```

### Using data files

Put files in `~/claude-data` on your Mac. They appear at `/data` inside the container.
Add `/data` to your `.gitignore` in any project that references these files.

### Verifying the sandbox is working

Run these in the container terminal:

```bash
uname -a          # should say Linux, not Darwin
ls /Users         # should be empty or error
ls /data          # should show your ~/claude-data contents
claude --version  # confirms Claude Code is running inside the container
```

---

## After restarting your Mac

Just open VSCode and reopen in container. Everything — cloned repos, SSH keys, installed
packages, git config — is preserved in the Docker volume.

---

## Setting up on a new machine

1. Install Docker Desktop, VSCode, and the Dev Containers extension
2. Clone this repository
3. Set your `ANTHROPIC_API_KEY` environment variable
4. Create `~/claude-data`
5. Open in VSCode and reopen in container (wait for build)
6. Generate a new SSH key inside the container and add it to GitHub (Step 7 above)
7. Clone your projects

---

## Troubleshooting

**The green Dev Container bar is missing**
Press Cmd+Shift+P → Dev Containers: Reopen in Container.

**`Permission denied` when pushing to GitHub**
Your SSH key isn't set up. Follow Step 7 above. Run `ssh -T git@github.com` to test.

**`ANTHROPIC_API_KEY` not found inside the container**
Make sure you set it in `~/.zshrc` on your Mac and that you've opened a new terminal
after doing so. Rebuild the container after setting it.

**The container is slow to start after a Mac restart**
Make sure Docker Desktop is running first (whale icon in menu bar).

**Something is broken and you want a clean start**
In Docker Desktop, go to Volumes, find `claude-sandbox-home`, and delete it. Then
rebuild the container. Note: this deletes your cloned repos and SSH keys inside the
container — make sure everything is pushed to GitHub first.
