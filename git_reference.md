# Git Reference Manual
> Version control that actually makes sense — from first commit to team workflows.

---

## Alphabetic Index

| Command | Section |
|---------|---------|
| [`git add`](#git-add) | [Staging & Committing](#2-staging--committing) |
| [`git bisect`](#git-bisect) | [Advanced Operations](#9-advanced-operations) |
| [`git blame`](#git-blame) | [Inspection & History](#5-inspection--history) |
| [`git branch`](#git-branch) | [Branching & Merging](#3-branching--merging) |
| [`git cherry-pick`](#git-cherry-pick) | [Advanced Operations](#9-advanced-operations) |
| [`git clean`](#git-clean) | [Undoing Changes](#6-undoing-changes) |
| [`git clone`](#git-clone) | [Setup & Configuration](#1-setup--configuration) |
| [`git commit`](#git-commit) | [Staging & Committing](#2-staging--committing) |
| [`git config`](#git-config) | [Setup & Configuration](#1-setup--configuration) |
| [`git diff`](#git-diff) | [Staging & Committing](#2-staging--committing) |
| [`git fetch`](#git-fetch) | [Remote Repositories](#4-remote-repositories) |
| [`git init`](#git-init) | [Setup & Configuration](#1-setup--configuration) |
| [`git log`](#git-log) | [Inspection & History](#5-inspection--history) |
| [`git merge`](#git-merge) | [Branching & Merging](#3-branching--merging) |
| [`git pull`](#git-pull) | [Remote Repositories](#4-remote-repositories) |
| [`git push`](#git-push) | [Remote Repositories](#4-remote-repositories) |
| [`git rebase`](#git-rebase) | [Branching & Merging](#3-branching--merging) |
| [`git reflog`](#git-reflog) | [Advanced Operations](#9-advanced-operations) |
| [`git remote`](#git-remote) | [Remote Repositories](#4-remote-repositories) |
| [`git reset`](#git-reset) | [Undoing Changes](#6-undoing-changes) |
| [`git restore`](#git-restore) | [Undoing Changes](#6-undoing-changes) |
| [`git revert`](#git-revert) | [Undoing Changes](#6-undoing-changes) |
| [`git show`](#git-show) | [Inspection & History](#5-inspection--history) |
| [`git stash`](#git-stash) | [Stashing](#7-stashing) |
| [`git status`](#git-status) | [Staging & Committing](#2-staging--committing) |
| [`git switch`](#git-switch) | [Branching & Merging](#3-branching--merging) |
| [`git tag`](#git-tag) | [Tagging](#8-tagging) |
| [`git worktree`](#git-worktree) | [Advanced Operations](#9-advanced-operations) |

---

## How to Read This Manual

Each command entry follows this structure:
```
COMMAND — what it does in one line

Syntax:   git command [flags] [arguments]
Flags:    -x    what this flag does
Examples: concrete usage
Notes:    traps, warnings, tips
```

**Tier system:**
- 🔴 **Tier 1** — Use daily. Learn these first.
- 🟡 **Tier 2** — Use regularly. Learn after Tier 1.
- 🔵 **Tier 3** — Use occasionally. Reference when needed.

---

## Git Concepts

*Before using Git, understand the mental model. Everything else becomes intuitive once you grasp these ideas.*

### The Three Areas

```
Working Directory  →  Staging Area (Index)  →  Repository (.git)
    (your files)        (next commit)           (commit history)

    git add ──────→
                        git commit ──────────→
    ←──────────────────────────────────────── git checkout / restore
```

- **Working Directory** — the actual files on your disk. What you see and edit.
- **Staging Area (Index)** — a holding zone. You pick which changes go into the next commit. This lets you commit part of your work, not everything at once.
- **Repository** — the full history of your project, stored in the `.git` folder.

### HEAD

`HEAD` is a pointer to the commit you're currently "on". Usually it points to a branch name, which points to a commit. When you make a new commit, `HEAD` (via the branch) moves forward.

```
HEAD → main → commit abc123
```

### Branches Are Just Pointers

A branch is not a copy of your code. It's just a 41-byte file containing a commit hash. Creating a branch is instant and free. That's why Git encourages branching.

```
main    → commit C
feature → commit E → commit D → commit C
```

### Remote Tracking Branches

When you clone a repo, Git creates **remote tracking branches** like `origin/main`. These are local snapshots of what the remote looked like last time you fetched. They update when you `git fetch` or `git pull`.

```
main              ← your local branch
origin/main       ← where the remote was last time you checked
```

---

---

# 1. Setup & Configuration

*One-time setup. Configure your identity, create repos, and clone existing ones.*

---

### `git config`
🔴 **Tier 1** — Set configuration values for Git (name, email, editor, aliases).

```
Syntax:   git config [--global | --local | --system] key value
```

| Flag | Meaning |
|------|---------|
| `--global` | Set for all repos for your user (`~/.gitconfig`) |
| `--local` | Set for current repo only (`.git/config`) — default |
| `--system` | Set for all users on this machine (rarely used) |
| `--list` | Show all current settings |
| `--unset` | Remove a setting |

**Examples:**
```bash
# First-time setup (required — Git won't let you commit without these)
git config --global user.name "Alex Thompson"
git config --global user.email "alex@example.com"

# Set default editor (for commit messages, interactive rebase)
git config --global core.editor "vim"              # or nano, code --wait

# Set default branch name for new repos
git config --global init.defaultBranch main

# Useful aliases
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.lg "log --oneline --graph --all"

# View all settings
git config --list

# View a specific setting
git config user.name
# Output: Alex Thompson
```

**Notes:**
- `--global` settings go in `~/.gitconfig`. `--local` settings go in `.git/config` inside the repo.
- Local settings override global settings. Useful for work vs personal email.

---

### `git init`
🔴 **Tier 1** — Create a new Git repository in the current directory.

```
Syntax:   git init [directory]
```

**Examples:**
```bash
# Initialize in current directory
cd my-project
git init
# Output: Initialized empty Git repository in /home/alex/my-project/.git/

# Initialize in a new directory (creates it)
git init new-project
cd new-project

# After init, make your first commit
git add .
git commit -m "Initial commit"
```

**Notes:**
- Creates a hidden `.git` folder — that's where all version history lives.
- Deleting `.git` destroys all history. The working files stay, but Git forgets everything.

---

### `git clone`
🔴 **Tier 1** — Download a remote repository (full history included).

```
Syntax:   git clone <url> [directory]
```

| Flag | Meaning |
|------|---------|
| `--depth 1` | Shallow clone — only latest commit (faster, less disk) |
| `--branch <name>` | Clone a specific branch |
| `--recurse-submodules` | Also clone submodules |

**Examples:**
```bash
# Clone via HTTPS
git clone https://github.com/user/repo.git

# Clone via SSH (requires SSH key setup)
git clone git@github.com:user/repo.git

# Clone into a specific folder name
git clone https://github.com/user/repo.git my-folder

# Shallow clone (good for large repos you just want to build)
git clone --depth 1 https://github.com/user/repo.git

# Clone a specific branch
git clone --branch develop https://github.com/user/repo.git
```

**Notes:**
- Cloning automatically sets up `origin` as the remote pointing to the source URL.
- SSH clones require your SSH key to be added to GitHub/GitLab. HTTPS clones ask for credentials.

---

---

# 2. Staging & Committing

*The core workflow: make changes, stage them, commit them. This is what you'll do hundreds of times.*

---

### `git status`
🔴 **Tier 1** — Show the state of your working directory and staging area.

```
Syntax:   git status [flags]
```

| Flag | Meaning |
|------|---------|
| `-s` | Short format (compact output) |
| `-b` | Show branch info (included in default output) |

**Examples:**
```bash
git status
# On branch main
# Changes to be committed:
#   (use "git restore --staged <file>..." to unstage)
#         modified:   app.py
#
# Changes not staged for commit:
#         modified:   config.yaml
#
# Untracked files:
#         notes.txt

git status -s
# M  app.py          ← staged (green M in left column)
#  M config.yaml     ← unstaged (red M in right column)
# ?? notes.txt       ← untracked
```

**Reading short format:**
```
XY filename
│└─ working directory status
└── staging area status

M  = staged modification
 M = unstaged modification
A  = staged new file
?? = untracked
D  = deleted
MM = staged AND has further unstaged changes
```

---

### `git add`
🔴 **Tier 1** — Stage changes for the next commit.

```
Syntax:   git add [flags] <pathspec>
```

| Flag | Meaning |
|------|---------|
| `-A` / `--all` | Stage everything (new, modified, deleted) |
| `-p` / `--patch` | Interactively choose hunks to stage |
| `-u` | Stage modified and deleted files only (not new untracked files) |

**Examples:**
```bash
# Stage a specific file
git add app.py

# Stage multiple files
git add app.py config.yaml README.md

# Stage everything in a directory
git add src/

# Stage all changes in the entire repo
git add -A

# Stage parts of a file interactively (choose which hunks)
git add -p app.py
# Git shows each change and asks: Stage this hunk? [y,n,s,q,?]
# y = yes, n = no, s = split into smaller hunks, q = quit
```

**Notes:**
- `git add .` stages everything in the current directory and below. `git add -A` stages everything in the entire repo regardless of where you are.
- Use `git add -p` when you've made multiple unrelated changes in one file and want to commit them separately. This is a power move.

---

### `git commit`
🔴 **Tier 1** — Record staged changes as a new commit in history.

```
Syntax:   git commit [flags]
```

| Flag | Meaning |
|------|---------|
| `-m "msg"` | Commit message inline (skip editor) |
| `-a` | Auto-stage all **tracked** modified files, then commit |
| `--amend` | Replace the last commit (rewrite history) |
| `--allow-empty` | Create a commit with no changes |
| `-v` | Show diff in the commit message editor |

**Examples:**
```bash
# Basic commit with message
git commit -m "Add user authentication endpoint"

# Multi-line commit message
git commit -m "Add user authentication endpoint

- Implement JWT token generation
- Add password hashing with bcrypt
- Create login and register routes"

# Stage all tracked changes and commit in one step
git commit -am "Fix typo in header component"

# Oops, forgot a file — add it to the last commit
git add forgotten-file.py
git commit --amend
# Opens editor with previous message — edit or save as-is

# Amend just the message (no file changes)
git commit --amend -m "Better commit message"
```

**Notes:**
- **Write good commit messages.** Format: short summary (50 chars), blank line, longer explanation if needed.
- `--amend` rewrites history. Never amend commits that have been pushed to a shared branch.
- `-a` only stages **tracked** files. New untracked files still need `git add` first.

---

### `git diff`
🔴 **Tier 1** — Show differences between commits, staging area, and working directory.

```
Syntax:   git diff [flags] [commit] [-- path]
```

| Flag | Meaning |
|------|---------|
| `--staged` / `--cached` | Show diff of staged changes (vs last commit) |
| `--stat` | Summary only: files changed, insertions, deletions |
| `--name-only` | Show only filenames that changed |
| `--word-diff` | Show changes word-by-word instead of line-by-line |

**Examples:**
```bash
# Show unstaged changes (working dir vs staging area)
git diff

# Show staged changes (what will be committed)
git diff --staged

# Diff a specific file
git diff app.py

# Diff between two branches
git diff main..feature-login

# Diff between two commits
git diff abc1234 def5678

# Just show which files changed
git diff --stat main..feature-login
#  src/auth.py   | 45 +++++++++++++++++++++
#  src/routes.py | 12 +++---
#  2 files changed, 51 insertions(+), 6 deletions(-)

# Diff of what changed between your branch and main (since they diverged)
git diff main...feature-login    # three dots = since common ancestor
```

**Notes:**
- Two dots (`..`) = difference between endpoints. Three dots (`...`) = difference since branches diverged.
- `git diff` with no arguments shows **unstaged** changes only. Use `--staged` to see what's about to be committed.

---

---

# 3. Branching & Merging

*Branches let you work on features in isolation. Merging and rebasing bring the work back together. This is where Git shines.*

---

### `git branch`
🔴 **Tier 1** — List, create, or delete branches.

```
Syntax:   git branch [flags] [branch-name] [start-point]
```

| Flag | Meaning |
|------|---------|
| `-a` | List all branches (local + remote) |
| `-r` | List remote branches only |
| `-d` | Delete a branch (safe — refuses if unmerged) |
| `-D` | Force delete a branch (even if unmerged) |
| `-m` | Rename current branch |
| `-v` | Show last commit on each branch |
| `--merged` | List branches already merged into current branch |
| `--no-merged` | List branches not yet merged |

**Examples:**
```bash
# List local branches (* = current)
git branch
# * main
#   feature-login
#   bugfix-header

# List all branches including remote
git branch -a
# * main
#   feature-login
#   remotes/origin/main
#   remotes/origin/develop

# Create a new branch (doesn't switch to it)
git branch feature-search

# Create a branch from a specific commit
git branch hotfix-123 abc1234

# Delete a merged branch
git branch -d feature-login
# Deleted branch feature-login (was 3a2b1c0).

# Force delete an unmerged branch
git branch -D experiment-gone-wrong

# Rename current branch
git branch -m new-name

# See which branches are merged into main
git branch --merged main

# See which branches still need to be merged
git branch --no-merged main
```

---

### `git switch`
🔴 **Tier 1** — Switch to a different branch (modern replacement for `git checkout` for branch switching).

```
Syntax:   git switch [flags] <branch>
```

| Flag | Meaning |
|------|---------|
| `-c` | Create a new branch and switch to it |
| `-C` | Create (or reset if exists) and switch |
| `--detach` | Switch to a commit without being on any branch |

**Examples:**
```bash
# Switch to an existing branch
git switch feature-login

# Create a new branch and switch to it (most common)
git switch -c feature-search

# Create a branch from a specific starting point
git switch -c hotfix-123 main

# Go back to the previous branch (like cd -)
git switch -
```

**Notes:**
- `git switch` was introduced in Git 2.23 as a clearer alternative to `git checkout` for switching branches.
- If you have uncommitted changes that conflict with the target branch, Git will refuse to switch. Either commit, stash, or discard them first.

---

### `git checkout`
🟡 **Tier 2** — The Swiss Army knife: switch branches, restore files, detach HEAD. Older but still widely used.

```
Syntax:   git checkout [flags] <branch|commit|file>
```

| Flag | Meaning |
|------|---------|
| `-b` | Create and switch to a new branch (like `switch -c`) |
| `-B` | Create or reset and switch |
| `--` | Explicitly separate file paths from branch names |

**Examples:**
```bash
# Switch branch (same as git switch)
git checkout main

# Create and switch (same as git switch -c)
git checkout -b feature-search

# Restore a file to its last committed state (DISCARDS CHANGES)
git checkout -- app.py

# Check out a specific file from another branch
git checkout main -- src/config.py

# Detach HEAD at a specific commit (for inspecting old code)
git checkout abc1234
```

**Notes:**
- `git checkout` does two very different things: switch branches AND restore files. That's why `git switch` and `git restore` were created — to separate these concerns.
- `git checkout -- file` permanently discards uncommitted changes to that file. No undo.

---

### `git merge`
🔴 **Tier 1** — Combine another branch's changes into the current branch.

```
Syntax:   git merge [flags] <branch>
```

| Flag | Meaning |
|------|---------|
| `--no-ff` | Force a merge commit even if fast-forward is possible |
| `--ff-only` | Only merge if fast-forward is possible (no merge commit) |
| `--squash` | Combine all changes into one commit (doesn't auto-commit) |
| `--abort` | Abort a merge in progress (during conflicts) |

**Examples:**
```bash
# Merge feature branch into main
git switch main
git merge feature-login
# If no conflicts: automatic merge commit (or fast-forward)
# If conflicts: Git stops and asks you to resolve them

# Force a merge commit (good for keeping history of feature branches)
git merge --no-ff feature-login

# Squash merge (collapse all feature commits into one)
git merge --squash feature-login
git commit -m "Add login feature"

# Abort a conflicted merge (go back to before you ran merge)
git merge --abort
```

**Fast-forward vs merge commit:**
```
# Fast-forward (main had no new commits):
# Before:  main ─── A ─── B
#                           └── feature ─── C ─── D
# After:   main ─── A ─── B ─── C ─── D

# Merge commit (main had new commits):
# Before:  main ─── A ─── B ─── E
#                           └── feature ─── C ─── D
# After:   main ─── A ─── B ─── E ─── M (merge commit)
#                           └── C ─── D ──┘
```

**Resolving merge conflicts:**
```bash
git merge feature-login
# CONFLICT (content): Merge conflict in src/auth.py
# Automatic merge failed; fix conflicts and then commit the result.

# 1. Open the conflicted file — look for conflict markers:
# <<<<<<< HEAD
# your code on main
# =======
# their code on feature-login
# >>>>>>> feature-login

# 2. Edit the file: keep what you want, delete the markers

# 3. Stage the resolved file
git add src/auth.py

# 4. Complete the merge
git commit
# (Git pre-fills the merge commit message)
```

---

### `git rebase`
🟡 **Tier 2** — Replay your branch's commits on top of another branch. Makes history linear.

```
Syntax:   git rebase [flags] <upstream> [branch]
```

| Flag | Meaning |
|------|---------|
| `-i` / `--interactive` | Interactive rebase — reorder, squash, edit commits |
| `--onto <new-base>` | Rebase onto a different base |
| `--abort` | Abort rebase in progress |
| `--continue` | Continue after resolving conflicts |
| `--skip` | Skip current conflicting commit |

**Examples:**
```bash
# Rebase your feature branch onto latest main
git switch feature-search
git rebase main
# Your commits are replayed on top of main's latest commit

# Interactive rebase — clean up last 4 commits
git rebase -i HEAD~4
# Opens editor with:
# pick abc1234 Add search bar UI
# pick def5678 Fix typo
# pick 789abcd Add search logic
# pick 012efgh Fix another typo
#
# Change to:
# pick abc1234 Add search bar UI
# squash def5678 Fix typo          ← squash into previous
# pick 789abcd Add search logic
# squash 012efgh Fix another typo  ← squash into previous
# Save and close — now you have 2 clean commits instead of 4

# If conflicts arise during rebase
# 1. Fix the conflict in the file
# 2. git add <file>
# 3. git rebase --continue

# Abort if things go wrong
git rebase --abort
```

**Rebase vs Merge:**
```
# Merge preserves history as it happened:
# main ─── A ─── B ─── E ─── M
#                  └── C ─── D ──┘

# Rebase rewrites history to be linear:
# main ─── A ─── B ─── E ─── C' ─── D'
```

**Notes:**
- **Golden rule: never rebase commits that have been pushed to a shared branch.** Rebase rewrites commit hashes. If others have based work on those commits, you'll create a mess.
- Rebase your own feature branches freely before merging. Interactive rebase is great for cleaning up messy work-in-progress commits.

---

---

# 4. Remote Repositories

*Syncing with GitHub, GitLab, or any remote server. Push your work up, pull others' work down.*

---

### `git remote`
🟡 **Tier 2** — Manage remote repository connections.

```
Syntax:   git remote [command] [name] [url]
```

**Examples:**
```bash
# List remotes
git remote
# origin

# List remotes with URLs
git remote -v
# origin  https://github.com/user/repo.git (fetch)
# origin  https://github.com/user/repo.git (push)

# Add a remote
git remote add upstream https://github.com/original/repo.git

# Change a remote's URL (e.g., switch from HTTPS to SSH)
git remote set-url origin git@github.com:user/repo.git

# Remove a remote
git remote remove upstream

# Rename a remote
git remote rename origin github
```

**Notes:**
- `origin` is the conventional name for your primary remote (set automatically by `git clone`).
- `upstream` is conventionally used for the original repo when you've forked.

---

### `git fetch`
🟡 **Tier 2** — Download changes from a remote but don't merge them. Safe to run anytime.

```
Syntax:   git fetch [remote] [branch]
```

| Flag | Meaning |
|------|---------|
| `--all` | Fetch from all remotes |
| `--prune` | Remove remote tracking branches that no longer exist on the remote |

**Examples:**
```bash
# Fetch from origin (default remote)
git fetch

# Fetch from a specific remote
git fetch upstream

# Fetch and clean up deleted remote branches
git fetch --prune

# Fetch a specific branch
git fetch origin feature-login

# After fetching, see what changed
git log main..origin/main    # commits on remote that you don't have
git diff main origin/main    # actual changes
```

**Notes:**
- `git fetch` is always safe. It only updates your remote tracking branches (`origin/main` etc.), never your local branches.
- Think of it as: "download the latest information from the remote, but let me decide what to do with it."

---

### `git pull`
🔴 **Tier 1** — Fetch from remote AND merge into current branch. Shortcut for `git fetch` + `git merge`.

```
Syntax:   git pull [flags] [remote] [branch]
```

| Flag | Meaning |
|------|---------|
| `--rebase` | Rebase instead of merge (linear history) |
| `--ff-only` | Only pull if it's a fast-forward (safest) |
| `--no-rebase` | Force merge even if pull.rebase is set |

**Examples:**
```bash
# Pull latest changes on current branch
git pull

# Pull with rebase (avoids unnecessary merge commits)
git pull --rebase

# Pull from a specific remote/branch
git pull origin main

# Set rebase as default pull behavior (recommended)
git config --global pull.rebase true

# If pull --rebase hits conflicts
# 1. Fix conflicts
# 2. git add <files>
# 3. git rebase --continue
```

**Notes:**
- `git pull --rebase` is generally preferred over plain `git pull` to keep history clean.
- If you have uncommitted changes, `git pull` will refuse. Commit or stash first.

---

### `git push`
🔴 **Tier 1** — Upload local commits to a remote repository.

```
Syntax:   git push [flags] [remote] [branch]
```

| Flag | Meaning |
|------|---------|
| `-u` / `--set-upstream` | Set tracking relationship (do this on first push) |
| `--force` / `-f` | Force push (overwrites remote history) |
| `--force-with-lease` | Safer force push (fails if remote has new commits) |
| `--tags` | Push all tags |
| `--delete` | Delete a remote branch |

**Examples:**
```bash
# Push current branch (after tracking is set up)
git push

# First push of a new branch (set upstream tracking)
git push -u origin feature-login
# After this, just 'git push' works

# Push to a specific remote/branch
git push origin main

# Force push (after rebase) — use --force-with-lease for safety
git push --force-with-lease origin feature-login

# Delete a remote branch
git push origin --delete feature-old

# Push all tags
git push --tags
```

**Notes:**
- **Never force push to `main` or `develop`** — you'll overwrite others' work.
- `--force-with-lease` is safer than `--force`: it refuses to push if someone else has pushed commits you haven't fetched yet.
- Use `--force-with-lease` after rebasing your own feature branches.

---

---

# 5. Inspection & History

*Understanding what happened: who changed what, when, and why.*

---

### `git log`
🔴 **Tier 1** — Show commit history.

```
Syntax:   git log [flags] [revision range] [-- path]
```

| Flag | Meaning |
|------|---------|
| `--oneline` | One line per commit (short hash + message) |
| `--graph` | ASCII art branch visualization |
| `--all` | Show all branches, not just current |
| `-n <num>` | Show only last N commits |
| `--author="name"` | Filter by author |
| `--since="date"` | Commits after date |
| `--until="date"` | Commits before date |
| `--grep="text"` | Search commit messages |
| `-p` | Show the actual diff for each commit |
| `--stat` | Show files changed per commit |
| `--follow` | Follow file through renames |

**Examples:**
```bash
# Basic log
git log

# Compact view (most useful day-to-day)
git log --oneline
# a1b2c3d Add search feature
# e4f5g6h Fix login bug
# i7j8k9l Initial commit

# Graph view of all branches (the "big picture")
git log --oneline --graph --all
# * a1b2c3d (HEAD -> feature) Add search
# | * e4f5g6h (main) Fix header
# |/
# * i7j8k9l Initial commit

# Last 5 commits with stats
git log -5 --stat

# Commits by a specific author
git log --author="Alex"

# Commits in the last week
git log --since="1 week ago"

# Search commit messages
git log --grep="fix" --oneline

# History of a specific file (follows renames)
git log --follow -p -- src/auth.py

# Commits on feature-login not yet on main
git log main..feature-login --oneline

# Pretty custom format
git log --pretty=format:"%h %an %ar %s" -10
# a1b2c3d Alex 2 hours ago Add search feature
```

---

### `git show`
🟡 **Tier 2** — Show details of a specific commit, tag, or other object.

```
Syntax:   git show [object]
```

**Examples:**
```bash
# Show the last commit (diff + metadata)
git show

# Show a specific commit
git show abc1234

# Show a specific file at a specific commit
git show abc1234:src/app.py

# Show what a tag points to
git show v1.2.0

# Show just the commit message (no diff)
git show --stat abc1234
```

---

### `git blame`
🟡 **Tier 2** — Show who last modified each line of a file (and when).

```
Syntax:   git blame [flags] <file>
```

| Flag | Meaning |
|------|---------|
| `-L <start>,<end>` | Only show specific line range |
| `-w` | Ignore whitespace changes |
| `-C` | Detect lines moved from other files |

**Examples:**
```bash
# Blame an entire file
git blame src/auth.py
# a1b2c3d4 (Alex  2024-01-15 10:30:22 +0000  1) def login(user, password):
# e5f6g7h8 (Sarah 2024-01-16 14:22:10 +0000  2)     if not user:
# e5f6g7h8 (Sarah 2024-01-16 14:22:10 +0000  3)         raise ValueError("...")

# Blame specific lines
git blame -L 10,20 src/auth.py

# Ignore whitespace changes (find real author, not reformatter)
git blame -w src/auth.py
```

**Notes:**
- Blame tells you the commit, author, and date for each line. Then use `git show <commit>` to see the full context of that change.

---

---

# 6. Undoing Changes

*Mistakes happen. Git has multiple ways to undo things, depending on what stage the mistake is at.*

---

### `git restore`
🔴 **Tier 1** — Discard changes in working directory or unstage files. Modern replacement for `git checkout --` and `git reset HEAD`.

```
Syntax:   git restore [flags] <file>
```

| Flag | Meaning |
|------|---------|
| `--staged` | Unstage a file (keep changes in working dir) |
| `--source=<commit>` | Restore from a specific commit |
| `--worktree` | Restore working directory (default) |

**Examples:**
```bash
# Discard uncommitted changes to a file (DESTRUCTIVE — no undo)
git restore app.py

# Discard all uncommitted changes
git restore .

# Unstage a file (opposite of git add)
git restore --staged app.py

# Unstage everything
git restore --staged .

# Restore a file to how it was 3 commits ago
git restore --source=HEAD~3 app.py

# Restore a file from another branch
git restore --source=main -- config.yaml
```

**Notes:**
- `git restore` (without `--staged`) **permanently discards** your uncommitted changes. There is no undo. Be sure.
- `git restore --staged` is safe — it just unstages, keeping your changes in the working directory.

---

### `git reset`
🟡 **Tier 2** — Move HEAD (and optionally the staging area and working directory) to a different commit. Rewrites history.

```
Syntax:   git reset [mode] [commit]
```

| Mode | HEAD | Staging | Working Dir | Use Case |
|------|------|---------|-------------|----------|
| `--soft` | Moves | Unchanged | Unchanged | Undo commit, keep everything staged |
| `--mixed` | Moves | Reset | Unchanged | Undo commit, keep changes unstaged (default) |
| `--hard` | Moves | Reset | Reset | Undo everything, lose all changes |

**Examples:**
```bash
# Undo last commit but keep changes staged (ready to recommit)
git reset --soft HEAD~1

# Undo last commit, keep changes unstaged
git reset HEAD~1            # --mixed is default

# Undo last 3 commits, keep changes unstaged
git reset HEAD~3

# NUCLEAR OPTION: undo last commit and DESTROY all changes
git reset --hard HEAD~1

# Reset to match the remote exactly (DESTROYS local changes)
git reset --hard origin/main

# Unstage a file (old way — prefer git restore --staged)
git reset HEAD app.py
```

**Notes:**
- `--soft` is great for "I committed too early, let me redo the commit."
- `--hard` is **destructive and irreversible** (unless you know the commit hash — see `git reflog`).
- Never reset commits that have been pushed to a shared branch.

---

### `git revert`
🟡 **Tier 2** — Create a new commit that undoes a previous commit. Safe for shared branches.

```
Syntax:   git revert [flags] <commit>
```

| Flag | Meaning |
|------|---------|
| `--no-commit` / `-n` | Stage the revert but don't commit yet |
| `--no-edit` | Use default revert message without opening editor |

**Examples:**
```bash
# Revert the last commit
git revert HEAD
# Creates a new commit that undoes the changes

# Revert a specific commit
git revert abc1234

# Revert without auto-committing (useful for reverting multiple)
git revert --no-commit abc1234
git revert --no-commit def5678
git commit -m "Revert broken auth changes"

# Revert a merge commit (must specify which parent to keep)
git revert -m 1 abc1234    # -m 1 = keep the main branch side
```

**Notes:**
- Unlike `git reset`, `git revert` doesn't rewrite history. It adds a new commit. **This is the safe way to undo on shared branches.**
- Reverting a merge commit is tricky — the `-m` flag specifies which parent to revert to.

---

### `git clean`
🔵 **Tier 3** — Remove untracked files from the working directory.

```
Syntax:   git clean [flags]
```

| Flag | Meaning |
|------|---------|
| `-n` / `--dry-run` | Preview what would be deleted (always do this first) |
| `-f` | Actually delete (required — Git won't clean without it) |
| `-d` | Also remove untracked directories |
| `-x` | Also remove files ignored by `.gitignore` |

**Examples:**
```bash
# Preview what would be deleted (ALWAYS do this first)
git clean -dn
# Would remove build/
# Would remove temp.log

# Delete untracked files and directories
git clean -df

# Delete everything including gitignored files (full reset)
git clean -dfx
```

**Notes:**
- `git clean` is **irreversible**. Always preview with `-n` first.
- `-x` removes gitignored files too (like `node_modules/`, `.env`, build artifacts). Use with extreme caution.

---

---

# 7. Stashing

*Temporarily save uncommitted changes so you can switch context. Like a clipboard for your working directory.*

---

### `git stash`
🔴 **Tier 1** — Save uncommitted changes and revert to a clean working directory.

```
Syntax:   git stash [command] [flags]
```

| Command | Meaning |
|---------|---------|
| `push` | Save changes (default when you just run `git stash`) |
| `pop` | Apply most recent stash and remove it from stash list |
| `apply` | Apply a stash but keep it in the stash list |
| `list` | Show all stashes |
| `drop` | Delete a specific stash |
| `clear` | Delete all stashes |
| `show` | Show the diff of a stash |

| Flag | Meaning |
|------|---------|
| `-m "message"` | Add a description to the stash |
| `-u` / `--include-untracked` | Also stash untracked files |
| `-k` / `--keep-index` | Stash only unstaged changes, keep staged as-is |

**Examples:**
```bash
# Stash current changes
git stash
# Saved working directory and index state WIP on main: a1b2c3d ...

# Stash with a descriptive message
git stash push -m "half-done login form"

# Stash including untracked files
git stash -u

# List all stashes
git stash list
# stash@{0}: On main: half-done login form
# stash@{1}: WIP on feature: e4f5g6h Add header

# Apply the most recent stash and remove it
git stash pop

# Apply a specific stash (keep it in the list)
git stash apply stash@{1}

# See what's in a stash
git stash show              # summary
git stash show -p           # full diff
git stash show stash@{1}    # specific stash

# Delete a specific stash
git stash drop stash@{1}

# Delete all stashes
git stash clear
```

**Notes:**
- Stash is a stack (LIFO). `git stash` pushes, `git stash pop` pops.
- Common workflow: you're mid-feature, need to fix a bug on main. Stash your work, switch to main, fix the bug, switch back, pop the stash.
- If `pop` causes conflicts, the stash is NOT dropped. Resolve conflicts, then `git stash drop` manually.

---

---

# 8. Tagging

*Mark specific commits as important (releases, milestones). Tags don't move — they permanently point to one commit.*

---

### `git tag`
🟡 **Tier 2** — Create, list, or delete tags.

```
Syntax:   git tag [flags] [tag-name] [commit]
```

| Flag | Meaning |
|------|---------|
| `-a` | Annotated tag (has message, author, date — preferred) |
| `-m "msg"` | Tag message (used with `-a`) |
| `-d` | Delete a tag |
| `-l "pattern"` | List tags matching a pattern |

**Examples:**
```bash
# Create a lightweight tag (just a label)
git tag v1.0.0

# Create an annotated tag (recommended — stores metadata)
git tag -a v1.0.0 -m "Release version 1.0.0"

# Tag a specific past commit
git tag -a v0.9.0 -m "Beta release" abc1234

# List all tags
git tag
# v0.9.0
# v1.0.0

# List tags matching a pattern
git tag -l "v1.*"

# Show tag details
git show v1.0.0

# Delete a local tag
git tag -d v1.0.0

# Push a tag to remote
git push origin v1.0.0

# Push all tags
git push --tags

# Delete a remote tag
git push origin --delete v1.0.0
```

**Notes:**
- Use annotated tags (`-a`) for releases. Use lightweight tags for temporary/personal markers.
- Tags are not pushed by default. You must explicitly `git push --tags` or `git push origin <tagname>`.
- Semantic versioning convention: `v1.0.0` (major.minor.patch).

---

---

# 9. Advanced Operations

*Power tools for specific situations. You won't use these daily, but they're lifesavers when you need them.*

---

### `git cherry-pick`
🟡 **Tier 2** — Apply a specific commit from another branch onto your current branch.

```
Syntax:   git cherry-pick [flags] <commit>
```

| Flag | Meaning |
|------|---------|
| `-n` / `--no-commit` | Apply changes but don't commit |
| `-x` | Add "(cherry picked from commit ...)" to message |
| `--abort` | Abort in-progress cherry-pick |

**Examples:**
```bash
# Cherry-pick a single commit
git cherry-pick abc1234

# Cherry-pick without committing (stage only)
git cherry-pick -n abc1234

# Cherry-pick a range of commits
git cherry-pick abc1234..def5678

# Cherry-pick multiple specific commits
git cherry-pick abc1234 def5678 ghi9012

# If conflicts arise
git cherry-pick --abort     # give up
# or resolve conflicts, then:
git add .
git cherry-pick --continue
```

**Notes:**
- Cherry-pick creates a **new commit** with a new hash (it's a copy, not a move).
- Useful for: applying a hotfix from main onto a release branch, or grabbing one commit from a branch you don't want to merge entirely.

---

### `git reflog`
🟡 **Tier 2** — Show the history of where HEAD has pointed. Your safety net for recovering "lost" commits.

```
Syntax:   git reflog [show] [flags]
```

**Examples:**
```bash
# Show reflog (every time HEAD moved)
git reflog
# a1b2c3d HEAD@{0}: commit: Add search feature
# e4f5g6h HEAD@{1}: checkout: moving from main to feature
# i7j8k9l HEAD@{2}: reset: moving to HEAD~2
# m3n4o5p HEAD@{3}: commit: This commit I accidentally reset away

# Recover a "lost" commit after git reset --hard
git reflog                    # find the commit hash
git reset --hard HEAD@{3}    # go back to that point

# Recover a deleted branch
git reflog                         # find the commit hash where branch was
git branch recovered-branch HEAD@{5}
```

**Notes:**
- Reflog is **local only** and entries expire after 90 days (30 days for unreachable commits).
- This is your "undo" button for almost everything — even `git reset --hard` can be undone if you act quickly.
- When someone says "I lost my commits!" the answer is almost always `git reflog`.

---

### `git bisect`
🔵 **Tier 3** — Binary search through commit history to find which commit introduced a bug.

```
Syntax:   git bisect <subcommand>
```

**Examples:**
```bash
# Start bisecting
git bisect start

# Mark current commit as bad (has the bug)
git bisect bad

# Mark a known good commit (didn't have the bug)
git bisect good v1.0.0

# Git checks out a commit halfway between. Test it, then:
git bisect good    # if this commit doesn't have the bug
git bisect bad     # if this commit has the bug

# Git keeps narrowing down... eventually:
# abc1234 is the first bad commit

# Done — go back to your branch
git bisect reset

# Automated bisect with a test script
git bisect start HEAD v1.0.0
git bisect run ./test.sh    # script exits 0 for good, 1 for bad
```

**Notes:**
- Bisect is incredibly efficient: 1000 commits = ~10 steps to find the culprit.
- Automated bisect (`run`) is the real power move — write a script that tests for the bug.

---

### `git worktree`
🔵 **Tier 3** — Work on multiple branches simultaneously in separate directories.

```
Syntax:   git worktree <command> [flags]
```

**Examples:**
```bash
# Add a worktree for a branch
git worktree add ../hotfix-dir hotfix-branch
# Now you have the hotfix branch checked out in ../hotfix-dir

# Add a worktree with a new branch
git worktree add -b emergency-fix ../fix-dir main

# List worktrees
git worktree list
# /home/alex/project        abc1234 [main]
# /home/alex/hotfix-dir     def5678 [hotfix-branch]

# Remove a worktree (after you're done)
git worktree remove ../hotfix-dir
```

**Notes:**
- Worktrees let you avoid stashing. Instead of stashing your feature work to fix a bug, just create a new worktree.
- Each worktree must be on a different branch. They share the same `.git` repository.

---

---

# 10. Common Workflows

*Real-world recipes for everyday situations. Copy-paste these patterns.*

---

### Feature Branch Workflow

The most common Git workflow for teams:

```bash
# 1. Start from an up-to-date main
git switch main
git pull

# 2. Create a feature branch
git switch -c feature-user-profile

# 3. Work, commit, repeat
git add src/profile.py tests/test_profile.py
git commit -m "Add user profile model and tests"
# ... more work ...
git add src/profile.py src/routes.py
git commit -m "Add profile API endpoints"

# 4. Stay up to date with main (rebase your branch)
git fetch origin
git rebase origin/main
# Fix any conflicts, then: git add . && git rebase --continue

# 5. Push your branch
git push -u origin feature-user-profile

# 6. Create a Pull Request (on GitHub/GitLab)

# 7. After PR is merged, clean up
git switch main
git pull
git branch -d feature-user-profile
```

---

### Fixing the Last Commit

```bash
# Forgot to add a file
git add forgotten-file.py
git commit --amend --no-edit    # adds file to last commit, same message

# Typo in commit message
git commit --amend -m "Corrected commit message"

# NOTE: only amend if you haven't pushed yet!
```

---

### Undoing Things at Every Stage

```bash
# Undo changes in working directory (before git add)
git restore app.py                  # single file
git restore .                       # all files

# Undo staging (after git add, before commit)
git restore --staged app.py         # unstage, keep changes

# Undo a commit (hasn't been pushed)
git reset --soft HEAD~1             # keep changes staged
git reset HEAD~1                    # keep changes unstaged
git reset --hard HEAD~1             # destroy changes (careful!)

# Undo a commit (already pushed to shared branch)
git revert abc1234                  # safe: creates a new "undo" commit
```

---

### Interactive Rebase for Clean History

```bash
# Clean up the last 5 commits before merging
git rebase -i HEAD~5

# In the editor, you can:
# pick   — keep the commit as-is
# reword — keep commit, edit message
# squash — meld into previous commit
# fixup  — like squash but discard this commit's message
# drop   — delete the commit entirely
# edit   — pause rebase at this commit to amend it

# Example: squash fix-up commits
# pick a1b2c3d Add user auth
# squash e4f5g6h Fix typo in auth      ← fold into above
# squash i7j8k9l Fix another typo      ← fold into above
# pick m3n4o5p Add user profile
# Result: 2 clean commits instead of 4
```

---

### Resolving Merge Conflicts Step by Step

```bash
# 1. The merge/rebase tells you there are conflicts
git merge feature-login
# Auto-merging src/auth.py
# CONFLICT (content): Merge conflict in src/auth.py

# 2. See which files have conflicts
git status
# both modified: src/auth.py

# 3. Open the file — conflict markers look like this:
# <<<<<<< HEAD
# def login(user):
#     return authenticate(user)
# =======
# def login(user, remember=False):
#     return authenticate(user, remember)
# >>>>>>> feature-login

# 4. Edit the file: choose one side, combine both, or rewrite
# def login(user, remember=False):
#     return authenticate(user, remember)

# 5. Remove ALL conflict markers (<<<, ===, >>>)

# 6. Stage the resolved file
git add src/auth.py

# 7. Continue
git commit              # if merging
git rebase --continue   # if rebasing
```

---

### Syncing a Fork with Upstream

```bash
# One-time setup: add upstream remote
git remote add upstream https://github.com/original/repo.git

# Sync
git fetch upstream
git switch main
git merge upstream/main    # or: git rebase upstream/main
git push origin main       # update your fork on GitHub
```

---

### Quick Reference: "I Need To..."

| I need to... | Command |
|--------------|---------|
| See what I've changed | `git status` / `git diff` |
| Save my work | `git add -A && git commit -m "msg"` |
| Create a branch | `git switch -c branch-name` |
| Switch branches | `git switch branch-name` |
| Get latest from remote | `git pull --rebase` |
| Push my branch | `git push -u origin branch-name` |
| Undo uncommitted file changes | `git restore file` |
| Unstage a file | `git restore --staged file` |
| Undo last commit (keep changes) | `git reset --soft HEAD~1` |
| Undo a pushed commit safely | `git revert HEAD` |
| Temporarily save work | `git stash` / `git stash pop` |
| See who changed a line | `git blame file` |
| Find which commit broke something | `git bisect start` |
| Recover something I "lost" | `git reflog` |
