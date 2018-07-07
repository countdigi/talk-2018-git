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

<br/>

- A Version Control System (VCS) is a piece of software that helps manage a project's files

- It stores a base version of each file and tracks changes made to the file over a period of time called Revisions

- These changes are stored in a "Revision History" and can be rewound and played back against the base version

---

## Revision History stores Commits

![Final Doc](images/swc-version-1.svg)

As a file is changed, those changes are saved by the VCS in a series of commits which are stored *outside the file*.

---

## Commits allow concurrent editing

![Final Doc](images/swc-version-2.svg)

Once changes are managed outside the file, you can treat the file differently by playing
back different sets of changes.

For example, the set of green changes was made by "User A" and the red changes by "User B".

---

## Commits allow merging

![Final Doc](images/swc-version-3.svg)

Later the sets of changes are played against the same base file merging them into one.
Typically this works out well, but if both sets modify exactly the same location, a conflict may occur
and a human must decide which change "wins" to resolve the conflict.

---

## Git

.right[![Git Logo](images/git-logo.jpg)]

- Git is an open-source implementation of a Version Control System

- Very popular due to its speed and distributed nature

- No real "master" copy - each user has a complete copy of the Revision History on their local disk

- Most operations are performed locally with no network connection necessary

- Changes are synchronized between users by pushing and fetching changes to and from another copy
  of the Revision History called a "remote"

- A remote copy of the Revision History is often hosted at Github to centralize sharing of changes
  but no different than local copy on disk

- You can create a locally-hosted remote by initializing a directory with `git init --bare <path>`
  and specifying `file:///<path>` as the remote URI (the option `--bare` creates a Repository without
  the Working Tree)

---

## The 3 Areas

Git has 3 areas where file contents resides:

1. *Working Tree* - The project's top-level directory, sub-directories, and files from which the user reviews
   content and makes changes

2. *Index* - A hidden, dynamic, binary file (located in the Repository) where changes to files are staged for future commits

3. *Repository* - A special directory (usually named `.git/` located in the top-level of Working Tree) where commits are stored

---

## The 3 Areas

.right[![Git Areas](images/3-tier.png)]

Different git commands move changes in and out of the Working Tree, Index, and Repository allowing
gradual transitions from one complex repository state to another.

For example:
- `git add` - stages changes made in the Working Tree into the Index
- `git commit` - snapshots changes in the Index into a new Commit
- `git checkout` - modifies the Working Tree to match a revision in the Repository

*Note: The Working Tree is sometimes called the Working Directory. The Index is sometimes called the Staging Area or Cache.*

---

## Basic Example

To create a git project, we can initialize a project directory:

    cd ~/projects/acme
    git init .

This will create a `.git/` directory containing the Repository but leave all other files untouched.

The project directory `~/projects/acme` is the top-level of our Working Tree.

The Index where we will stage changes will be located in the special binary file `.git/index`.

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

Now lets make another change to `README.md`...

---

## Commit Graph

If we make another change to `README.md`, stage the change in the Index with `git add` and commit the change with `git commit`, that new commit will store a pointer to our first commit building a chain.

In the diagram, a single branch named master has a 4-commit revision history with `4e77435` as the initial commit and `01ac0f6` as the most recent commit.

The branch `master` points to the latest commit and `HEAD` points to the branch `master`.

`HEAD` tells Git what will be the parent of your next commit and so we say we are currently "on" the branch `master`.

Note the arrows pointing left-to-right in the diagram represent time, but the commit graph would be arrows pointing right-to-left (towards the past).

![Commit Graph](images/commit-chain.png)

---

## The 3 Object Types

Git maintains its Revision History through 3 types of objects:

- A *Blob* which contains the contents of a file

- A *Tree* which contains entries referring to other Blobs and Trees

- A *Commit* which contains:
   - A pointer to the project's top-level tree at a particular revision
   - A pointer to a parent commit
   - A message including details such as the author and date

---

## The SHA

When a Blob, Tree, or Commit object is created, Git uses something called a Hash Function
to create a unique, fixed-size identifer based on the object's content.

Git uses the Secure Hash Algorithm 1 (SHA-1) to implement its hash function so
the identifier is typically referred to as a "SHA". The SHA is a 160 bit value
represented as a 40-character hexadecimal string (e.g. `6fbcbe046ef6288521d4559c35da541cf4ea428c`).

You can generate your own SHA identifer from the CLI: <br/>
```
$ echo '# Project Acme' | git hash-object --stdin
6fbcbe046ef6288521d4559c35da541cf4ea428c
```

Git allows users to provide a shortened version of the SHA, typically the first 7 characters of the identifier
so the value `6fbcbe0` could be used to save typing.
Git will let you know if the shortened SHA is ambiguous and could refer to more than one object and ask for a longer value.

---

## SHA Collision

If two files with different content create the same SHA, the first one to be added will win and we would experience a "Hash Collision" and the second file's content would be silently ignored and never saved.

The chance of 2 different files creating the same SHA is incredibly small.

To give a perspective, if 1 million users each created 86,400 unique files per day, it would take 38 billion years
before it was likely two different files would create the same SHA.

The Sun will turn into a Red Giant and consume the Earth in 7.6 billion years.

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


