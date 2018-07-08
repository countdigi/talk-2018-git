class: top

## Version Control with Git

.right[![Git Logo](images/git-logo.jpg)]

<br/>
Kevin Counts<br/>
Bioinformatics Engineering<br/><br/>

Health Informatics Institute<br/>
University of South Florida<br/>
<https://www.hii.usf.edu/>

---

## Version Control

- A *Version Control System* records changes to a set of files over time so that you can recall specific versions later

- It records a base version of each file and tracks changes in series of revisions

- Users choose what makes a new file revision by periodically creating a *Commit*, a point-in-time snapshot of the files

- These commits along with the file revisions are stored in a *Repository*

- The comits and revisions in the *Repository* form a *Revision History*

- The changes from the *Revision History* can later be rewound and played back against the base versions
  to create different point-in-time representations of the files

- A VCS is designed to work with text-based files - not binaries such as JPEG Images, Word Documents, Excel Documents

- Binary files can be tracked but not all operations are supported (no human-readable diffs, no merging of multiple commits, etc.)

---

## Revision History

- As files are changed, those changes are saved by the VCS in a series of commits/revisions
 which are stored **outside the file** in a *Repository* forming the *Revision History*

![Final Doc](images/swc-version-1.svg)

---

## Concurrent editing


- Once changes are managed outside the file, you can treat a file differently by playing
 back different sets of changes

- For example, the set of green changes was made by "User A" and the red changes by "User B"

![Final Doc](images/swc-version-2.svg)

---

## Merging Changes

- Later the changes from "User A" and "User B" are played against the same base file merging them into one.

- This is a "Killer Feature" of a modern VCS - users can edit the same files and not have to lock others out while they are working
  on them

![Final Doc](images/swc-version-3.svg)

---

## Merge Conflict

- Merges usually work well but if the same location in the file is changed by both, a *Merge Conflict* may occur

- The user merging must decide which change "wins" to resolve the conflict.

![Final Doc](images/swc-version-3.svg)

---

## Git

.right[![Git Logo](images/git-logo.jpg)]

- Git is an open-source implementation of a Version Control System

- Very popular due to its speed and distributed nature

- No real "master" copy - each user has a complete copy of the Revision History in their Git Repository on local disk (`.git/`)

- Almost all operations are performed locally with no network connection necessary

- Changes in the local Repository are often pushed to and fetched from another copy of the Repository called a "remote"
  which is no different in nature than the local copy (just located "remotely")

- A common remote is a GitHub project

- *Note that GitHub is not Git* - it is an optional service that many people choose to conveniently host a copy of their Repository -
  you can make your own remote in another location on disk or on another computer and access it with `file:///` and `ssh:///`.

---

## The 3 Areas

Git has 3 areas where file contents resides:

1. *Working Tree* - The project's top-level directory, sub-directories, and files which the user edits and changes

2. *Index* - A hidden, dynamic, binary file (`.git/index`) where changes to files in the Working Tree are staged for commit

3. *Repository* - A top-level, hidden directory (`.git/`) containing all the Commits and File Revisions of the project (Revision
   History)

-------

- When things are "clean" - the files in the Working Tree, Index, and most recent Commit in the Repository (`HEAD`) will be identical.

- Users use `git status` on a frequent basis to examine the state of the Working Tree, Index, and `HEAD` as they make changes to
  files.



---

## The 3 Areas

.right[![Git Areas](images/3-tier.png)]

Different git commands move changes in and out of the Working Tree, Index, and Repository allowing
gradual transitions from one complex repository state to another.

For example:
- `git add` - stages changes made in the Working Tree into the Index
- `git commit` - snapshots changes staged in the Index into a new Commit
- `git checkout` - modifies the Working Tree to match a revision in the Repository

Note:
- *The Working Tree is sometimes called the Working Directory*
- *The Index is sometimes called the Staging Area or Cache*

---

## Basic Example

To create a git project, we can initialize a project directory:
```
$ cd ~/projects/acme
$ git init .
```

This will create a `.git/` directory containing the Repository but leave all other files untouched.

The project directory `~/projects/acme` is the top-level of our Working Tree.

The Index where we will stage changes will be located in the special binary file `.git/index`

---

## Staging/Commiting to the Repository

Lets presume we have one file in the Working Tree named `README.md`.

To add this to the Revision History we must:

1. Stage the file in the Index: <br/>
   `git add README.md`

2. Commit the staged change into the Repository: <br/>
   `git commit -m 'Add README.md'`

We now have the beginning of the Revision History which can be viewed with the `git log` command:

    $ git log
    commit 17e8ca945e8e80ed0b701e15d0dcffcce35fe657
    Author: Kevin Counts <counts@digicat.org>
    Date:   Sat Jul 7 11:04:34 2018 -0400

        Add README.md


---

## Commit Graph

Now lets make another change to `README.md` by adding a new line of text.

We then:

- Stage the change in the Index with `git add README.md`
- Commit the change with `git commit -m 'Add new line to README.md'`

This new commit will store a pointer to our first commit and start building a chain.
In the diagram below, we see a 4-commit Revision History with the default branch `master` pointing to the most recent commit id `01ac0f6`.

The branch or commit id that `HEAD` points to is what Git uses for the parent of the next commit. We say we are on the `master` branch
because `HEAD` points to `master` which points to its head, commit `01ac0f6`.
Git will use `01ac0f6` as the parent of the next commit and then move the head of `master` forward to point to the new commit id and so on
building the Revision History as a linked-list.

![Commit Graph](images/commit-chain.png)

---

## The 3 Object Types

A Git Repository has 3 primary object types:

- A *Blob* which holds the contents of a file

- A *Tree* which maps files and sub-directories to Blobs and other Trees (sub-directories)

- A *Commit* which contains:
  - A pointer to a Tree (top-level at time of commit)
  - A pointer to a parent commit
  - A commit message with Author and Date

---

## The SHA

When a Blob, Tree, or Commit object is created, Git uses something called a Hash Function
to create a unique, fixed-size identifer based on the object's content.

Git uses the Secure Hash Algorithm 1 (SHA-1) to implement its hash function so
the identifier is typically referred to as a "SHA". The SHA is a 160 bit value
represented as a 40-character hexadecimal string.

You can generate your own SHA identifer from the CLI: <br/>
```
$ echo '# Project Acme' | git hash-object --stdin
6fbcbe046ef6288521d4559c35da541cf4ea428c
```

*Shorthand:*

- Use the first N characters of the SHA (e.g. `6fbcbe0`) to save typing

- If two or more objects have the first N characters in their SHA, Git will ask for more (*never write scripts using shorthand*)

---

## SHA Collision

- If two files with different content create the same SHA we would experience a "Hash Collision"

- The first file to be added would have its contents copied and the second file's content would never be saved since Git would see the object was already there.

- The chance of 2 different files creating the same SHA is incredibly small

- To give perspective, if 1 million users each created 86,400 unique files per day, it would take 38 billion years
before it was likely two different files would create the same SHA

- The Sun will turn into a Red Giant and consume the Earth in 7.6 billion years

![Sun](images/sun.jpg)

---

## The Blob

- The term blob is an abbreviation for "Binary Large Object" and comes from the database ecosystem

- A blob stores the contents of a file only

- There is no file name associated with a blob - just a SHA (e.g. `.git/objects/6f/bcbe046ef6288521d4559c35da541cf4ea428c`)

- File names are stored in Trees with a Tree entry matching a human-readable file name and SHA pointing to the actual content

- If you have two or more copies of the same file in your repository, Git will only store one copy internally

- If you have two files which differ only by one character, Git will store two copies.

- Git uses different compression techniques to minimize "wasted-space"


---

## Other

Git stores the blob based on its SHA-1 within its repository:

For example the file with the content `# Project Acme` would be stored at:
```
.git/objects/6f/bcbe046ef6288521d4559c35da541cf4ea428c
```

You can display the contents of a Blob by using the command `git cat-file -p <id>` and its type with `git cat-file -t <id>` , for example:
```
$ git cat-file -p 6fbcbe046ef6288521d4559c35da541cf4ea428c
# Project Acme

$ git cat-file -t 6fbcbe046ef6288521d4559c35da541cf4ea428c
blob
```

---

## The Commit Object


