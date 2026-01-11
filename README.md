# Complete Git Commands Reference Guide

## Table of Contents
1. [Basic Commands Explained](#basic-commands)
2. [Advanced Commands](#advanced-commands)
3. [Practical Scenarios](#practical-scenarios)
4. [Daily Workflow](#daily-workflow)
5. [Quiz Questions](#quiz)
6. [Troubleshooting Guide](#troubleshooting)

---

## Basic Commands Explained

### Repository Initialization & Status

#### `git init`
**Purpose:** Initialize a new Git repository in the current directory

**Example:**
```bash
mkdir my-project
cd my-project
git init
# Creates .git directory
# Output: Initialized empty Git repository in /path/to/my-project/.git/
```

**What it does:** Creates a hidden `.git` folder that stores all version control information.

---

#### `git status`
**Purpose:** Display the state of the working directory and staging area

**Example:**
```bash
git status
# Output shows:
# - Current branch name
# - Staged files (green - ready to commit)
# - Modified files (red - tracked but not staged)
# - Untracked files (red - new files not tracked)
```

**Detailed Output:**
```
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   login.js

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes)
        modified:   index.html

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        config.json
```

---

#### `git status -s` (Short Status)
**Purpose:** Show condensed status output

**Example:**
```bash
git status -s
# Output:
# ?? newfile.txt      (untracked)
# M  modified.txt     (modified and staged)
#  M unstaged.txt     (modified but not staged)
# A  added.txt        (new file staged)
# D  deleted.txt      (deleted and staged)
# MM both-changed.txt (staged and unstaged changes)
```

**Status Codes:**
- `??` = Untracked file
- `A ` = New file added to staging
- `M ` = Modified and staged
- ` M` = Modified but not staged
- `MM` = Modified, staged, then modified again
- `D ` = Deleted and staged
- `R ` = Renamed

---

### Staging & Committing

#### `git add`
**Purpose:** Add file contents to the staging area (preparing for commit)

**Examples:**
```bash
# Add single file
git add filename.txt

# Add all files in current directory
git add .

# Add all JavaScript files
git add *.js

# Add all files in src directory
git add src/

# Add all changes (new, modified, deleted)
git add -A

# Interactive staging (choose hunks to stage)
git add -p

# Add all tracked files (ignores untracked)
git add -u
```

**Interactive Staging Example:**
```bash
git add -p index.html
# Git shows each change and asks:
# Stage this hunk [y,n,q,a,d,s,e,?]?
# y - stage this hunk
# n - don't stage
# q - quit
# a - stage this and all remaining
# d - don't stage this or remaining
# s - split into smaller hunks
# e - manually edit hunk
```

---

#### `git reset`
**Purpose:** Unstage files or undo commits

**Examples:**
```bash
# Unstage specific file (keep changes in working directory)
git reset filename.txt

# Unstage all files
git reset

# Undo last commit, keep changes staged
git reset --soft HEAD~1

# Undo last commit, unstage changes (default behavior)
git reset --mixed HEAD~1
# or simply:
git reset HEAD~1

# Undo last commit and delete changes permanently ⚠️
git reset --hard HEAD~1

# Undo multiple commits
git reset --hard HEAD~3  # Delete last 3 commits

# Reset to specific commit
git reset --hard abc123
```

**Difference between --soft, --mixed, --hard:**
```bash
# Starting state: 3 commits ahead
# A -> B -> C (HEAD)

# --soft HEAD~1
# A -> B (HEAD), C's changes are STAGED
# Ready to recommit immediately

# --mixed HEAD~1 (default)
# A -> B (HEAD), C's changes are UNSTAGED
# Changes in working directory, need to git add again

# --hard HEAD~1
# A -> B (HEAD), C's changes are DELETED
# Cannot be recovered easily (use git reflog)
```

---

#### `git commit`
**Purpose:** Record changes to the repository

**Examples:**
```bash
# Basic commit with message
git commit -m "Add login feature"

# Multi-line commit message
git commit -m "Add login feature" -m "This includes username/password validation and remember me functionality"

# Commit with automatic staging of modified files
git commit -am "Quick fix for bug"
# Note: Only works for tracked files, not new files

# Open editor for detailed commit message
git commit

# Modify last commit (change message or add files)
git commit --amend -m "Updated message"

# Add more files to last commit without changing message
git add forgotten-file.txt
git commit --amend --no-edit

# Commit with specific author
git commit --author="John Doe <john@example.com>" -m "Message"

# Empty commit (useful for triggering CI/CD)
git commit --allow-empty -m "Trigger build"
```

**Good Commit Message Format:**
```
Short summary (50 chars or less)

Detailed explanation if needed (wrap at 72 chars):
- What changed
- Why it changed
- Any side effects or considerations

Fixes #123
```

---

### Configuration

#### `git config`
**Purpose:** Get and set repository or global options

**Examples:**
```bash
# Set global username (applies to all repositories)
git config --global user.name "Your Name"

# Set global email
git config --global user.email "your.email@example.com"

# Set default editor
git config --global core.editor "vim"
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "nano"

# Set compression level (0-9)
git config --global core.compression 2

# Set default diff tool
git config --global diff.tool vimdiff

# Set default merge tool
git config --global merge.tool vimdiff

# View all configurations
git config --list

# View specific configuration
git config user.name

# View configuration with origin
git config --list --show-origin

# Edit global config file directly
git config --global --edit

# Local configuration (repository-specific)
git config user.email "work@company.com"

# System-wide configuration (for all users)
git config --system core.editor vim

# Remove configuration
git config --global --unset user.name

# Useful aliases
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.lg 'log --oneline --graph --all'
```

**Configuration Levels:**
1. **System:** `/etc/gitconfig` - All users
2. **Global:** `~/.gitconfig` - Your user account
3. **Local:** `.git/config` - Current repository only

---

### Branching

#### `git branch`
**Purpose:** List, create, or delete branches

**Examples:**
```bash
# List local branches (* shows current)
git branch

# List all branches (local + remote)
git branch -a

# List remote branches only
git branch -r

# Create new branch (doesn't switch to it)
git branch feature-login

# Delete branch (safe - won't delete if unmerged)
git branch -d feature-login

# Force delete branch (even if unmerged)
git branch -D feature-login

# Rename current branch
git branch -m new-name

# Rename another branch
git branch -m old-name new-name

# Show last commit on each branch
git branch -v

# Show merged branches
git branch --merged

# Show unmerged branches
git branch --no-merged

# Create branch from specific commit
git branch bugfix abc123

# Set upstream branch
git branch --set-upstream-to=origin/main
```

---

#### `git checkout`
**Purpose:** Switch branches or restore files

**Examples:**
```bash
# Switch to existing branch
git checkout main

# Create and switch to new branch
git checkout -b feature-dashboard

# Create branch from specific commit and switch
git checkout -b bugfix abc123

# Discard changes in specific file
git checkout -- filename.txt

# Discard all changes in working directory ⚠️
git checkout .

# Switch to previous branch
git checkout -

# Checkout specific commit (detached HEAD)
git checkout abc123

# Checkout remote branch
git checkout -b local-name origin/remote-branch

# Restore file from specific commit
git checkout abc123 -- path/to/file.txt

# Checkout tag
git checkout v1.0.0
```

**Note:** `git switch` and `git restore` are newer alternatives:
```bash
# Modern alternatives
git switch main              # Switch branch
git switch -c new-branch     # Create and switch
git restore file.txt         # Discard changes
git restore --staged file.txt # Unstage file
```

---

### Remote Operations

#### `git remote`
**Purpose:** Manage set of tracked repositories

**Examples:**
```bash
# Add remote repository
git remote add origin https://github.com/username/repo.git

# List remotes
git remote

# List remotes with URLs
git remote -v
# Output:
# origin  https://github.com/username/repo.git (fetch)
# origin  https://github.com/username/repo.git (push)

# Show detailed info about remote
git remote show origin

# Rename remote
git remote rename origin upstream

# Change remote URL
git remote set-url origin https://github.com/username/new-repo.git

# Remove remote
git remote remove origin

# Add multiple remotes
git remote add upstream https://github.com/original/repo.git
git remote add origin https://github.com/yourfork/repo.git

# Prune deleted remote branches
git remote prune origin
```

---

#### `git push`
**Purpose:** Upload local commits to remote repository

**Examples:**
```bash
# Push to remote branch (first time)
git push -u origin main
# -u sets upstream tracking

# Push after upstream is set
git push

# Push specific branch
git push origin feature-login

# Push all branches
git push --all

# Push tags
git push --tags

# Push specific tag
git push origin v1.0.0

# Delete remote branch
git push origin --delete feature-old

# Force push (overwrites remote - dangerous!)
git push --force origin main

# Safer force push (fails if remote has unexpected changes)
git push --force-with-lease origin main

# Push to different remote branch name
git push origin local-branch:remote-branch

# Dry run (see what would be pushed)
git push --dry-run
```

---

#### `git clone`
**Purpose:** Create a copy of a remote repository

**Examples:**
```bash
# Clone repository
git clone https://github.com/username/repo.git

# Clone into specific directory
git clone https://github.com/username/repo.git my-folder

# Clone specific branch
git clone -b develop https://github.com/username/repo.git

# Shallow clone (only latest commit - faster)
git clone --depth 1 https://github.com/username/repo.git

# Clone with specific depth
git clone --depth 5 https://github.com/username/repo.git

# Clone without history (single branch only)
git clone --single-branch --branch main https://github.com/username/repo.git

# Clone via SSH
git clone git@github.com:username/repo.git

# Clone bare repository (no working directory)
git clone --bare https://github.com/username/repo.git
```

---

#### `git fetch`
**Purpose:** Download objects and refs from remote repository (doesn't merge)

**Examples:**
```bash
# Fetch from default remote (origin)
git fetch

# Fetch from specific remote
git fetch upstream

# Fetch specific branch
git fetch origin main

# Fetch all remotes
git fetch --all

# Fetch and prune deleted remote branches
git fetch --prune

# Fetch tags
git fetch --tags

# Dry run (show what would be fetched)
git fetch --dry-run
```

**Fetch vs Pull:**
- `git fetch`: Downloads changes but doesn't merge them
- `git pull`: Downloads changes AND merges them (fetch + merge)

---

#### `git pull`
**Purpose:** Fetch from remote and merge into current branch

**Examples:**
```bash
# Pull from tracked upstream
git pull

# Pull from specific remote and branch
git pull origin main

# Pull with rebase instead of merge
git pull --rebase

# Pull all submodules
git pull --recurse-submodules

# Pull and automatically stash/unstash changes
git pull --autostash

# Pull from all remotes
git pull --all

# Fast-forward only (fails if merge needed)
git pull --ff-only
```

**Pull = Fetch + Merge:**
```bash
# These are equivalent:
git pull origin main

# Same as:
git fetch origin main
git merge origin/main
```

---

### Merging & Rebasing

#### `git merge`
**Purpose:** Join two or more development histories together

**Examples:**
```bash
# Merge branch into current branch
git checkout main
git merge feature-login

# Merge without fast-forward (creates merge commit)
git merge --no-ff feature-login

# Squash all commits into one before merging
git merge --squash feature-login
git commit -m "Add login feature"

# Abort merge during conflicts
git merge --abort

# Continue merge after resolving conflicts
git merge --continue

# Merge with custom commit message
git merge feature-login -m "Merge login feature"

# Merge with strategy
git merge -X theirs feature-login  # Prefer their changes
git merge -X ours feature-login    # Prefer our changes
```

**Merge Conflict Resolution:**
```bash
# 1. Attempt merge
git merge feature-branch
# CONFLICT in file.js

# 2. Check status
git status

# 3. Open conflicted file and look for:
<<<<<<< HEAD
Your changes
=======
Their changes
>>>>>>> feature-branch

# 4. Edit file, remove markers, keep desired code

# 5. Stage resolved files
git add file.js

# 6. Complete merge
git commit
```

---

#### `git rebase`
**Purpose:** Reapply commits on top of another base branch

**Examples:**
```bash
# Rebase current branch onto main
git checkout feature-branch
git rebase main

# Interactive rebase (edit last 3 commits)
git rebase -i HEAD~3

# Interactive rebase commands:
# pick = keep commit
# reword = change commit message
# edit = stop and edit commit
# squash = combine with previous commit
# fixup = like squash but discard message
# drop = remove commit

# Continue after resolving conflicts
git rebase --continue

# Skip current commit
git rebase --skip

# Abort rebase
git rebase --abort

# Rebase onto different branch
git rebase --onto main feature-old feature-new

# Preserve merge commits
git rebase --preserve-merges main

# Auto-squash fixup commits
git rebase -i --autosquash main
```

**Merge vs Rebase:**
```bash
# Merge: Creates merge commit, preserves history
git merge feature
# A---B---C main
#      \   \
#       D---E merge commit

# Rebase: Linear history, rewrites commits
git rebase main
# A---B---C---D'---E' (D and E are rewritten)
```

---

#### `git cherry-pick`
**Purpose:** Apply changes from specific commits

**Examples:**
```bash
# Copy specific commit to current branch
git cherry-pick abc123

# Copy multiple commits
git cherry-pick abc123 def456

# Copy range of commits
git cherry-pick abc123..def456

# Cherry-pick without committing
git cherry-pick -n abc123

# Continue after resolving conflicts
git cherry-pick --continue

# Abort cherry-pick
git cherry-pick --abort

# Cherry-pick with different author
git cherry-pick abc123 --author="John <john@example.com>"
```

---

#### `git mergetool`
**Purpose:** Launch merge conflict resolution tool

**Examples:**
```bash
# Launch default merge tool
git mergetool

# Use specific tool
git mergetool --tool=vimdiff
git mergetool --tool=meld
git mergetool --tool=kdiff3

# List available tools
git mergetool --tool-help

# Configure default tool
git config --global merge.tool vimdiff
git config --global diff.tool vimdiff

#Other clear explaination of Git conflict resoluton
**1. git config --global core.editor vim**
What it does

Sets Vim as the default text editor Git uses.

When it is used

Git opens an editor when:

Writing commit messages

Editing merge commits

Editing rebase todo files

Editing tags

Example
git commit


Vim opens instead of nano or vi.

Reality check

This does not affect diff or merge tools

Only controls text editing

**2. git config --global core.compression 2**
What it does

Sets Git object compression level.

Range: 0 (no compression) → 9 (max compression)

Default: 6

What level 2 means

Faster operations

Slightly larger repository size

When it matters

Large repositories

High-performance CI/CD environments

Truth

For normal developers, this setting gives negligible benefit. Don’t overthink it.

**3. git config --global diff.tool vimdiff**
What it does

Sets vimdiff as the default tool for viewing diffs.

How to use it
git difftool

This opens:

Left: current file

Right: changed file

With syntax highlighting and split view

Common mistake
Running git diff
❌ That does NOT use vimdiff
✔️ Use git difftool

**4. git mergetool**
What it does
Launches the configured merge tool (vimdiff here) during a merge conflict.
When it is used
git merge branch-name
git mergetool

It opens 3–4 panes depending on config.
5. Vimdiff panes explained (CRITICAL)
Inside git mergetool, Vim opens:

Label	Meaning
LOCAL (LO)	Your current branch
REMOTE (RE)	Incoming branch
BASE (BA)	Common ancestor
MERGED	Final output
6. Vimdiff conflict resolution commands
:diffg LO
Uses LOCAL version
:diffg LO

👉 Keeps your branch changes
:diffg BA
Uses BASE version
:diffg BA

👉 Restores original common version
⚠️ Rarely useful in real merges
:diffg RE
Uses REMOTE version
:diffg RE

👉 Accepts incoming branch changes
7. Saving and exiting merge tool
:wqa
What it does
w → write (save)
q → quit
a → all buffers

Meaning

✔️ Saves all merge changes
✔️ Exits vimdiff
✔️ Returns control to Git

8. Correct full conflict resolution flow (REAL LIFE)
git merge feature-branch
# conflict occurs

git mergetool

Inside vimdiff:
:diffg RE    " or LO
:wqa

Back in terminal:
git status
git commit
```

---

### History & Logs

#### `git log`
**Purpose:** Show commit logs

**Examples:**
```bash
# Basic log
git log

# Compact one-line format
git log --oneline

# Show graph of branches
git log --graph --all

# Beautiful graph
git log --oneline --graph --all --decorate

# Show last N commits
git log -n 5
git log -5

# Show commits since date
git log --since="2 weeks ago"
git log --since="2024-01-01"

# Show commits by author
git log --author="John"

# Search commit messages
git log --grep="bug fix"

# Show changes (patches)
git log -p

# Show stats
git log --stat

# Show commits that changed specific file
git log -- path/to/file.txt

# Follow file through renames
git log --follow file.txt

# Show commits between dates
git log --since="2024-01-01" --until="2024-12-31"

# Show commits affecting specific function
git log -L :functionName:file.js

# Show commits in range
git log main..feature-branch

# Pretty format
git log --pretty=format:"%h - %an, %ar : %s"

# Show diff for specific file
git log -p -- file.txt

# Search for code changes (pickaxe)
git log -S "function_name"

# Show merge commits only
git log --merges

# Show commits not yet merged
git log --no-merges

# Reverse order (oldest first)
git log --reverse
```

---

#### `git show`
**Purpose:** Show various types of objects

**Examples:**
```bash
# Show last commit
git show

# Show specific commit
git show abc123

# Show specific file at commit
git show abc123:path/to/file.txt

# Show tag
git show v1.0.0

# Show commit stats only
git show --stat abc123

# Show commit changes for specific file
git show abc123 -- file.txt
```

---

#### `git diff`
**Purpose:** Show changes between commits, working tree, etc.

**Examples:**
```bash
# Show unstaged changes
git diff

# Show staged changes
git diff --staged
# or
git diff --cached

# Show all changes (staged + unstaged)
git diff HEAD

# Compare two commits
git diff abc123 def456

# Compare branches
git diff main..feature-branch

# Show only filenames
git diff --name-only

# Show stats
git diff --stat

# Compare with remote branch
git diff main origin/main

# Show changes for specific file
git diff path/to/file.txt

# Word diff (better for text)
git diff --word-diff

# Ignore whitespace
git diff -w

# Show changes between commits for specific file
git diff abc123 def456 -- file.txt
```

---

#### `git reflog`
**Purpose:** Show reference logs (history of HEAD movements)

**Examples:**
```bash
# Show all reference updates
git reflog

# Show reflog for specific branch
git reflog show main

# Show last 10 entries
git reflog -10

# Recover from reflog entry
git checkout HEAD@{2}

# Create branch from reflog entry
git checkout -b recovery HEAD@{5}

# Reset to reflog entry
git reset --hard HEAD@{3}
```

---

#### `git revert`
**Purpose:** Create new commit that undoes previous commit (safe undo)

**Examples:**
```bash
# Revert last commit
git revert HEAD

# Revert specific commit
git revert abc123

# Revert without committing (stage changes only)
git revert -n abc123

# Revert range of commits
git revert abc123..def456

# Revert merge commit
git revert -m 1 abc123

# Continue after resolving conflicts
git revert --continue

# Abort revert
git revert --abort
```

---

### Tags

#### `git tag`
**Purpose:** Create, list, delete tags

**Examples:**
```bash
# List all tags
git tag

# List tags matching pattern
git tag -l "v1.*"

# Create lightweight tag
git tag v1.0.0

# Create annotated tag (recommended)
git tag -a v1.0.0 -m "Version 1.0.0 release"

# Tag specific commit
git tag -a v0.9.0 abc123 -m "Beta release"

# Show tag details
git show v1.0.0

# Push tag to remote
git push origin v1.0.0

# Push all tags
git push --tags

# Delete local tag
git tag -d v1.0.0

# Delete remote tag
git push origin --delete v1.0.0

# Checkout tag
git checkout v1.0.0

# Create branch from tag
git checkout -b version1 v1.0.0
```

---

### Stashing

#### `git stash`
**Purpose:** Temporarily save changes without committing

**Examples:**
```bash
# Stash current changes
git stash

# Stash with message
git stash save "WIP: working on feature"
# or modern syntax:
git stash push -m "WIP: working on feature"

# Include untracked files
git stash -u

# Include ignored files too
git stash -a

# List all stashes
git stash list

# Show stash diff
git stash show
git stash show -p  # Show full diff

# Apply stash (keep in stash list)
git stash apply

# Apply specific stash
git stash apply stash@{2}

# Apply and remove from stash list
git stash pop

# Remove specific stash
git stash drop stash@{1}

# Remove all stashes
git stash clear

# Create branch from stash
git stash branch new-branch-name

# Stash only staged changes
git stash --keep-index

# Stash specific files
git stash push path/to/file.txt
```

---

### Other Important Commands

#### `git rm`
**Purpose:** Remove files from working tree and index

**Examples:**
```bash
# Remove file (from Git and filesystem)
git rm file.txt

# Remove file from Git but keep in filesystem
git rm --cached file.txt

# Remove directory
git rm -r folder/

# Force removal
git rm -f file.txt

# Dry run
git rm -n file.txt
```

---

#### `git mv`
**Purpose:** Move or rename files

**Examples:**
```bash
# Rename file
git mv oldname.txt newname.txt

# Move file
git mv file.txt folder/

# Equivalent to:
mv oldname.txt newname.txt
git rm oldname.txt
git add newname.txt
```

---

#### `git clean`
**Purpose:** Remove untracked files

**Examples:**
```bash
# Dry run (show what would be deleted)
git clean -n

# Remove untracked files
git clean -f

# Remove untracked files and directories
git clean -fd

# Remove ignored files too
git clean -fx

# Interactive mode
git clean -i
```

---

#### `git restore`
**Purpose:** Restore working tree files (modern alternative to checkout)

**Examples:**
```bash
# Discard changes in file
git restore file.txt

# Restore all files
git restore .

# Unstage file
git restore --staged file.txt

# Restore from specific commit
git restore --source=abc123 file.txt

# Restore and unstage
git restore --staged --worktree file.txt
```

---

#### `git switch`
**Purpose:** Switch branches (modern alternative to checkout)

**Examples:**
```bash
# Switch to branch
git switch main

# Create and switch to new branch
git switch -c new-feature

# Switch to previous branch
git switch -

# Create branch from specific commit
git switch -c bugfix abc123
```

---

#### `git blame`
**Purpose:** Show who changed each line of a file

**Examples:**
```bash
# Show annotations for entire file
git blame file.txt

# Show specific line range
git blame -L 10,20 file.txt

# Show with email
git blame -e file.txt

# Detect moved/copied lines
git blame -C file.txt

# Ignore whitespace changes
git blame -w file.txt
```

---

#### `git grep`
**Purpose:** Search repository content

**Examples:**
```bash
# Search for text
git grep "TODO"

# Show line numbers
git grep -n "function"

# Count matches
git grep -c "import"

# Search in specific commit
git grep "bug" abc123

# Case insensitive
git grep -i "error"

# Show context lines
git grep -C 3 "function"
```

---

## Advanced Commands

### `git bisect`
**Purpose:** Binary search to find commit that introduced a bug

**Example:**
```bash
git bisect start
git bisect bad  # Current commit is bad
git bisect good abc123  # This commit was good

# Git checks out middle commit
# Test if bug exists, then:
git bisect bad   # Bug exists
# or
git bisect good  # Bug doesn't exist

# Repeat until found
git bisect reset  # End bisect session
```

---

### `git worktree`
**Purpose:** Manage multiple working trees

**Example:**
```bash
git worktree add ../hotfix main
cd ../hotfix
# Work on hotfix
cd ../main-repo
git worktree remove ../hotfix
```

---

### `git submodule`
**Purpose:** Manage repositories within repositories

**Example:**
```bash
git submodule add https://github.com/user/lib.git libs/lib
git submodule update --init --recursive
git submodule update --remote
```

---

### `git archive`
**Purpose:** Create archive of files from repository

**Example:**
```bash
git archive --format=zip HEAD > project.zip
git archive --format=tar main | gzip > project.tar.gz
git archive --format=tar --prefix=project/ HEAD | gzip > project.tar.gz
```

---

### `.gitignore`
**Purpose:** Specify intentionally untracked files to ignore

**Example:**
```bash
# Create .gitignore
cat > .gitignore << EOF
# Dependencies
node_modules/
vendor/

# Build output
dist/
build/
*.o
*.exe

# IDE
.vscode/
.idea/
*.swp

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Environment
.env
.env.local

# Specific file
config/secrets.yml

# Exception (don't ignore this)
!important.log
EOF

git add .gitignore
git commit -m "Add gitignore"
```

---

### `git init --bare`
**Purpose:** Create bare repository (no working directory)

**Example:**
```bash
git init --bare myrepo.git
# Used for central repositories on servers
```

---

## Practical Scenarios

### Scenario 1: Starting a New Project
```bash
mkdir myapp
cd myapp
git init
echo "# MyApp" > README.md
git add README.md
git commit -m "Initial commit"
git remote add origin https://github.com/username/myapp.git
git push -u origin main
```

---

### Scenario 2: Feature Branch Workflow
```bash
git checkout -b feature/login
# Make changes
git add .
git commit -m "Add login page"
git push -u origin feature/login
# Create Pull Request on GitHub
# After approval:
git checkout main
git pull origin main
git merge feature/login
git push origin main
git branch -d feature/login
git push origin --delete feature/login
```

---

### Scenario 3: Fixing Production Bug
```bash
git checkout main
git pull origin main
git checkout -b hotfix/critical-bug
# Fix bug
git add .
git commit -m "Fix critical security bug"
git push -u origin hotfix/critical-bug
# Fast-track review and merge
git checkout main
git merge hotfix/critical-bug
git push origin main
git tag -a v1.2.1 -m "Hotfix release"
git push --tags
```

---

### Scenario 4: Recovering Deleted Work
```bash
# Oops, did hard reset
git reset --hard HEAD~3

# Find lost work
git reflog
# Output: abc123 HEAD@{1}: commit: Important feature

# Recover
git checkout -b recovery abc123
# Or merge it back
git merge abc123
```

---

### Scenario 5: Cleaning Up History
```bash
git rebase -i HEAD~5
# In editor, mark commits to squash:
pick abc123 Add feature
squash def456 Fix typo
squash ghi789 Another fix
pick jkl012 Add tests
# Save and edit commit message
```

---

### Scenario 6: Syncing Fork with Upstream
```bash
git remote add upstream https://github.com/original/repo.git
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

---

## Daily Workflow

### Morning Routine
```bash
git checkout main
git pull origin main
git checkout feature-branch
git rebase main  # Keep branch updated
```

---

### Before Committing
```bash
git status
git diff
git add -p  # Review each change
git commit -m "Clear message"
```

---

### Before Pushing
```bash
git log --oneline origin/feature-branch..HEAD  # See what will be pushed
git push origin feature-branch
```

---

### End of Day
```bash
git add .
git commit -m "WIP: progress on feature"
# OR
git stash save "WIP: unfinished feature"
```

---

## Troubleshooting Guide

### "I committed to the wrong branch"
```bash
git log --oneline -n 1  # Get commit ID (abc123)
git checkout correct-branch
git cherry-pick abc123
git checkout wrong-branch
git reset --hard HEAD~1
```

---

### "I need to undo my last commit"
```bash
# Keep changes
git reset --soft HEAD~1

# Discard changes
git reset --hard HEAD~1

# Create reverting commit (safest)
git revert HEAD
```

---

### "I have merge conflicts"
```bash
git status  # See conflicted files
# Edit files, remove markers <<<< ==== >>>>
git add resolved-file.js
git commit
# OR
git merge --abort  # Start over
```

---

### "I accidentally deleted a file"
```bash
git restore deleted-file.txt
# OR
git checkout HEAD -- deleted-file.txt
```

---

### "I pushed sensitive data"
```bash
git rm --cached secrets.txt
echo "secrets.txt" >> .gitignore
git add .gitignore
git commit -m "Remove secrets"
git push
# IMPORTANT: Rotate all exposed credentials!
```

---

### "My branch is behind main"
```bash
git checkout main
git pull origin main
git checkout feature-branch
git rebase main
# Resolve conflicts if any
git push --force-with-lease origin feature-branch
```

---

### "I want to edit an old commit"
```bash
git rebase -i HEAD~5
# Change 'pick' to 'edit' for commit to change
# Make changes
git add .
git commit --amend
git rebase --continue
```

---

## Git Aliases (Time Savers)

Add to `~/.gitconfig`:
```ini
[alias]
    co = checkout
    

## Medium Doucument
```bash

```ini

[Complete Git Mastery Guide](https://medium.com/@bhargavt639/the-complete-git-mastery-guide-379abfe316c2)
