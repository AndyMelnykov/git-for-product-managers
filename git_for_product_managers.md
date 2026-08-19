# Git for Product Managers

A practical guide to using Git and GitLab when working on a local project with VS Code, Codex, Claude Code, or similar AI coding tools.

This guide assumes a simple setup:

- You are the **only contributor**
- Your local project folder is:

```text
C:\Projects\Reddit_agent
```

- You edit the project locally using:
  - VS Code
  - Codex
  - Claude Code
  - other local development tools
- You use **Git** for version control
- You use **GitLab** as the remote repository

The goal is not to become a Git expert. The goal is to understand enough Git to work safely, understand what your tools are doing, recover from common mistakes, and keep your local project synchronized with GitLab.

---

# 1. Git vs GitLab vs GitHub

These are different things.

## Git

**Git** is the version control system running on your computer.

It tracks:

- which files changed
- what changed inside them
- when changes were saved
- who made the changes
- the history of the project

Git works locally even without an internet connection.

## GitLab

**GitLab** is a service that hosts Git repositories remotely.

In this setup, GitLab gives you:

- a remote backup of the repository
- access to the code from another computer
- repository history
- browser-based code viewing
- issues, CI/CD, merge requests, and other collaboration features if you need them later

## GitHub

GitHub is another service that hosts Git repositories.

For this scenario, you are using **GitLab**, so GitHub is not involved.

The important distinction is:

```text
Git = version control technology
GitLab = remote hosting service for Git repositories
GitHub = another remote hosting service for Git repositories
```

---

# 2. The Mental Model

Your setup has three main layers:

```text
C:\Projects\Reddit_agent
        │
        ▼
Local Git repository
        │
        ▼
Remote Git repository in GitLab
```

But Git itself effectively works with four states:

```text
Working directory
      │
      │ git add
      ▼
Staging area
      │
      │ git commit
      ▼
Local repository history
      │
      │ git push
      ▼
GitLab
```

This is the most important concept to understand.

Git does **not** behave like Dropbox or OneDrive.

Changing a file does not automatically synchronize it with GitLab.

You explicitly decide:

1. which changes should be included
2. when they should become a saved version
3. when that version should be sent to GitLab

---

# 3. The Core Workflow

Your normal workflow is:

```text
Edit files
   ↓
Review changes
   ↓
Stage changes
   ↓
Create a commit
   ↓
Push the commit to GitLab
```

In commands:

```powershell
git status
git diff
git add .
git diff --staged
git commit -m "Add market data processing"
git push
```

These commands will cover most of your everyday Git work.

---

# 4. What Is a Commit?

A **commit** is a saved version of your project.

Think of it as a meaningful checkpoint.

For example:

```text
Initial project setup
        ↓
Add Reddit API integration
        ↓
Add post collection
        ↓
Add market classification
        ↓
Add AI analysis
        ↓
Fix duplicate post handling
```

Each commit records the exact changes made since the previous commit.

This gives you a project history.

If an AI agent later breaks something, you can see exactly what changed and potentially return to an earlier working version.

A commit is initially stored **locally**.

It reaches GitLab only after:

```powershell
git push
```

---

# 5. Initial Git Installation

Install **Git for Windows** if it is not already installed.

Then open PowerShell and verify:

```powershell
git --version
```

You should see something similar to:

```text
git version 2.x.x
```

Configure your identity:

```powershell
git config --global user.name "Andy Melnykov"
git config --global user.email "your-email@example.com"
```

Configure `main` as the default branch name:

```powershell
git config --global init.defaultBranch main
```

Check the configuration:

```powershell
git config --global --list
```

You normally do this only once per computer.

---

# 6. Create the GitLab Repository

In GitLab:

1. Select **New project**
2. Select **Create blank project**
3. Name it:

```text
Reddit_agent
```

4. Set visibility to:

```text
Private
```

5. For the simplest initial setup, do not ask GitLab to create:
   - README
   - `.gitignore`
   - license

This keeps the remote repository empty and avoids unnecessary synchronization issues during the initial connection.

GitLab will give you a repository address.

SSH format:

```text
git@gitlab.com:USERNAME/reddit_agent.git
```

HTTPS format:

```text
https://gitlab.com/USERNAME/reddit_agent.git
```

---

# 7. SSH vs HTTPS

You can connect your local Git repository to GitLab using either:

- HTTPS
- SSH

For regular development, **SSH is usually more convenient**.

Once configured, your computer can authenticate with GitLab using an SSH key instead of repeatedly asking for credentials or tokens.

---

# 8. Configure SSH Access to GitLab

Generate an SSH key:

```powershell
ssh-keygen -t ed25519 -C "your-email@example.com"
```

When asked where to save it, pressing Enter normally creates:

```text
C:\Users\YOUR_USERNAME\.ssh\id_ed25519
```

Two files are created:

```text
id_ed25519
id_ed25519.pub
```

Important:

```text
id_ed25519      = private key
id_ed25519.pub  = public key
```

Never share the private key.

Display the public key:

```powershell
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub
```

Copy the complete result.

It should start with something similar to:

```text
ssh-ed25519 AAAAC3...
```

In GitLab, open your SSH key settings and add the public key.

Then test the connection:

```powershell
ssh -T git@gitlab.com
```

The first connection may ask whether you trust the host.

Enter:

```text
yes
```

If authentication works, GitLab should recognize your account.

---

# 9. Turn the Local Folder Into a Git Repository

Move into your project folder:

```powershell
cd C:\Projects\Reddit_agent
```

Check the contents:

```powershell
dir
```

Initialize Git:

```powershell
git init
```

This creates a hidden `.git` directory inside the project.

That directory contains Git's metadata and history.

You should normally never edit `.git` manually.

Set the main branch name:

```powershell
git branch -M main
```

---

# 10. Connect the Local Repository to GitLab

Add GitLab as the remote repository:

```powershell
git remote add origin git@gitlab.com:USERNAME/reddit_agent.git
```

Replace `USERNAME` with your GitLab username or namespace.

Check the remote:

```powershell
git remote -v
```

You should see something similar to:

```text
origin  git@gitlab.com:username/reddit_agent.git (fetch)
origin  git@gitlab.com:username/reddit_agent.git (push)
```

`origin` is simply the conventional name for the main remote repository.

Conceptually:

```text
origin = our GitLab repository
```

---

# 11. Create a .gitignore File

Before the first commit, create:

```text
C:\Projects\Reddit_agent\.gitignore
```

`.gitignore` tells Git which files should **not** be tracked.

Example:

```gitignore
# Secrets
.env
.env.*
!.env.example

# Python
.venv/
venv/
__pycache__/
*.py[cod]
.pytest_cache/
.mypy_cache/

# Node.js
node_modules/

# IDE
.vscode/
.idea/

# Operating system
.DS_Store
Thumbs.db

# Logs and temporary files
*.log
*.tmp
temp/
tmp/

# Build artifacts
build/
dist/
```

---

# 12. Protect Secrets

This is particularly important for AI projects.

A local `.env` file may contain:

```text
OPENAI_API_KEY=...
REDDIT_CLIENT_ID=...
REDDIT_CLIENT_SECRET=...
```

That file should normally never be committed.

Your `.gitignore` should therefore contain:

```gitignore
.env
.env.*
```

But you can safely keep an example:

```text
.env.example
```

For example:

```text
OPENAI_API_KEY=
REDDIT_CLIENT_ID=
REDDIT_CLIENT_SECRET=
```

This tells someone which variables are required without exposing the actual credentials.

A useful rule:

> Never commit API keys, passwords, access tokens, private certificates, or production credentials.

Also remember that deleting a secret in a later commit does not necessarily remove it from earlier Git history.

Prevention is much easier than cleanup.

---

# 13. Create the First Commit

Check the repository:

```powershell
git status
```

Git will show untracked files.

Stage the files:

```powershell
git add .
```

Check what is staged:

```powershell
git status
```

Review the exact staged changes:

```powershell
git diff --staged
```

Create the commit:

```powershell
git commit -m "Initial project setup"
```

Now the project has its first local Git version.

---

# 14. Push the First Commit to GitLab

Run:

```powershell
git push -u origin main
```

This does two things:

1. pushes the local `main` branch to GitLab
2. establishes `origin/main` as the upstream branch

After this first push, you normally only need:

```powershell
git push
```

---

# 15. Your Normal Daily Workflow

At the beginning of a work session:

```powershell
cd C:\Projects\Reddit_agent

git pull --rebase
git status
```

Then work normally using:

- VS Code
- Codex
- Claude Code
- terminal
- another editor

After completing a meaningful piece of work:

```powershell
git status
git diff
```

Review the changes.

Then stage them:

```powershell
git add .
```

Review exactly what will be committed:

```powershell
git diff --staged
```

Commit:

```powershell
git commit -m "Add Reddit post collection"
```

Push to GitLab:

```powershell
git push
```

Your complete routine becomes:

```powershell
cd C:\Projects\Reddit_agent

git pull --rebase

# Work with VS Code / Codex / Claude Code

git status
git diff

git add .
git diff --staged

git commit -m "Describe the completed change"
git push
```

---

# 16. The Five Most Important Git Commands

If you initially remember only five commands, remember these:

```powershell
git status
git diff
git add .
git commit -m "..."
git push
```

Plus one command at the start of your work:

```powershell
git pull --rebase
```

---

# 17. git status

Use:

```powershell
git status
```

frequently.

It tells you:

- which branch you are on
- which files changed
- which files are new
- which files are staged
- which files are not staged
- whether your branch differs from GitLab

For someone working with AI coding agents, `git status` is one of the safest commands you can run.

It changes nothing.

It only reports the current state.

---

# 18. git diff

Use:

```powershell
git diff
```

to inspect changes that have not yet been staged.

It shows:

- removed lines
- added lines
- modified files

For example, after Claude Code makes changes:

```powershell
git status
git diff
```

This allows you to review what the agent actually changed instead of relying only on its summary.

---

# 19. git add

Git does not automatically put every modified file into a commit.

First, you **stage** changes.

To stage everything:

```powershell
git add .
```

To stage one file:

```powershell
git add README.md
```

Example:

```powershell
git add src\reddit_client.py
```

To stage multiple specific files:

```powershell
git add README.md src\reddit_client.py src\market_analysis.py
```

For AI-assisted work, staging specific files can sometimes be safer than blindly running:

```powershell
git add .
```

especially when an agent has touched many files.

---

# 20. Staging Area

The staging area is a temporary selection of changes that will go into the next commit.

Think of it as:

```text
All current changes
        ↓
Select what belongs together
        ↓
Staging area
        ↓
Commit
```

This lets you keep multiple unrelated local changes while committing only one logical group.

---

# 21. Review the Future Commit

After `git add`, run:

```powershell
git diff --staged
```

This is especially important.

`git diff` answers:

> What have I changed?

`git diff --staged` answers:

> What exactly am I about to save in the next commit?

For AI-generated changes, the second question is often more important.

A useful workflow is:

```powershell
git status
git diff
git add .
git diff --staged
git commit -m "..."
```

---

# 22. git commit

Create a commit with:

```powershell
git commit -m "Add Reddit API client"
```

The message should describe the logical change.

Good examples:

```text
Add Reddit API client
Add market analysis prompt
Add post classification
Fix duplicate post processing
Update configuration documentation
Refactor sentiment analysis module
Remove obsolete API integration
```

Weak examples:

```text
Changes
Update
Work
Fix
Latest
New version
```

A simple convention is enough:

```text
Add ...
Fix ...
Update ...
Remove ...
Refactor ...
```

---

# 23. Make Small, Logical Commits

Avoid working for several days and then creating:

```text
Massive update
```

Instead, create checkpoints such as:

```text
Add Reddit authentication
Add subreddit configuration
Add post collection
Add data normalization
Add AI analysis
Fix duplicate handling
```

Small commits are easier to:

- review
- understand
- debug
- revert
- compare
- explain to an AI agent

They also create useful recovery points.

---

# 24. git push

A commit exists locally until you send it to GitLab.

Use:

```powershell
git push
```

Conceptually:

```text
Local commit
    ↓
git push
    ↓
GitLab
```

After a successful push, GitLab contains the same committed history as your local repository.

---

# 25. git pull

If GitLab contains commits that your computer does not yet have, you need to retrieve them.

For this workflow, use:

```powershell
git pull --rebase
```

Conceptually:

```text
GitLab
   ↓
git pull --rebase
   ↓
Local repository
```

Because you are the only contributor, there will usually be nothing new.

Still, using it before starting work is a good habit, particularly if you sometimes:

- work on another computer
- edit files through GitLab
- let another automation push changes
- use cloud development environments

---

# 26. Why Use git pull --rebase?

A regular:

```powershell
git pull
```

may create merge commits in some situations.

For a simple single-contributor repository, this often creates unnecessary history.

Using:

```powershell
git pull --rebase
```

usually keeps the history more linear:

```text
A → B → C → D
```

instead of creating avoidable merge branches.

For your simple setup, `git pull --rebase` is a good default.

---

# 27. Working With Codex or Claude Code

AI coding agents can modify many files quickly.

That makes Git more important, not less important.

A good workflow is:

## Before giving the agent a substantial task

Make sure your current work is saved:

```powershell
git status
```

If necessary:

```powershell
git add .
git commit -m "Checkpoint before agent changes"
git push
```

Now you have a clean recovery point.

## Let the agent work

Claude Code, Codex, or another agent modifies the local files.

## Inspect the result

Run:

```powershell
git status
git diff
```

This tells you exactly what changed.

## Stage the result

```powershell
git add .
```

## Review what will actually be committed

```powershell
git diff --staged
```

## Commit

```powershell
git commit -m "Add market analysis workflow"
```

## Push

```powershell
git push
```

This creates a safe boundary around AI-generated work.

---

# 28. A Useful AI Development Pattern

For significant agent work:

```text
Working version
      ↓
Checkpoint commit
      ↓
AI agent changes files
      ↓
Review git diff
      ↓
Test
      ↓
Commit accepted changes
      ↓
Push
```

This means that if the agent produces poor results, the previous working version is still clearly preserved.

---

# 29. Undo Changes to One File

Suppose an AI agent changed:

```text
src\reddit_client.py
```

and you want to discard those uncommitted modifications.

Run:

```powershell
git restore src\reddit_client.py
```

The file returns to the version from the latest commit.

Warning:

The uncommitted modifications to that file are lost.

---

# 30. Undo All Uncommitted Changes

To restore all tracked files:

```powershell
git restore .
```

This discards the current modifications.

Use it carefully.

Before running it, inspect:

```powershell
git status
git diff
```

---

# 31. Remove a File From Staging

Suppose you ran:

```powershell
git add .
```

but do not want one file in the commit.

Run:

```powershell
git restore --staged path\to\file
```

Example:

```powershell
git restore --staged README.md
```

The changes remain in the file, but the file is removed from the next commit.

To unstage everything:

```powershell
git restore --staged .
```

This does not delete your modifications.

---

# 32. Review Git History

Use:

```powershell
git log --oneline
```

Example:

```text
a531f29 Add Reddit post collection
18a4d71 Add project configuration
772f300 Initial project setup
```

Each line represents one commit.

The value at the beginning is the abbreviated commit ID.

For example:

```text
a531f29
```

A more visual history command is:

```powershell
git log --oneline --graph --decorate --all
```

---

# 33. Commit IDs

Each commit gets a unique identifier.

Example:

```text
a531f29
```

You can use commit IDs when:

- comparing versions
- reverting changes
- inspecting old code
- asking an AI coding agent to analyze a specific change

For example:

```powershell
git show a531f29
```

shows that commit.

---

# 34. Correct the Last Commit Message

If you just created a local commit with a poor message:

```powershell
git commit --amend -m "Add Reddit post collection"
```

This rewrites the latest commit.

This is easiest and safest **before the commit has been pushed**.

Once history has been pushed to GitLab, avoid casually rewriting it unless you understand the consequences.

---

# 35. Undo the Last Local Commit but Keep the Changes

If you committed too early:

```powershell
git reset --soft HEAD~1
```

This removes the latest commit while keeping its changes staged.

You can then modify the files and create a better commit.

Again, this is simplest when the commit has **not yet been pushed**.

---

# 36. What If git push Is Rejected?

You may see something similar to:

```text
rejected
non-fast-forward
```

This means the GitLab branch contains commits that are missing locally.

Usually:

```powershell
git pull --rebase
git push
```

will resolve it.

This situation may happen if you:

- modified README directly in GitLab
- worked from another computer
- used another tool that pushed a commit
- allowed GitLab to create files remotely

---

# 37. Merge Conflicts

A conflict occurs when Git cannot automatically decide how to combine two versions of the same file.

A conflicted file may contain:

```text
<<<<<<< HEAD
your version
=======
other version
>>>>>>> commit-id
```

You need to edit the file manually and decide what the final content should be.

Remove the conflict markers.

Then:

```powershell
git add .
git rebase --continue
```

When the rebase completes:

```powershell
git push
```

If you decide you do not want to continue the rebase:

```powershell
git rebase --abort
```

For a single-contributor project, conflicts should be relatively rare if you avoid editing the same project simultaneously in multiple places.

---

# 38. VS Code Source Control

VS Code provides a graphical interface for most Git operations.

Open the project:

```powershell
code C:\Projects\Reddit_agent
```

Then open **Source Control** in the left sidebar.

You can:

- see changed files
- inspect line-by-line differences
- stage files
- unstage files
- enter commit messages
- commit
- push
- pull
- synchronize

Approximate command mapping:

| VS Code | Git command |
|---|---|
| Stage Changes | `git add` |
| Unstage Changes | `git restore --staged` |
| Commit | `git commit` |
| Push | `git push` |
| Pull | `git pull` |
| Sync Changes | pull + push |
| Discard Changes | `git restore` |

The VS Code interface is useful, but learning the basic commands makes it much easier to understand what the UI is doing.

---

# 39. The main Branch

A Git repository can have multiple branches.

For your initial workflow, you can keep things simple and use only:

```text
main
```

Check your current branch:

```powershell
git branch
```

You may see:

```text
* main
```

The `*` indicates the current branch.

For a single-contributor project, working directly in `main` is acceptable when changes are small and frequent.

---

# 40. Optional: Use a Branch for Risky Experiments

Branches become useful when you want to let an AI agent make a larger experimental change without immediately affecting `main`.

Create a branch:

```powershell
git switch -c experiment/new-agent-architecture
```

Now changes and commits go into that branch.

Work normally:

```powershell
git add .
git commit -m "Test new agent architecture"
```

Push the branch:

```powershell
git push -u origin experiment/new-agent-architecture
```

If you accept the result, return to `main`:

```powershell
git switch main
```

Update it:

```powershell
git pull --rebase
```

Merge the experiment:

```powershell
git merge experiment/new-agent-architecture
```

Push:

```powershell
git push
```

Delete the local experimental branch if no longer needed:

```powershell
git branch -d experiment/new-agent-architecture
```

You do not need branches for every small change.

For a single-contributor AI project, they are most useful for:

- large refactoring
- new architecture
- major dependency changes
- experimental implementations
- uncertain AI-generated changes

---

# 41. A Simple Branching Strategy for One Contributor

Do not overcomplicate the process.

Use:

```text
main
```

for normal development.

Create a temporary branch only when the change feels risky.

For example:

```text
main
 │
 ├── experiment/new-agent-architecture
 │
 └── experiment/new-ranking-model
```

Once an experiment works, merge it into `main`.

---

# 42. Commands That Are Safe to Use Frequently

These commands mainly inspect the repository:

```powershell
git status
git diff
git diff --staged
git log --oneline
git branch
git remote -v
```

They are good diagnostic commands because they do not normally modify your files.

When uncertain, start with:

```powershell
git status
```

---

# 43. Commands to Treat Carefully

Be more cautious with commands that can remove or rewrite work.

Examples:

```powershell
git restore .
git reset
git reset --hard
git clean
git rebase
git push --force
```

This does not mean you should never use them.

It means you should understand their effect first.

In particular, avoid casually using:

```powershell
git reset --hard
```

or:

```powershell
git push --force
```

These can destroy work or rewrite repository history.

For your workflow, you rarely need them.

---

# 44. Recommended Routine Before a Large AI Change

Before asking Codex or Claude Code for a major modification:

```powershell
git status
```

If the working directory contains valuable changes, commit them first:

```powershell
git add .
git diff --staged
git commit -m "Checkpoint before architecture experiment"
git push
```

Then create a branch if the experiment is risky:

```powershell
git switch -c experiment/architecture-change
```

Now the agent can work without putting your main branch at unnecessary risk.

---

# 45. Recommended Routine After an AI Change

After Codex or Claude Code finishes:

```powershell
git status
git diff
```

Review:

- unexpected files
- deleted files
- configuration changes
- dependency files
- secret files
- generated files
- large rewrites

Then test the application.

If satisfied:

```powershell
git add .
git diff --staged
git commit -m "Add agent architecture changes"
git push
```

This workflow gives you a clear audit trail of AI-generated work.

---

# 46. Git as a Product Manager's Safety System

For a product manager working directly with AI coding agents, Git is useful for more than traditional software development.

It provides four important capabilities.

## 1. Checkpoints

Before an uncertain experiment:

```powershell
git commit
```

You have a known recovery point.

## 2. Change visibility

After an AI agent works:

```powershell
git diff
```

You can inspect the actual implementation changes.

## 3. Experiment isolation

For risky work:

```powershell
git switch -c experiment/...
```

You isolate the experiment from the stable version.

## 4. Historical context

Using:

```powershell
git log
```

you can understand how the product evolved.

Git effectively becomes part of the operating model for AI-assisted product development.

---

# 47. Practical Example

Imagine the project starts with:

```text
C:\Projects\Reddit_agent

README.md
main.py
requirements.txt
src\
```

You want Claude Code to implement Reddit data collection.

Before starting:

```powershell
cd C:\Projects\Reddit_agent
git pull --rebase
git status
```

Suppose the repository is clean.

Claude Code modifies:

```text
main.py
requirements.txt
src\reddit_client.py
```

After the agent finishes:

```powershell
git status
```

You see:

```text
modified: main.py
modified: requirements.txt
new file: src/reddit_client.py
```

Review:

```powershell
git diff
```

If the implementation looks correct, stage it:

```powershell
git add .
```

Review the future commit:

```powershell
git diff --staged
```

Commit:

```powershell
git commit -m "Add Reddit post collection"
```

Push:

```powershell
git push
```

GitLab now contains that version.

---

# 48. Starting the Next Feature

The next day:

```powershell
cd C:\Projects\Reddit_agent
git pull --rebase
git status
```

Then ask Codex to implement market classification.

Afterward:

```powershell
git status
git diff
```

Test the result.

Then:

```powershell
git add .
git diff --staged
git commit -m "Add market classification"
git push
```

Your Git history now looks approximately like:

```text
Initial project setup
↓
Add Reddit post collection
↓
Add market classification
```

---

# 49. A Good Commit Rhythm

Do not commit after every individual line.

Do not wait until an entire project is complete either.

A useful unit is:

> one logically complete change that you would be comfortable describing with one sentence.

Examples:

```text
Add Reddit authentication
Add subreddit configuration
Add post collection
Add deduplication
Add AI market analysis
Add structured JSON output
Fix rate limit handling
```

---

# 50. What GitLab Is Doing in This Workflow

For your current use case, GitLab mainly acts as:

```text
Remote repository
+
Backup
+
History viewer
+
Access point from another machine
```

You do not initially need to use:

- merge requests
- reviewers
- complex permissions
- elaborate branching models
- GitLab CI/CD
- release pipelines

Those become relevant as the project or team grows.

For a one-person project, keep the process intentionally simple.

---

# 51. Minimal Daily Cheat Sheet

## Start work

```powershell
cd C:\Projects\Reddit_agent
git pull --rebase
git status
```

## Review AI or manual changes

```powershell
git status
git diff
```

## Prepare a commit

```powershell
git add .
git diff --staged
```

## Save the version

```powershell
git commit -m "Describe the completed change"
```

## Synchronize with GitLab

```powershell
git push
```

---

# 52. Recovery Cheat Sheet

## Undo uncommitted changes in one file

```powershell
git restore path\to\file
```

## Undo all tracked uncommitted changes

```powershell
git restore .
```

## Remove one file from staging

```powershell
git restore --staged path\to\file
```

## Remove everything from staging

```powershell
git restore --staged .
```

## Change the latest local commit message

```powershell
git commit --amend -m "Better commit message"
```

## Remove the latest local commit but keep the changes

```powershell
git reset --soft HEAD~1
```

## Abort an in-progress rebase

```powershell
git rebase --abort
```

---

# 53. Inspection Cheat Sheet

## Current repository state

```powershell
git status
```

## Unstaged changes

```powershell
git diff
```

## Staged changes

```powershell
git diff --staged
```

## Commit history

```powershell
git log --oneline
```

## Visual history

```powershell
git log --oneline --graph --decorate --all
```

## Current branches

```powershell
git branch
```

## Remote repositories

```powershell
git remote -v
```

---

# 54. Ten Rules to Remember

1. **Pull before starting work.**

```powershell
git pull --rebase
```

2. **Use `git status` frequently.**

3. **Review AI-generated changes with `git diff`.**

4. **Review the future commit with `git diff --staged`.**

5. **Never commit secrets or `.env` files.**

6. **Create small, meaningful commits.**

7. **Push important checkpoints to GitLab.**

8. **Use branches for risky AI experiments, not necessarily for every small feature.**

9. **Avoid destructive commands such as `git reset --hard` unless you understand exactly what they will do.**

10. **If something looks wrong, do not immediately try random Git commands. Start with:**

```powershell
git status
```

---

# 55. The Workflow to Memorize

For your setup, almost everything comes down to this:

```powershell
cd C:\Projects\Reddit_agent

git pull --rebase
git status

# Work using VS Code / Codex / Claude Code

git diff

git add .
git diff --staged

git commit -m "Describe the completed change"
git push
```

Conceptually:

```text
SYNC
  ↓
WORK
  ↓
REVIEW
  ↓
STAGE
  ↓
REVIEW AGAIN
  ↓
COMMIT
  ↓
PUSH
```

That is the core Git workflow you need for a single-contributor AI-assisted project.
