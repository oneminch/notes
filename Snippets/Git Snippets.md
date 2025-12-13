## Assorted

### Add an existing local project to GitHub

```bash
git init
git add .
git commit -m "Add existing project files to Git"
git remote add origin <remote github url>
git push -u origin master
```

### Restore / Discard Changes in Tracked Files

```bash
git restore <file>
```

### Syncing a Local Branch with `main`

```bash
# TODO
```

### Unstage File from Git

```bash
git restore --staged <file>

# Older
git reset <file>
```

## Branches

### Create a Local Branch Tracking a Remote One.

```bash
# To work on a non-default branch after cloning
git checkout -b <branch_name> origin/<branch_name>
# OR
git switch -c <branch_name> origin/<branch_name>
```

### Delete a Local Branch

```bash
git branch -d <branch_name>
```

### Delete a Remote Branch

```bash
git push origin --delete <branch_name>
```

### Difference between Branches

```bash
# Show all the differences between two branches
git diff branch1 branch2

# Show the differences in specific files between two branches
git diff branch1 branch2 -- path/to/file

# Compare with a common ancestor
git diff branch1...branch2

# View commits that differ between two branches
# List commits in `branch2` that are not in `branch1`
git log branch1..branch2
```

### Rename Branches

#### Locally

```bash
git checkout <old-branch-name>

git branch -m <new-branch-name>
```

#### Remote

```bash
git push origin --delete <old-branch-name>

git push origin -u <new-branch-name>
```

## Commits

### Add new file / changes to a previous commit

```bash
# After Making Edits
git add <file>

git commit --amend --no-edit
```

### Change the commit message of the most recent unpushed change

```bash
git commit --amend -m "New commit message."
```

### Revert to a previous commit

```bash
# Move HEAD to the specified commit
git reset --hard <short_hash>
```

### Rewind to a certain commit in history

```bash
git checkout <commit-number>
```

### Remove File from Previous Commit

```bash
git reset --soft HEAD^
# OR git reset --soft HEAD~1
# OR git reset HEAD path/to/file (Old Way)

# New Way
git restore --staged path/to/file

# Reuse Commit (or use new one)
git commit -c ORIG_HEAD

# Remove folder
git rm -r --cached path/to/folder

git commit --amend
```

### Save Uncommitted Changes and Revert to Last Commit

```bash
git stash

# Include Untracked Files
git stash -u

# With Message
git stash save "Message"

# List all Stashes
git stash list

# Apply & then Remove Most Recent Stash
git stash pop

# Apply a Stash without Removing it
git stash apply stash@{n}

# Delete a Stash
git stash drop stash@{n}

# Delete all Stashes
git stash clear
```

### Undo the most recent commit

```bash
# Undo commit but keep staged changes
git reset --soft HEAD~1

# Undo commit & unstage the changes
git reset HEAD~1

# Undo commit & discard the changes
git reset --hard HEAD~1
```

#### Revert most recent commit

- Create a new commit that undoes the changes introduced by the most recent commit.
    - Safer for already pushed commits.
    - Preserves the commit history.

```bash
git revert HEAD
```

### Undo a Conflict & Start Over

```bash
git merge --abort

git rebase --abort
```

## Remote

### Add new file / changes to an already pushed commit

```bash
# After Making Edits
git add <file>

git commit --amend --no-edit

git push --force origin <branch-name>
```

### Add a new remote

```bash
git remote remove <remote_name>

git remote add <remote_name> <remote_url>
```

### Get Current Remote URL

```bash
git remote get-url <remote_name>
# e.g. git remote get-url origin
```

### Push all local branches to remote

```bash
git push <remote> --all
```

### Push currently checked-out branch

```bash
# (on feature/two-factor-auth)
git push -u origin HEAD

# Instead of
git push -u origin feature/two-factor-auth
```

### Work with Multiple Remotes using SSH

```bash
# For personal account
ssh-keygen -t ed25519 -C "personal@example.com" -f ~/.ssh/id_ed25519_personal

# For work account
ssh-keygen -t ed25519 -C "work@company.com" -f ~/.ssh/id_ed25519_work

eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_work
```

```yml
# ~/.ssh/config
Host gh-personal
HostName github.com
User git
IdentityFile ~/.ssh/id_ed25519_personal

Host gh-work
HostName github.com
User git
IdentityFile ~/.ssh/id_ed25519_work
```

```bash
cd /path/to/repo

git remote add origin git@[gh-personal|gh-work]:username/repo.git
```
