# Git & Version Control — Engineering Reference

Structured notes for interviews and day-to-day work. Concise, example-driven.

---

## 1. Basic Terminology

| Term | Meaning |
|------|--------|
| **Repository (repo)** | Project folder + full history of changes (commits). |
| **Commit** | Snapshot of the project at a point in time; has a unique hash (e.g. `a1b2c3d`). |
| **Working tree / working directory** | Current files you see and edit on disk. |
| **Staging area (index)** | Place where you add changes before committing. "Staged" = ready to commit. |
| **Branch** | Movable pointer to a commit; lets you work on lines of work in parallel. |
| **HEAD** | Pointer to the current branch (and thus the current commit). |
| **Remote** | Another copy of the repo (e.g. on GitHub); usually named `origin`. |
| **Clone** | Copy an entire repo (including history) from a remote. |
| **Fork** | Your own copy of someone else’s repo (GitHub concept). |
| **Pull** | Fetch + merge from remote into current branch. |
| **Push** | Send your commits to a remote branch. |
| **Upstream** | Remote branch that your local branch tracks (e.g. `origin/main`). Enables plain `git push` / `git pull`. |

**Real-world:** Think of commits as "save points," staging as "select what to save," and branches as "parallel storylines" (e.g. `main` = production, `feature/login` = new login work).

---

## 2. Why Version Control Helps

- **History:** See who changed what and when; recover old versions.
- **Collaboration:** Multiple people work on same codebase without overwriting each other.
- **Experimentation:** Try ideas on branches; merge only if they work.
- **Rollback:** Revert bad releases or undo mistakes.
- **Code review:** Pull requests and diffs before merging.
- **CI/CD:** Pipelines run on commits/branches (build, test, deploy).

---

## 3. Git Workflow (High Level)

<img width="1196" height="849" alt="image" src="https://github.com/user-attachments/assets/25b393fe-47b2-414a-a11c-cb378b971123" />


1. Edit files (working tree).
2. `git add` → stage changes (index).
3. `git commit` → create snapshot (new commit in history).
4. Optionally `git push` to remote or `git pull` to get others’ work.

---

## 4. Git States (File Lifecycle)

| State | Meaning | Command to get there |
|-------|--------|----------------------|
| **Untracked** | File exists but Git doesn’t track it. | New file created. |
| **Tracked / Unmodified** | In repo, no changes since last commit. | After commit. |
| **Modified** | Changed on disk, not staged. | Edit a tracked file. |
| **Staged** | Changes added to index, ready to commit. | `git add`. |


---

## 5. The `.git` Folder

Hidden folder in repo root. Contains:

| Path / concept | Purpose |
|----------------|--------|
| `HEAD` | Points to current branch (e.g. `ref: refs/heads/main`). |
| `config` | Repo-level config (remote URLs, branch settings). |
| `refs/heads/<branch>` | Commit hash the branch points to. |
| `refs/tags/<tag>` | Commit (or tag object) the tag points to. |
| `objects/` | All Git objects (commits, trees, blobs, tags). |
| `index` | Staging area (binary). |
| `logs/` | Reflog and branch movement history. |

**Don’t edit these by hand** unless you know what you’re doing. Use Git commands.

---

## 6. Git Objects (Four Types)

All stored under `.git/objects/` by hash (first 2 chars = folder, rest = filename).

| Object | Stores | Example |
|--------|--------|--------|
| **Blob** | File content (no name, no structure). | Contents of `main.py`. |
| **Tree** | Directory listing: names + blob/tree hashes. | One tree = one directory. |
| **Commit** | Author, message, timestamp, parent commit(s), top-level tree. | One snapshot. |
| **Tag** | Points to a commit (and optional message, tagger). | Annotated tag. |

**Chain:** Commit → tree → trees/blobs. Blobs are shared: same file content = same blob (dedup).

```
Commit (abc123)
  └── Tree (root)
        ├── blob (main.py)
        ├── blob (readme.md)
        └── Tree (src/)
              └── blob (app.py)
```

**Inspect:** `git cat-file -p <hash>` (e.g. `git cat-file -p HEAD`).

---

## 7. Branches

- Branch = pointer to a commit. Creating a branch just creates a new pointer.
- `HEAD` points to the current branch; that branch moves on each new commit.

```bash
# List branches
git branch

# Create branch (doesn’t switch)
git branch feature/auth

# Switch branch
git switch feature/auth
# or
git checkout feature/auth

# Create and switch in one step
git switch -c feature/auth
git checkout -b feature/auth

# Delete branch (must not be on it)
git branch -d feature/auth   # safe delete
git branch -D feature/auth   # force delete

# Rename current branch
git branch -m new-name
```

**Real-world:** `main`/`master` = production; `develop` = integration; `feature/xxx`, `bugfix/xxx` = short-lived branches.

---

## 8. Merging Strategies

| Strategy | Command | Result | When to use |
|----------|---------|--------|-------------|
| **Fast-forward** | `git merge other` | Branch pointer moves to tip of `other`; no merge commit. | Linear history when no new commits on current branch. |
| **Three-way merge** | `git merge other` | New merge commit with two parents. | Both branches have new commits. |
| **Squash merge** | `git merge --squash other` then commit | One new commit with all changes from `other`. | Clean linear history (e.g. one PR = one commit). |
| **Rebase** | `git rebase other` | Replay your commits on top of `other`. | Linear history; avoid on shared branches. |

<img width="474" height="246" alt="image" src="https://github.com/user-attachments/assets/3857c64e-bd4b-4240-988e-cda9c3bda870" />


**Fast-forward example:**

```
Before:  main → A → B
              feature → C → D

After (fast-forward merge of feature into main):
         main → A → B → C → D
```

**Three-way merge:** New commit M with parents B and D.

---

## 9. Merge Conflicts

When the same lines (or same area) were changed in both branches, Git can’t decide automatically.

**Steps:**

1. `git merge other` (or pull) → "Conflict" message.
2. Open conflicted files; look for:

   ```
   <<<<<<< HEAD
   your version
   =======
   their version
   >>>>>>> branch-name
   ```

3. Edit to the correct result and remove the markers.
4. `git add <file>` for each resolved file.
5. `git commit` (merge commit) or `git merge --continue` if in progress.

**Abort merge:** `git merge --abort`

**Accept one side for whole file** (when you want to keep one version entirely):
```bash
git checkout --ours <file>    # keep current branch's version
git checkout --theirs <file>  # keep incoming branch's version
git add <file>
```

**Useful:** `git diff` (unmerged), `git status` (list conflicted files).

---

## 10. Git History

| Concept | Command | Use |
|---------|---------|-----|
| **Log (one line per commit)** | `git log --oneline` | Quick history. |
| **Log with graph** | `git log --oneline --graph --all` | Branches and merges. |
| **Log of a file** | `git log -- path/to/file` | Who changed a file. |
| **Patch per commit** | `git log -p` | Diffs in log. |
| **Reflog** | `git reflog` | All HEAD/branch moves (recover "lost" commits). |

```bash
git log -3 --oneline           # Last 3 commits
git log --author="name"        # By author
git log --since="2024-01-01"   # By date
```

---

## 11. Undoing Changes in Git

### 11.1 Unstaging (keep changes on disk)

```bash
git restore --staged <file>   # unstage one file
git restore --staged .        # unstage all
# older:
git reset HEAD <file>
```

### 11.2 Discard changes in working tree (danger: loses edits)

```bash
git restore <file>
# older:
git checkout -- <file>
```

### 11.3 Reset (move branch pointer; different “strength”)

| Type | Command | Working tree | Staging | Commit history |
|------|---------|--------------|---------|----------------|
| **Soft** | `git reset --soft <commit>` | unchanged | staged | moves back; changes become staged |
| **Mixed** (default) | `git reset --mixed <commit>` | unchanged | cleared | moves back; changes unstaged |
| **Hard** | `git reset --hard <commit>` | **matches commit** | cleared | moves back; **discards** local changes |

```bash
git reset --soft HEAD~1   # Undo last commit, keep changes staged
git reset HEAD~1          # Undo last commit, keep changes unstaged
git reset --hard HEAD~1   # Undo last commit and discard changes (destructive)
```

**Real-world:** Use `--soft` to fix last commit message or add forgotten files. Use `--hard` only when you’re sure you don’t need current work.

### 11.4 Revert (safe undo with new commit)

Creates a new commit that reverses a given commit. Good for shared branches.

```bash
git revert <commit-hash>
git revert HEAD   # revert latest commit
```

**Reset vs revert:** Reset moves the branch; revert adds a new “undo” commit. Prefer revert on `main`/shared branches.

### 11.5 Reverting a merge commit

Merge commits have two parents. Tell Git which parent is "mainline" (usually `main`):

```bash
git revert -m 1 <merge-commit-hash>   # -m 1 = keep first parent's history
```

Creates a new commit that undoes the merge. Use when a merged feature branch caused issues.

---

## 12. Remote Repo

| Command | Purpose |
|---------|--------|
| `git remote -v` | List remotes and URLs. |
| `git remote add <name> <url>` | Add remote (e.g. `origin`). |
| `git remote remove <name>` | Remove remote. |
| `git remote set-url <name> <url>` | Change URL. |
| `git fetch <remote>` | Download commits/branches; no merge. |
| `git pull [remote] [branch]` | Fetch + merge (default: `origin` + current branch). |
| `git push [remote] [branch]` | Upload commits. |
| `git push -u origin main` | Push and set upstream (then `git push`/`git pull` alone work). |

**Clone:** `git clone <url> [folder]` — creates folder, initializes repo, sets `origin`, checks out default branch.

**Upstream / tracking:** After first push with `-u`, your branch tracks the remote branch. Then `git status` shows "ahead/behind", and `git push` / `git pull` work without extra args. Set later: `git branch --set-upstream-to=origin/<branch>`.

**Pull = fetch + merge.** To avoid merge commits when you have local commits: `git pull --rebase` (fetches, then rebases your commits on top of remote). Many teams use this by default: `git config pull.rebase true`.

**Push rejected (diverged):** Remote has commits you don't. Either `git pull` (or `git pull --rebase`) then push, or—only on your own feature branch after rebase—`git push --force-with-lease` (safer than `--force`: fails if someone else pushed).

---

## 13. GitHub Workflow (Typical)

1. Clone or fork → local repo.
2. Create branch: `git switch -c feature/xyz`.
3. Edit, `git add`, `git commit`.
4. Push: `git push -u origin feature/xyz`.
5. Open Pull Request (PR) on GitHub: `feature/xyz` → `main`.
6. Review, fix (push more commits to same branch).
7. Merge PR (merge / squash / rebase on GitHub).
8. Delete branch (optional); pull `main` locally: `git pull origin main`.

**PR best practices:** Keep PRs small and focused; add description and link issue (e.g. "Fixes #123"); request reviewers; address feedback with new commits (or amend and force-push if you prefer a clean history and own the branch).

---

## 14. Two Main Collaboration Models

| Model | Flow | Use case |
|-------|------|----------|
| **Centralized** | Single remote; everyone pushes/pulls to same repo and branches. | Team repo, company codebase. |
| **Fork + PR** | Contributors fork repo, push to their fork, open PR into upstream. | Open source; no write access to main repo. |

---

## 15. Rebase (Before / After)

**Before rebase:**

```
main:     A → B → C
feature:  A → B → D → E
```

**After `git switch feature` then `git rebase main`:**

```
main:     A → B → C
feature:  A → B → C → D' → E'
```

Your commits D, E are replayed on top of C (new hashes D', E').

**Interactive rebase (edit/squash/reorder):**

```bash
git rebase -i HEAD~3   # last 3 commits
```

In editor: pick, reword, edit, squash, fixup, drop. Save and close.

**Golden rule:** Don’t rebase commits that are already pushed and shared. Rebase local or feature branches only.

**During rebase (conflicts):** Fix conflicts, `git add`, then `git rebase --continue`. To drop the conflicting commit: `git rebase --skip`. To cancel: `git rebase --abort`.

---

## 16. Squash

Combine several commits into one.

**Option A — merge with squash (e.g. on GitHub):**  
Merge PR with "Squash and merge" → one commit on `main`.

**Option B — interactive rebase:**

```bash
git rebase -i HEAD~4
# Change "pick" to "squash" (or "s") for commits you want to fold into the first
```

Result: one commit with combined message (you can edit).

---

## 17. Tags and Releases

| Type | Command | Use |
|------|---------|-----|
| **Lightweight** | `git tag v1.0.0` | Pointer to a commit; no extra metadata. |
| **Annotated** | `git tag -a v1.0.0 -m "Release 1.0"` | Tag object with message, tagger, date; preferred for releases. |

```bash
git tag                    # list tags
git tag -l "v1.*"          # pattern
git show v1.0.0            # tag + commit info
git push origin v1.0.0     # push one tag
git push origin --tags     # push all tags
```

**Releases (GitHub):** Create tag (or use existing) → GitHub "Releases" → attach binaries and release notes.

---

## 18. Git Practices

- **Commit often:** Small, logical commits; easier to review and revert.
- **Good messages:** First line &lt;50 chars, imperative: "Add login validation" not "Added login validation."
- **Branch naming:** `feature/short-name`, `bugfix/issue-123`, `hotfix/critical-fix`.
- **Don’t commit secrets:** Use `.gitignore`; use env vars or secret managers.
- **Pull before push:** `git pull --rebase` or `git pull` then fix conflicts before pushing.
- **Revert on shared branches:** Prefer `git revert` over `git reset --hard` on `main`.
- **Protected branches:** Use branch protection (e.g. require PR, CI) for `main`.
- **Force push:** Use only on your own feature branches (e.g. after rebase). Prefer `git push --force-with-lease` over `--force` (refuses if remote changed).
- **Partial stage:** Use `git add -p` to stage only some changes in a file (hunk by hunk); keeps commits focused.

---

## 19. Repo and Setup Commands

```bash
# New repo
git init
git init <directory>

# Clone
git clone <url>
git clone <url> <folder>
git clone --depth 1 <url>   # shallow (latest commit only)

# Config (repo-level: .git/config)
git config user.name "Your Name"
git config user.email "you@example.com"

# Config (global: ~/.gitconfig)
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global core.editor "code --wait"

# List config
git config --list
git config user.name

# Ignore — edit .gitignore (glob patterns)
git check-ignore -v <file>   # debug why file is ignored
# .gitignore patterns: * (anywhere), **/ (any depth), ! (negate), / (repo root only), # comment
# Examples: *.log, build/, /env, !important.log
```

---

## 20. Common Daily Workflow Commands

```bash
# Start of day / before feature work
git pull origin main
git switch -c feature/my-feature

# While working
git status
git add <file> | git add -p | git add .
git commit -m "Add feature X"
git diff
git diff --staged

# Push and open PR
git push -u origin feature/my-feature

# After PR merged
git switch main
git pull origin main
git branch -d feature/my-feature

# Quick fixes
git restore <file>              # discard unstaged changes
git restore --staged <file>     # unstage
git reset --soft HEAD~1         # undo last commit, keep changes
git revert HEAD                 # undo last commit with new commit
git reflog                      # find lost commits
```

---

## 21. Cherry-pick, Bisect, Detached HEAD

**Cherry-pick** — Apply specific commit(s) from another branch (e.g. hotfix to main, then to develop):

```bash
git cherry-pick <commit-hash>
git cherry-pick <hash1> <hash2>
git cherry-pick --abort   # if conflicts and you want to cancel
```

**Bisect** — Find the commit that introduced a bug (binary search over history):

```bash
git bisect start
git bisect bad              # current commit is bad
git bisect good <commit>    # this commit was good
# Git checks out a commit; you test and run:
git bisect good   # or  git bisect bad
# Repeat until it identifies the culprit. Then:
git bisect reset
```

**Detached HEAD** — When you checkout a tag or a specific commit (e.g. `git checkout v1.0`), you're not on a branch. Changes you commit are orphaned if you switch away. To keep them: `git switch -c new-branch`. To just look around: `git switch main` (or any branch) to leave.

---

## 22. Extra: Useful One-Liners

```bash
# Stash (save work without committing)
git stash
git stash push -m "WIP: login form"
git stash list
git stash pop
git stash apply stash@{0}

# Compare branches
git diff main..feature

# Who changed a line (blame)
git blame <file>
git blame -L 10,20 <file>   # lines 10–20

# Clean untracked files
git clean -n   # dry run
git clean -fd  # remove untracked files and dirs

# Submodules (reference)
git submodule add <url> <path>
git submodule update --init --recursive
```

**Partial staging (patch):** `git add -p <file>` — stage hunks interactively (y/n/split/etc.). Essential for clean, logical commits.

---

## 23. When Things Go Wrong (Recovery)

| Situation | What to do |
|-----------|------------|
| Committed to `main` by mistake | `git branch feature/quick` (saves commits) → `git reset --hard origin/main` (reset main to remote) → `git switch feature/quick`. Now work is on branch; push as PR. |
| Lost commit after reset | `git reflog` → find commit hash → `git switch -c recovery <hash>` or `git cherry-pick <hash>`. |
| Merge conflict, want to abort | `git merge --abort` or `git rebase --abort`. |
| Need to undo a pushed commit (shared branch) | `git revert <commit>` then push. Don't force-push on shared branches. |
| Force-pushed over my work (my branch only) | If you have the old hash from reflog: `git reset --hard <old-hash>` then coordinate with team; or recreate work from backup. |

**Parent notation:** `HEAD~1` = first parent (previous commit); `HEAD^2` = second parent (e.g. merged branch). Useful for `git log`, `git revert -m`, and reading history.

---

## 24. Cheat Sheet (Quick Lookup)

| Task | Command |
|------|--------|
| Status | `git status` |
| Stage all | `git add .` |
| Commit | `git commit -m "msg"` |
| Push | `git push` |
| Pull | `git pull` |
| New branch | `git switch -c branch` |
| Switch branch | `git switch branch` |
| Merge | `git merge other` |
| Abort merge | `git merge --abort` |
| Undo last commit (keep changes) | `git reset --soft HEAD~1` |
| Undo last commit (new commit) | `git revert HEAD` |
| View log | `git log --oneline --graph` |
| Recover | `git reflog` |
| Force push (own branch) | `git push --force-with-lease` |
| Revert merge | `git revert -m 1 <merge-commit>` |
| Cherry-pick | `git cherry-pick <hash>` |

---

## 25. Optional: Line Endings, Signing, Large Files

- **Line endings (Windows/Mac/Linux):** `git config core.autocrlf true` (Windows: checkout LF, commit CRLF) or `input` (commit as LF). Or use `.gitattributes`: `* text=auto`. Avoids noisy diffs and CI failures.
- **Signed commits:** `git commit -S -m "msg"` (requires GPG key). Verify: `git log --show-signature`. Used in high-compliance or open-source projects.
- **Large files:** Don't commit big binaries; use [Git LFS](https://git-lfs.com/) or store artifacts elsewhere. GitHub rejects files > 100MB.
