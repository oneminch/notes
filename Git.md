## Fundamentals

- `git add`
    - It's possible to add parts of a file to the staging area.

```bash
git add -p <file>
```

- `git fetch`
    - Downloads commits, files and refs from a remote repo without modifying the working directory.
    - Useful for reviewing changes before integrating them.

```bash
git fetch origin
```

- `git pull`
    - Combines the operations of `git fetch` and `git merge`.
    - Fetches changes from the remote repository and automatically merges them into the current branch.
    - Used to quickly sync a local branch with the remote repo.

```bash
git pull origin main
```

> [!note]
> Upstream in Git refers to the original repository from which a project was forked or cloned.

## Branching Strategies

- Long-running branches
    - Exist thru the entire lifetime of a project.
    - Often mirror stages in the development lifecycle (e.g. dev).
    - Contain no direct commits as a convention.
- Short-lived Branches
    - are used for new features, fixes, and refactors.
    - get deleted after integration (merge or rebase).

### GitHub Flow

- Contains one long-running branch (`main`) & multiple short-lived branches (e.g. feature branches).

### GitFlow

- Has more rules and structure.
- Contains long-running branches (`main` and `develop`) and short-lived branches (e.g. `feature`) that start from and get merged to a long-running branch (e.g. `develop`).
    - e.g. A `release` branch can be started from a long-running branch such as `develop`, but get merged to the `main` branch.

## Manipulating History

### Merge

- **Fast Forward Merges**
    - A simplified merging process that occurs when the branch being merged into (usually `main`) _has not diverged_ from the feature branch being merged.
    - Maintains a linear commit history, keeping all the commits from the feature branch in sequence.
    - Possible when there's a linear path from the base branch to the feature branch.
        - No new commits on base branch: The base branch (e.g., main) must not have any new commits since the feature branch was created or last updated.
    - Instead of creating a new merge commit, Git simply moves the pointer of the base branch forward to the latest commit of the feature branch.

```bash
# Before
A --- B --- C (main)
            \
             D --- E (feature)

# After
A --- B --- C --- D --- E (main, feature)
```

- **Merge Commits**
    - Created when the base branch has diverged from the feature branch.
    - Combine changes from merging branches.
    - May require manual conflict resolution if a merge conflict occurs.

```bash
A --- B --- C --- D (main)
       \
        E ---- F ---- G (feature)

# git checkout main
# git merge feature

A --- B --- C --- D --- H (main)
       \               /
        E ---- F ---- G (feature)
```

### Rebase

- Moving or replanting a branch to a new starting point.
- Used to create a clean, linear commit history.
- Involves rewriting a branch's history.

```bash
git rebase <base-branch>
```

- The process:
    - Git temporarily sets aside changes made in the current branch (e.g. `feature`).
    - It moves the base of the branch to the latest commit of the base branch (e.g. `main`).
    - It applies the changes on top of this new base, one by one.

```bash
A --- B --- C (main)
       \
        D --- E (feature)

# git checkout feature
# git rebase main

A --- B --- C (main)
             \
              D* --- E* (feature)
```

> [!important]
> Rebase shouldn't be used on commits that are already pushed to a remote repository.
>
> They should be used locally to clean up commit history before merging into a shared branch.

### Stash

- Used to temporarily save changes made to a working directory without committing them.
- Useful when switching branches or performing other operations without having to commit current changes.

```bash
# Save Uncommitted Changes and Revert to Last Commit
git stash

# Include Untracked Files
git stash -u

# With Message
git stash save "Message"

# List all Stashes
git stash list

# Apply & then Remove Most Recent Stash
git stash pop

# Apply a Stash without Removing it (n - index of stash)
git stash apply stash@{n}

# Delete a Stash
git stash drop stash@{n}

# Delete all Stashes
git stash clear
```

### Squash

- Used to combine multiple commits into a single commit.
- Useful for cleaning up and organizing commit history before merging changes.

> [!note]
> To exclude a specific commit from a squash operation, the commits must be reordered in the editor, so that the omitted commit appears at the bottom of the list.

```bash
# Before
A --- B --- C --- D --- E (feature)

# Using Interactive Rebase
git rebase -i HEAD~4

# In the editor,
# -> Change 'pick' to 'squash'/'s' for commits to be squashed
# -> Save and quit the editor
pick B <commit-message-for-B>  # Base commit
squash D <commit-message-for-D>
squash E <commit-message-for-E>
pick C <commit-message-for-C> # Excluded Commit

# In the new editor, enter new commit message at the top, save and quit.

# After
A --- F --- C (feature)
```

- It's also possible to squash commits during a merge using the `--squash` flag.

```bash
git merge --squash feature

git commit -m "Merged feature with squashed commits"
```

### Cherry-Picking

- Used to apply specific commits from one branch to another.
- Useful to selectively apply changes without merging entire branches.
    - e.g. Applying a bug fix to multiple branches

```bash
A --- B --- C --- D (main)
       \
        E --- F --- G (feature)

# To apply commit F to the main branch
git checkout main
git cherry-pick <commit-hash-of-F>

A --- B --- C --- D --- F* (main)
       \
        E --- F --- G (feature)

```

### Restoring

- `git reset`
    - Moves the branch pointer to a different commit and can modify the staging area and working directory.
    - Affects entire commits.
    - Can alter commit history.
    - Useful for local changes or branch manipulation.
    - Can be dangerous (especially with `--hard`).
    - Behaves like a time machine for your repository.

```bash
# Undo last commit, keep changes staged:
git reset --soft HEAD~1

# Undo last commit, unstage changes:
git reset HEAD~1

# Undo last commit and discard changes:
git reset --hard HEAD~1
```

- `git restore`
    - Used to discard changes in the working directory or unstage files.
    - Works on specific files.
    - Doesn't affect commit history.
    - Useful for undoing local file changes.
    - Generally safe for local changes.
    - Behaves like an "undo" button for specific files.

```bash
# Discard changes in a file:
git restore file.txt

# Unstage a file:
git restore --staged file.txt
```

- `git revert`
    - Used to reverse the effects of a specific commit by creating a new commit that undoes those changes.
    - Adds to commit history.
    - Creates a new commit to undo changes.
    - Useful for safely undoing pushed commits.
    - Safe for shared repositories.
    - Behaves like an "undo" that creates a new commit.

```bash
# Revert the last commit:
git revert HEAD
```

## Tags

- Serve as landmarks that mark significant milestones in a project's development.
    - e.g. releases, versions, [[CI & CD|CI/CD]], etc
- Most commonly used in conjunction with [[semantic versioning]] to mark specific release points, which allows developers to easily reference and check out a specific version of a codebase.
- Immutable unlike branches.

```bash
# Create a Lightweight Tag
git tag v1.0-lw

# Create an Annotated Tag
git tag -a v1.4 -m "Version 1.4"

# List All Tags
git tag

# Delete Tag Locally
git tag --delete v1.4

# Delete Tag from Remote
git push origin --delete v1.4

# Push a Tag
git push origin v1.4

# Push All Tags
git push origin --tags
```

## `.gitignore`

- Patterns
    - `*` - used as a wildcard match.
    - `/` - used to ignore pathnames relative to the `.gitignore` file.
    - `#` - used to add comments to a `.gitignore` file.
    - `**` - used to match any number of directories.
    - `!` - used to negate a file that would be ignored.

```gitignore
*.log
!example.log
```

## Workflows

### Conventional Commits

- A specification for adding structure and meaning to commit messages.
- Provides a set of rules for creating an explicit commit history that is both human and machine-readable.
- Designed to work with SemVer (Semantic Versioning).
    - e.g. `fix` correlates with `PATCH`, `feat` with `MINOR`, and `BREAKING CHANGE` with `MAJOR` version changes.

```
<type>[optional scope]: <description>

[optional body]

[optional footer]
```

- Common types:
    - `feat` - A new feature
    - `fix` - A bug fix
    - `docs` - Documentation changes
    - `style` - Code style changes (formatting, semicolons, etc.)
    - `refactor` - Code refactoring
    - `perf` - Performance improvements
    - `test` - Adding or modifying tests
    - `build` - Changes to build process or tools
    - `ci` - Changes to CI configuration files and scripts
    - `chore` - Other changes that don't modify source or test files
    - `revert` - Reverts a previous commit

> [!quote]- Conventional Commits
> ![[Conventional Commits]]

> [!example]
>
> ```
> feat(auth): implement 2fa
>
> - Add email verification step
> - Create recovery code generation
> - Update account settings page with 2FA options
>
> Closes #12
> ```

### Versioning & Releases

- Create a tag
- Push tags to the remote
- Navigate to Releases
- Draft a new release
- Select a tag & fill in release notes
- Publish the release

## Miscellany

- `git reflog`
    - Helps track changes made to the tips of branches in a local git repo.
- `git bisect`
    - Used to identify the specific commit that introduced a bug in code.
    - Employs a binary search algorithm to efficiently narrow down the range of commits, to find the offending commit without having to check each one individually.

---

## Further

### Books 📚

- [Pro Git (Scott Chacon)](https://git-scm.com/book) ⭐

- [Beej's Guide to Git](https://beej.us/guide/bggit/) ⭐

### Resources 🧩

- [Git Me](https://www.gitme.live/)

### Videos 🎥

![How Git Works: Explained in 4 Minutes (YouTube)](https://www.youtube.com/watch?v=e9lnsKot_SQ)

![Git MERGE vs REBASE](https://www.youtube.com/watch?v=0chZFIZLR_0)
