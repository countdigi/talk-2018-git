class: middle

.right[![HII Logo](images/hii-logo.png)]

# Git Version Control

- Kevin Counts, Systems/Software Engineer
  - Email: kevin.counts@hii.usf.edu
  - Github: https://github.com/countdigi

- Health Informatics Institute<br/>
  USF Health, Tampa, Florida
  - Web: http://www.hii.usf.edu
  - Github: https://github.com/USF-HII

---

## What is Git?

.right[![Linus Torvalds](images/Linus_Torvalds.jpeg)]

- Distributed Version Control System (DVCS) created by Linus Torvalds in 2005

- Created as open-source alternative to BitKeeper for managing Linux Kernel project

- Emphasis on speed, data integrity, and distributed workflows

- Very popular for open-source software projects

---

## Distributed Version Control

- Each developer has a local copy of the entire codebase and complete history in their `.git/` directory (repository)

- Operations such as commits, history, revert, branch, merge, diff are fast and isolated because they are local

- Isolation gives you more freedom to experiment and organize ideas and defer modifying upstream

- Each developer's local copy is a quasi-backup (from last time other developer's changes were "pulled")

---

## 3 Tier Architecture

.right[![3 Tier Architecture](images/3-tier.png)]

- **Working Directory** - The directory containing a versioned tree of files tracked in the repository.
  <br/><br/>
  Generally the top-level directory of the project and all its sub-directories and files except for the `.git`
  folder which is the embedded repository.

- **Staging Area (Index)** - Stages changes made in the Working Tree to be committed to the repository

- **Repository** - The `.git` directory. Contains all committed versions of the Working Tree, metadata, history, etc.

```
project_folder/ <--- Working Directory
  file1.txt
  file2.txt
  .git/         <---- Repository
    index       <---- Staging Area (Index)
    HEAD        <---- Points to tip of current branch commit ID
    ... etc.
```

???

- Working Directory - Master branch, HEAD

- Encourages clear, cohesive change history by allowing a subset of changed files (or even changed "chunks" in a file)
  to be grouped together in a commit.


---

## Working Directory File Status

A file in the working directory may be in one of the following states:

- **Untracked** - File was never staged or committed (not in repository)

- **Tracked** - File commited and not staged (**Not shown in status**)

- **Staged** - File staged to be included in next commit

- **Modified** - File changed but change is not staged

Run `git status` to view what state a file is in:

```
$ git status --short

|------ Column 1: Staging Area (Index)
 |------------------------------------- Column 2: Working Directory

??   file1.txt  # Untracked
A    file2.txt  # Staged new file
M    file3.txt  # Staged new change in tracked file
 M   file4.txt  # Modified (changed file but not staged)
 D   file5.txt  # Deleted from Working Directory but deletion not staged
D    file6.txt  # Deleted from Working Directory and deletion staged
```


---

## Staging a Change

Create a file and show status:

```
$ echo "Hi Mom" > README.md

$ git status --short
?? foo
```

Add file to the index and show status:

```
$ git add README.md

$ git status --short
A  foo
```

---

## Committing a Change

Commit the staged change:

```
$ git commit -m 'Add README.md'
[master 4d3fdc8] Add README.md
1 files changed, 1 insertions(+), 0 deletions(-)
create mode 100644 README.md
```

Show status:

```
$ git status --short
<empty>
```

---

## Commit Objects

.right[![Linus Torvalds](images/commit-object.png)]

- A commit object (commit) represents a version of all files tracked in the repository at the time the commit was created.

- Commits know their parent(s) and this way capture the version history of the repository.

- In Git, a commit is very similar to a filesystem snapshot.

- A Git commit doesn't directly track files. It tracks snapshots of the entire file tree.

- A commit is identified by a Commit ID (SHA-1 Hash).

???

- What is special about the commit object in the diagram?

- The next commit would add a `parent` field with the Commit ID of this commit

---

## SHA-1 Hash

- SHA-1 is a secure hash algorithm published by NIST producing 160-bit hash value known as a "message digest"

- Each object stored in Git is run through SHA-1 algorithm and an object ID is generated

- Even varying one character in an object creates a totally different message digest, for example:
  ```
  echo 'hi mom' | sha1sum
  9f74809a2ee7607b16fcc70d9399a4de9725a727 -

  echo 'hi mom!' | sha1sum
  af543966f22c4c6d40c3060422e88b1b50bad42d  -

  ```

- If 2 objects generate the same message digest, this is called a hash collision. For SHA-1 it is astronomically improbable.

<!--


- http://ndpsoftware.com/git-cheatsheet.html#loc=stash; # cool visual exploration of git

- ORIG_HEAD

- https://git-scm.com/book/en/v2

---

## Distributed versus Centralized

```bash
#!/usr/bin/env bash

for w in ${words}; do
  echo ${w}
done
```

---

## Initialize repository

    $ mkdir ~/src/project42

    $ cd ~/src/project42

    $ touch README.md; git init

    $ tree
    .
    |----.git/
    |    |-- HEAD
    |    |-- branches/
    |    |-- config
    |    |-- objects/
    |    |   |-- info/
    |    |   |-- pack/
    |    |-- refs/
    |        |-- heads/
    |        |-- tags/
    |-- README.md





# Git

## References

- Git Website/Book:  https://git-scm.com/
- Wikpedia: https://en.wikipedia.org/wiki/Git_(software)

## Get Help

- `git help` - Show common sub-commands
- `git help -a` - Show all sub-commands
- `git help -g` - Show guides

## Configure your identity

- `git config --global user.name "Mary Smith"`
- `git config --global user.email mary.smith@hii.usf.edu`


## Misc

- https://github.com/git-tips/tips

Git is a distributed revision control system with an emphasis on speed, data integrity,
and support for distributed, non-linear workflows created by Linus Torvalds in 2005 for development of the Linux kernel.

- Every Git working directory is a full-flegded repository with complete history and full version-tracking capabilities,
  indepededent of network access or a central server.

- Key feature of Git is that it maintains cryptographic authentication of history since the ID of a particular version
  (commit) depends upon the complete history leading up to that commit.

- Git has two data structures:
  1. Mutable index (aka "stage" or "cache") that tracks changes in working directory to be committed.
     The index serves as connection point between the object database and the working tree.

  2. Immutable append-only object database.


The object database contains four types of objects:
  - A blob (binary large object) is the content of a file. Blobs have no file name, time stamps, or other metadata.
  - A tree object is the equivalent of a directory.
    It contains a list of file names, each with some type bits and the name of a blob or tree object that is that file,
    symbolic link, or directory's contents. This object describes a snapshot of the source tree.
  - A commit object links tree objects together into a history.
    It contains the name of a tree object (of the top-level source directory), a time stamp, a log message,
    and the names of zero or more parent commit objects.
  - A tag object is a container that contains reference to another object and can hold additional meta-data
    related to another object.  Most commonly, it is used to store a digital signature of a commit
    object corresponding to a particular release of the data being tracked by Git.


The index serves as connection point between the object database and the working tree.

Each object is identified by a SHA-1 hash of its contents. Git computes the hash, and uses this value for the object's name. The
object is put into a directory matching the first two characters of its hash. The rest of the hash is used as the file name for that
object.

Git stores each revision of a file as a unique blob. The relationships between the blobs can be found through examining the tree and
commit objects. Newly added objects are stored in their entirety using zlib compression. This can consume a large amount of disk
space quickly, so objects can be combined into packs, which use delta compression to save space, storing blobs as their changes
relative to other blobs.

A Git repository contains the history of a collection of files starting from a certain directory. The process of copying an existing Git repository via the Git tooling is called cloning. After cloning a repository the user has the complete repository with its history on his local machine. Of course, Git also supports the creation of new repositories.

A local repository provides at least one collection of files which originate from a certain version of the repository. This collection of files is called the working tree. It corresponds to a checkout of one version of the repository with potential changes done by the user.

---

The user can change the files in the working tree by modifying existing files and by creating and removing files.

A file in the working tree of a Git repository can have different states. These states are the following:

untracked: the file is not tracked by the Git repository. This means that the file never staged nor committed.

tracked: committed and not staged

staged: staged to be included in the next commit

dirty / modified: the file has changed but the change is not staged

After doing changes in the working tree, the user can add these changes to the Git repository or revert these changes.

HEAD is a symbolic reference most often pointing to the currently checked out branch.

---

A repository contains the history, the different versions over time and all different branches and tags. In Git each copy of the repository is a complete repository. If the repository is not a bare repository, it allows you to checkout revisions into your working tree and to capture changes by creating new commits. Bare repositories are only changed by transporting changes from other repositories.

This description uses the term repository to talk about a non-bare repository. If it talks about a bare repository, this is explicitly mentioned.
Revision

The working tree contains the set of working files for the repository. You can modify the content and commit the changes as new commits to the repository.

---



-->
