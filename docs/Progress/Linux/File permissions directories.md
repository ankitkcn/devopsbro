**File Permissions for Directories in Linux**

In Linux, every file and directory is associated with three permission bits—read (r = 4), write (w = 2), and execute (x = 1)—for each of three classes (owner, group, others). While on regular files these bits govern content‐reading, content‐writing, and program execution, on directories they assume specialized roles:

* **Read (r = 4):** Permission to list the names of entries in the directory (e.g. via `ls` or `readdir`).
* **Write (w = 2):** Permission to create new entries or remove/rename existing ones within the directory.
* **Execute (x = 1):** Permission to “traverse” or search the directory—that is, to access inodes by name (e.g. `cd` into the directory, `stat` or open files residing beneath it).

Because many operations combine these bits, different bit‐patterns yield distinct behaviors:

---

### r--  (4)

A directory with **read-only** permission (numeric 4) allows a user to retrieve the raw list of filenames, but without execute permission it cannot be entered or used to open any file.

```bash
chmod 400 mydir
# mydir: dr--------  
```

* **List names:** Yes (if you invoke `ls mydir`, you’ll see filenames).
* **Enter or open:** No (e.g. `cd mydir` fails; `stat mydir/file` is denied).
  Thus, r-- is of very limited use on directories, since knowing filenames alone does not permit accessing them.

---

### rw-  (6)

With **read+write** (numeric 6) but **no execute**, one can modify the directory’s data structure but cannot traverse into it or list its contents:

```bash
chmod 600 mydir
# mydir: drw-------  
```

* **List names:** Although the read bit is set, without execute you cannot actually “search” the directory, so most tools refuse to list its contents.
* **Create/remove entries:** The write bit would permit creation and removal of entries *if* traversal were allowed; however, lacking execute, even unlink and rename typically fail.
  In practice, rw- on a directory is nearly unusable—it neither allows reliable listing nor safe modification of entries.

---

### r-x  (5)

The **read+execute** combination (numeric 5) is common for directories meant to be browsed and navigated but not altered:

```bash
chmod 500 mydir
# mydir: dr-x------  
```

* **List names:** Yes.
* **Traverse (cd/open):** Yes.
* **Modify entries:** No.
  This setting suits directories that should be visible and accessible (e.g. system‐wide libraries), while preventing any user from adding or deleting files.

---

### -wx  (3)

With **write+execute** (numeric 3) but **no read**, the directory cannot be listed but *can* be entered and have entries created or deleted—provided you know their names:

```bash
chmod 300 mydir
# mydir: d-wx------  
```

* **List names:** No.
* **Traverse:** Yes.
* **Create/remove entries:** Yes (so long as the name is specified).
  This pattern underpins “drop-box” directories (e.g. `maildrop` or a temporary upload folder) where users may place files without seeing what others have dropped.

---

### --x  (1)

An **execute-only** directory (numeric 1) allows traversal if you already know the exact name of an entry, but you cannot list or modify anything:

```bash
chmod 100 mydir
# mydir: d--x------  
```

* **List names:** No.
* **Traverse:** Yes.
* **Modify entries:** No.
  Use cases include highly restricted namespaces where a service must access known subpaths but users must not glean any directory structure.

---

### -w-  (2)

A **write-only** directory (numeric 2) without execute is generally nonfunctional in practice:

```bash
chmod 200 mydir
# mydir: d-w-------  
```

* **List names:** No.
* **Traverse:** No.
* **Modify entries:** Although write is set, lacking execute prevents any system calls (create, unlink) from succeeding.
  Consequently, -w- on a directory is effectively equivalent to no permissions (`---`).

---

Thus, by mixing the read/write/execute bits in directories, administrators can finely tailor access:

* **r-x (5)** lets users browse without altering.
* **-wx (3)** lets users deposit or remove files without peeking inside.
* **--x (1)** lets processes access known entries without exposing structure.

Knowing the numeric equivalents (r = 4, w = 2, x = 1) and the semantic roles of each bit in directory contexts enables precise control over who can list, enter, and modify filesystem namespaces.
