title: Undoing Things

# **Undoing Things in Git: A Deep Dive into `amend`, `revert`, `restore`, `reset`, `clean`, and `reflog`**

Git is a content-addressable version control system built on immutable snapshots. Unlike traditional version control systems that track file changes as deltas, Git takes a fundamentally different approach: it captures entire snapshots of your file tree and stores them as **blobs**, **trees**, and **commits**, which are interconnected through a directed acyclic graph (DAG). This architectural decision empowers Git with remarkable flexibility in revising history, recovering from mistakes, and restructuring your development timeline.

However, with this power comes complexity: when developers ask “How do I undo something in Git?”, there is no single answer. The “right” tool depends on the layer you’re operating on—working directory, index, or commit history—and on the scope: Are you correcting a typo in the last commit? Removing untracked files? Rewinding the branch tip? Or recovering a lost commit?

This guide explains, with complete conceptual detail and practical use cases, the six core Git tools for undoing things:

* `git commit --amend`
* `git revert`
* `git restore`
* `git reset`
* `git clean`
* `git reflog`

Each section focuses on **what it changes**, **why it exists**, **how it works internally**, **when to use it**, and **common mistakes to avoid**.

---

## 1. `git commit --amend`: Refining the Most Recent Commit

### Purpose

To replace the current tip of the branch with a modified version, optionally changing the content, message, or metadata of the most recent commit.

### Layer Affected

* **Repository** (rewrites the tip commit)

### How It Works Internally

* When you run `git commit --amend`, Git:

  1. Reads the current `HEAD` commit and its parent.
  2. Prepares a new commit object, possibly with a new tree (if index differs).
  3. Reuses the same parent as the previous tip.
  4. Updates the branch ref to point to the new commit.
* The old commit becomes **dangling** (i.e., unreachable from a ref) but remains in `.git/objects`.

### Use Cases

* You committed prematurely and forgot to add a file.
* You need to correct a typo or clarify the commit message.
* You staged more changes and want them included in the same commit as before.

### Command Examples

```bash
# Add a missing file and amend the last commit
git add forgotten_file.txt
git commit --amend

# Change the commit message only
git commit --amend -m "Fix typo and update README"

# Keep message but update timestamp and author
git commit --amend --no-edit --reset-author
```

### Cautions

* **Avoid amending commits that are already pushed** to a shared branch unless you force-push and warn collaborators.
* Amended commits have **new hashes**; the old versions are still present until garbage collected.

---

## 2. `git revert`: Creating an Inverse Commit

### Purpose

To **undo the effects** of a previous commit by creating a new commit that reverses its changes. Unlike `reset` or `amend`, `revert` **preserves history**.

### Layer Affected

* **Repository** (adds a new commit to undo previous one)

### How It Works Internally

* Git calculates the patch introduced by the target commit and applies the **inverse** of that patch to the current tree.
* A **new commit** is created with that inverse diff, referencing the current HEAD as its parent.

### Use Cases

* You want to undo a bad commit on a public branch **without rewriting history**.
* You need to back out of a feature or hotfix that introduced regressions.
* You want to make the undo visible and traceable in history.

### Command Examples

```bash
# Revert the most recent commit
git revert HEAD

# Revert a specific commit by SHA
git revert abc1234

# Revert a merge commit (you must specify the parent)
git revert -m 1 d3f4567
```

### Cautions

* **Conflicts may occur**, especially if subsequent commits modified the same lines.
* You can “undo a revert” by reverting the revert commit (effectively reapplying the original patch).

---

## 3. `git restore`: Replacing File Content from Index or Commit

### Purpose

To **restore the content of files** from either the index (staging area) or a previous commit to the working directory or index.

### Layers Affected

* **Working Directory**
* **Index** (optional)

### How It Works Internally

* `git restore` copies the file content from the specified source (default is index or `HEAD`) into the working directory and/or index.
* Does **not create a new commit** or affect `HEAD`.

### Use Cases

* Discarding uncommitted changes to a file.
* Unstaging a file without modifying its content.
* Reverting a file to an earlier version from history.

### Command Examples

```bash
# Discard changes in working directory (from index)
git restore my_file.txt

# Unstage a file (index ← HEAD)
git restore --staged my_file.txt

# Restore from a previous commit
git restore --source=HEAD~2 --staged --worktree my_file.txt
```

### Cautions

* Files restored from the index or earlier commits **overwrite** local modifications—ensure you don’t lose important unsaved work.
* `git restore` is safe but **not reversible** unless you have a stash or prior commit.

---

## 4. `git reset`: Moving HEAD and Rewriting Index/Worktree

### Purpose

To move the current branch’s tip (and `HEAD`) to another commit, optionally resetting the index and working directory to match it.

### Layers Affected

* **Repository**
* **Index**
* **Working Directory** (depending on mode)

### How It Works Internally

* Git moves `HEAD` and the current branch ref to a different commit.
* Based on the mode:

  * `--soft`: leaves index and working directory unchanged.
  * `--mixed`: resets the index to match new HEAD, but leaves working directory.
  * `--hard`: resets everything, including working files, to new HEAD.

### Use Cases

* You want to rework recent commits (soft).
* You want to unstage all changes (mixed).
* You want to discard all local work and roll back to a clean state (hard).
* You need to “uncommit” without losing content.

### Command Examples

```bash
# Undo last commit, keep changes staged
git reset --soft HEAD~1

# Undo commit and unstage changes
git reset --mixed HEAD~1

# Remove last two commits and all associated changes
git reset --hard HEAD~2

# Unstage a specific file
git reset HEAD file.txt
```

### Cautions

* `--hard` permanently discards working directory changes; it’s **destructive**.
* After reset, previous commits become dangling unless referenced or recovered via `reflog`.

---

## 5. `git clean`: Removing Untracked Files

### Purpose

To **delete untracked files and directories** from the working directory, including optionally ignored files.

### Layer Affected

* **Working Directory** (only untracked and ignored content)

### How It Works Internally

* Git scans the working directory for files that:

  * Are not in the index.
  * Are not part of any commit.
* It then deletes them according to the flags.

### Use Cases

* Cleaning up build artifacts or editor-generated files.
* Getting back to a "pristine" working tree.
* Removing ignored files like logs or `.DS_Store`.

### Command Examples

```bash
# Preview deletion
git clean -nfd

# Delete untracked files and directories
git clean -fd

# Delete ignored files too
git clean -fdx
```

### Cautions

* **Destructive and irreversible**: Git cannot recover deleted untracked files.
* Always run `git clean -n` first to preview.

---

## 6. `git reflog`: Recovering from Mistakes

### Purpose

To **track all changes to HEAD and branch refs**, even if commits are no longer reachable from any branch or tag.

### Layer Affected

* **Ref History** (tracking of branch pointer movements)

### How It Works Internally

* Git records every change to `HEAD` (e.g., commit, reset, checkout, merge, amend) in `.git/logs/HEAD` and `.git/logs/refs/heads/<branch>`.

### Use Cases

* Recovering commits lost after a reset, rebase, or amend.
* Locating a previously checked-out commit.
* Finding a working state before a mistake.

### Command Examples

```bash
# Show movement history of HEAD
git reflog

# Reset to a previous position
git reset --hard HEAD@{3}

# Create a branch from a reflog commit
git checkout -b recovered HEAD@{4}
```

### Cautions

* Reflog entries expire after 90 days (by default) or after garbage collection (`git gc`).
* If you perform destructive operations (`reset --hard`, `clean`, `checkout`), check `reflog` immediately if anything is lost.

---

## Summary Table: What Each Command Undoes

| Command          | Undo Scope                               | Preserves History? | Affects HEAD? | Destructive?      | Reversible via Reflog? |
| ---------------- | ---------------------------------------- | ------------------ | ------------- | ----------------- | ---------------------- |
| `commit --amend` | Last commit content/message              | ✘ (rewrites)       | ✔             | No                | ✔                      |
| `revert`         | Any past commit                          | ✔                  | ✔             | No                | N/A (non-destructive)  |
| `restore`        | File content in index or worktree        | ✔                  | ✘             | Possibly          | No (unless committed)  |
| `reset`          | Branch tip and optionally index/worktree | ✘ (rewrites)       | ✔             | Yes (in `--hard`) | ✔                      |
| `clean`          | Untracked and ignored files              | N/A                | ✘             | Yes               | ✘                      |
| `reflog`         | Restores any lost `HEAD` or commit       | N/A                | ✘ (read-only) | No                | ✔                      |

