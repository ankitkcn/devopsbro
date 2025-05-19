title: Git Repository Setup and File State Management


This article presents an integrated and deeply conceptual guide to Git repository setup, file tracking, internal mechanics like `HEAD`, and most importantly, how to compare different commits using `git diff`. The purpose is to build not only a practical command reference but also a solid mental model of what Git is doing internally.

---

## 1. Repository Initialization and Cloning

### 1.1 `git init`

```bash
git init
```

* Initializes a new Git repository by creating a `.git/` directory in the current folder.
* `.git/` includes:

  * `HEAD`: points to the current branch (e.g., `refs/heads/main`)
  * `objects/`: stores all data (files, directories, commits) as **content-addressed blobs**
  * `refs/`: contains references (branches and tags)
  * `index`: the staging area
  * `config`: local repository configuration

Optional:

```bash
git init --initial-branch=main
```

This ensures the default branch is `main`, not `master`.

---

### 1.2 `git clone`

```bash
git clone <url> [dir]
```

* Clones a remote repository into a local directory.
* Copies the entire `.git/` structure.
* Sets the remote URL as `origin` by default.
* To give a different remote name:

```bash
git clone -o upstream <url>
```

You now refer to `upstream` instead of `origin`.

---

## 2. Understanding Branch Names: `main` vs `master`

* Git historically used `master` as the default.
* Modern conventions use `main` to avoid problematic terminology.
* You can rename the current branch:

```bash
git branch -m master main
```

And make `main` the default for future repositories:

```bash
git config --global init.defaultBranch main
```

---

## 3. File Lifecycle in Git

Every file in a Git repository is in one of several **states**:

| State     | Description                                                     |
| --------- | --------------------------------------------------------------- |
| Untracked | File exists on disk but Git isn’t tracking it.                  |
| Tracked   | File is part of the repository (may be unmodified or changed).  |
| Modified  | File differs from last commit.                                  |
| Staged    | File is added to the index and will be part of the next commit. |
| Committed | File snapshot is recorded in Git history.                       |

---

## 4. The Index (Staging Area)

The **index** (a.k.a. staging area) is a binary file `.git/index` that stores:

* The next tree (snapshot) that will be committed.
* For each file:

  * Path
  * Mode (permissions)
  * Object SHA-1 (blob)
  * Timestamps

To stage changes:

```bash
git add file.txt        # stages the file
git add .               # stages all changes in the current directory
git add -u              # stages only modified and deleted tracked files
```

---

## 5. Commit Operation and the Role of `HEAD`

```bash
git commit -m "Message"
```

* Takes the current contents of the index, builds a **tree** object.
* Saves a **commit object** pointing to:

  * The tree
  * Parent commit(s)
  * Metadata (author, timestamp, message)
* Updates `HEAD` to point to this new commit.

### 5.1 `HEAD`

* A symbolic reference to the current commit through the current branch.
* If you're on `main`, `HEAD` → `refs/heads/main` → `<commit hash>`.

---

## 6. Comparing Changes Using `git diff`

### 6.1 Working directory vs Index (Staging area)

```bash
git diff
```

* Shows what has changed **but not yet staged**.
* Compares **working directory** to **index**.

### 6.2 Index vs Last Commit (`HEAD`)

```bash
git diff --cached
```

* Shows what is staged but not yet committed.
* Compares **index** to **HEAD**.

### 6.3 Full difference: Working directory vs Last Commit

```bash
git diff HEAD
```

* Shows **all differences** (staged and unstaged) from last commit.

---

## 7. Comparing Two Commits

This is where Git's object database model becomes powerful.

### 7.1 Syntax

```bash
git diff <commit1> <commit2>
```

* Compares the **tree** of `commit2` against `commit1`.
* Shows what you would need to apply to `commit1` to get `commit2`.

Example:

```bash
git diff HEAD~1 HEAD
```

* Compares the previous commit to the current commit.
* Shows what changed in the most recent commit.

```bash
git diff a1b2c3d f6e7g8h
```

* Compares any two commits by hash.

If you want to see file changes **in a specific file** between two commits:

```bash
git diff HEAD~3 HEAD -- path/to/file.txt
```

---

### 7.2 Other Useful Variants

#### Compare two branches

```bash
git diff feature-branch main
```

* Shows what is in `main` that is not in `feature-branch`.

#### Compare a commit to the working directory

```bash
git diff <commit> -- path/to/file
```

---

## 8. The Dot Notation: `..` and `...`

### 8.1 `A..B`

```bash
git log A..B
```

* Shows commits in `B` that are **not** in `A`.

This means: "What is in `B` that I haven’t already seen in `A`?"

### 8.2 `A...B` (Three-dot)

```bash
git log A...B
```

* Shows commits that are in **either A or B but not both**.
* Useful when examining divergence.

```bash
git diff A...B
```

* Diffs the **merge base** of A and B with `B`.

---

## 9. Viewing History and Commits

```bash
git log
```

Useful options:

* `--oneline`: compact form
* `--graph`: ASCII commit tree
* `--all`: includes all branches
* `--decorate`: adds refs like `HEAD`, `origin/main`

```bash
git log --oneline --graph --decorate --all
```

### Show specific commit

```bash
git show HEAD
```

* Displays the full patch and metadata of the latest commit.

```bash
git show HEAD~2
```

* Two commits before current.

---

## 10. Summary Table of Important Commands

| Purpose                             | Command                                      | Notes                   |
| ----------------------------------- | -------------------------------------------- | ----------------------- |
| Initialize repository               | `git init`                                   | Creates `.git/`         |
| Clone repository                    | `git clone <url>`                            | Pulls full history      |
| Track new file                      | `git add file.txt`                           | Moves to index          |
| Stage all changes                   | `git add .`                                  | Includes untracked      |
| Commit                              | `git commit -m "message"`                    | Creates new snapshot    |
| Status of working dir               | `git status`                                 | Shows tracked/untracked |
| Compare working dir vs index        | `git diff`                                   | Unstaged changes        |
| Compare index vs HEAD               | `git diff --cached`                          | Staged changes          |
| Compare working dir vs HEAD         | `git diff HEAD`                              | All changes             |
| Compare two commits                 | `git diff <commit1> <commit2>`               | Between any two         |
| Compare current and previous commit | `git diff HEAD~1 HEAD`                       | One-commit diff         |
| Show commit history                 | `git log`                                    | Full history            |
| Compact log                         | `git log --oneline -n 5`                     | Short log               |
| Graph log with refs                 | `git log --oneline --graph --decorate --all` | Tree-like history       |
| Show a commit                       | `git show HEAD`                              | Patch + metadata        |
| Compare branches                    | `git diff branchA branchB`                   | Diffs trees             |
| Unique commits in B                 | `git log A..B`                               | B minus A               |
| Symmetric difference                | `git log A...B`                              | In A or B but not both  |

---

This article provides a complete and rigorous understanding of Git's core operations from initialization and cloning to precise file comparison using `git diff`. Every command is mapped not only to its syntax but also to the internal concepts like **trees**, **blobs**, **HEAD**, and the **index**, which make Git a content-addressable, snapshot-based system rather than a change-based one.

If you want to follow this with an article on resetting, amending commits, or undoing things using `git reset`, `git restore`, and `git reflog`, let me know.
