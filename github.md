# Complete Git & GitHub Cheat Sheet

## 🗃️ Git Stash Operations
```bash
# Save current work with custom message
git stash save "Work in progress on feature X"
git stash push -m "Custom message"

# List all stashes
git stash list

# Apply and remove latest stash
git stash pop

# Apply stash without removing it
git stash apply
git stash apply stash@{2}  # Apply specific stash

# Show stash contents
git stash show
git stash show -p stash@{1}  # Show patch

# Delete stashes
git stash drop stash@{1}  # Delete specific stash
git stash clear           # Delete all stashes

# Stash specific files
git stash push -m "message" -- file1.js file2.css
```

## 📊 Git Diff & Log Operations
```bash
# View differences
git diff                    # Working directory vs staging
git diff --staged          # Staging vs last commit
git diff HEAD              # Working directory vs last commit
git diff branch1 branch2   # Compare branches

# Compare commits
git diff HEAD~2 HEAD       # Last 2 commits
git diff commit1..commit2  # Between specific commits

# Advanced log viewing
git log --oneline          # Compact log
git log --graph --oneline  # Visual branch graph
git log -p                 # Show patches
git log --since="2 weeks ago"
git log --author="John"
git log --grep="bug fix"   # Search commit messages
```

## 🔄 Commit Management & Squashing
```bash
# Interactive rebase (better than reset for squashing)
git rebase -i HEAD~3       # Last 3 commits
# In editor: change "pick" to "squash" or "s"

# Reset methods (destructive - use with caution)
git reset --soft HEAD~3    # Keep changes in staging
git reset --mixed HEAD~3   # Keep changes in working directory (default)
git reset --hard HEAD~3    # Discard all changes

# Safe squashing with merge
git reset --soft HEAD~3
git commit -m "Squashed commits message"

# Amend commits
git commit --amend -m "New message"
git commit --amend --no-edit  # Keep same message
git add file.txt && git commit --amend --no-edit  # Add to last commit
```

## ⏪ Reverting Changes
```bash
# Local changes
git checkout -- filepath           # Revert file in working directory
git checkout .                     # Revert all files
git clean -fd                      # Remove untracked files/directories
git clean -n                       # Dry run (show what will be deleted)

# Staging area
git reset HEAD filepath            # Unstage specific file
git reset HEAD                     # Unstage all files

# Commit level (safe for shared repos)
git revert <commit-hash>           # Create new commit that undoes changes
git revert --no-commit <commit>    # Revert without auto-commit
git revert HEAD~2..HEAD           # Revert range of commits

# Check what you've done
git reflog                         # Show all HEAD movements
git reflog --date=relative         # With relative dates
```

## 🍒 Cherry Pick Operations
```bash
# Basic cherry pick
git checkout target-branch
git cherry-pick <commit-hash>

# Cherry pick multiple commits
git cherry-pick commit1 commit2 commit3
git cherry-pick commit1..commit3   # Range (exclusive of commit1)
git cherry-pick commit1^..commit3  # Range (inclusive of commit1)

# Cherry pick options
git cherry-pick -x <commit>        # Add source commit reference
git cherry-pick --no-commit <commit>  # Don't auto-commit
git cherry-pick -m 1 <merge-commit>   # Pick from merge commit

# Handle conflicts
git cherry-pick --continue         # After resolving conflicts
git cherry-pick --abort           # Cancel cherry-pick
git cherry-pick --skip            # Skip current commit
```

## 🌿 Branch Operations
```bash
# Create and switch branches
git branch feature-branch         # Create branch
git checkout feature-branch       # Switch to branch
git checkout -b feature-branch    # Create and switch
git switch -c feature-branch      # Modern way (Git 2.23+)

# Branch management
git branch                        # List local branches
git branch -r                     # List remote branches
git branch -a                     # List all branches
git branch -d branch-name         # Delete local branch (safe)
git branch -D branch-name         # Force delete local branch
git push origin --delete branch-name  # Delete remote branch

# Rename branches
git branch -m old-name new-name   # Rename branch
git branch -M new-name            # Rename current branch

# Track remote branches
git branch -u origin/main         # Set upstream for current branch
git checkout -b local-branch origin/remote-branch  # Create tracking branch
```

## 🔄 Merge & Rebase
```bash
# Merging
git merge feature-branch          # Merge into current branch
git merge --no-ff feature-branch  # Force merge commit
git merge --squash feature-branch # Squash merge

# Rebasing
git rebase main                   # Rebase current branch onto main
git rebase -i HEAD~3              # Interactive rebase
git rebase --onto main dev~3 dev  # Advanced rebase

# Handle merge/rebase conflicts
git status                        # Check conflict status
git add resolved-file             # Mark as resolved
git merge --continue              # Continue merge
git rebase --continue             # Continue rebase
git merge --abort                 # Abort merge
git rebase --abort                # Abort rebase
```

## 🌐 Remote Operations
```bash
# Remote management
git remote -v                     # List remotes
git remote add origin <url>       # Add remote
git remote set-url origin <new-url>  # Change remote URL
git remote remove origin          # Remove remote

# Fetching and pulling
git fetch                         # Fetch all remotes
git fetch origin                  # Fetch specific remote
git pull                          # Fetch and merge
git pull --rebase                 # Fetch and rebase
git pull origin main              # Pull specific branch

# Pushing
git push                          # Push current branch
git push origin branch-name       # Push specific branch
git push -u origin branch-name    # Push and set upstream
git push --force-with-lease       # Safe force push
git push -f                       # Force push (dangerous)
git push origin develop:update-docker-image  # Push to different remote branch
```

## 📋 GitHub CLI (gh) Commands
```bash
# Installation and setup
gh auth login                     # Authenticate with GitHub

# Repository operations
gh repo create my-repo            # Create repository
gh repo clone owner/repo          # Clone repository
gh repo fork owner/repo           # Fork repository
gh repo view                      # View repository info
gh repo list                      # List your repositories

# Pull Request operations
gh pr create                      # Create pull request
gh pr create --title "Title" --body "Description"
gh pr list                        # List pull requests
gh pr view 123                    # View PR #123
gh pr checkout 123                # Checkout PR #123
gh pr merge 123                   # Merge PR #123
gh pr close 123                   # Close PR #123
gh pr review 123 --approve        # Approve PR
gh pr review 123 --request-changes -b "Comments"

# Issue operations
gh issue create                   # Create issue
gh issue list                     # List issues
gh issue view 456                 # View issue #456
gh issue close 456                # Close issue #456
gh issue comment 456 --body "Comment"

# Workflow operations
gh workflow list                  # List workflows
gh workflow run workflow.yml      # Trigger workflow
gh run list                       # List workflow runs
gh run view 789                   # View run #789
```

## 🔐 GitHub SSH & Security
```bash
# SSH key management
ssh-keygen -t ed25519 -C "your_email@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
gh ssh-key add ~/.ssh/id_ed25519.pub

# Test SSH connection
ssh -T git@github.com

# GPG signing
git config --global user.signingkey <key-id>
git config --global commit.gpgsign true
git commit -S -m "Signed commit"
```

## 🏷️ Tagging
```bash
# Create tags
git tag v1.0.0                    # Lightweight tag
git tag -a v1.0.0 -m "Version 1.0.0"  # Annotated tag
git tag -a v1.0.0 -m "Message" <commit>  # Tag specific commit

# List and show tags
git tag                           # List all tags
git tag -l "v1.*"                 # List tags matching pattern
git show v1.0.0                   # Show tag info

# Push tags
git push origin v1.0.0            # Push specific tag
git push origin --tags            # Push all tags
git push --follow-tags            # Push commits and tags together

# Delete tags
git tag -d v1.0.0                 # Delete local tag
git push origin --delete v1.0.0   # Delete remote tag
```

## 🔍 Advanced Git Operations
```bash
# Search and blame
git grep "search term"            # Search in repository
git blame file.txt                # Show line-by-line authorship
git log -S "search term"          # Find commits that added/removed text

# Submodules
git submodule add <url> path      # Add submodule
git submodule update --init --recursive  # Initialize submodules
git submodule update --remote     # Update submodules

# Worktrees (multiple working directories)
git worktree add ../feature-branch feature
git worktree list                 # List worktrees
git worktree remove ../feature-branch

# Bisect (find bugs)
git bisect start                  # Start bisecting
git bisect bad                    # Mark current commit as bad
git bisect good <commit>          # Mark commit as good
git bisect reset                  # End bisect session
```

## 🆘 Emergency Commands
```bash
# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Recover lost commits
git reflog                        # Find lost commit hash
git checkout <lost-commit-hash>
git branch recovery <lost-commit-hash>

# Fix detached HEAD
git checkout main
git branch temp-branch            # If you made commits in detached HEAD

# Remove file from Git but keep locally
git rm --cached file.txt
echo "file.txt" >> .gitignore

# Change last commit author
git commit --amend --author="New Author <email@example.com>"
```

## 📝 Useful Aliases
```bash
# Add to ~/.gitconfig or run git config --global alias.<name> "<command>"
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm commit
git config --global alias.lg "log --oneline --graph --all"
git config --global alias.unstage "reset HEAD --"
git config --global alias.last "log -1 HEAD"
git config --global alias.amend "commit --amend --no-edit"
```

## ⚙️ Configuration
```bash
# User configuration
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Editor and tools
git config --global core.editor "code --wait"  # VS Code
git config --global merge.tool vimdiff

# Line endings
git config --global core.autocrlf true   # Windows
git config --global core.autocrlf input  # macOS/Linux

# Default branch
git config --global init.defaultBranch main

# View configuration
git config --list
git config --global --list
```

---

*💡 **Pro Tips:**
- Use `git status` frequently to understand your repository state
- Always create branches for new features: `git checkout -b feature/new-feature`
- Use `--dry-run` or `-n` flags to preview destructive operations
- Set up aliases for frequently used commands
- Use `git stash` before switching branches with uncommitted changes
- Prefer `git pull --rebase` to keep a cleaner history*
