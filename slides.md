class: top

## Version Control with Git

![Git Logo](images/git-logo.jpg)

<br/>
Kevin Counts<br/>
Systems Engineering
<br/>
<br/>
Health Informatics Institute<br/>
University of South Florida<br/>

---

## The Version Dilema

![Final Doc](images/final-doc.png)


---

## What is Git?


---

## What is Git?

- Version Control System

--

- Distributed

--

- Supports non-linear development

--

- Ensures cryptographic integrity

---

## What is Git?

**Version Control System (VCS)**

--

- Software that manages the state of a folder's contents over time

--

- The managed folder is called the Working Tree<br/>(e.g. `~/projects/teddy-human-wgs/`)

--

- As files are added and modified in the Working Tree, versioning is accomplished by creating "Commits" which snapshot the state of all the files at that moment in time

--

- Each commit is stored in a repository which is a hidden sub-folder named `.git/` under the Working Tree (e.g. `~/projects/teddy-human-wgs/.git/`)

--

- Version history is established by each commit having a pointer to its parent commit forming a "Commit Graph" pointing back in time<br/>(e.g. `c1 <- c2 <- c3 <- c4`)

---

## What is Git?

**Distributed**

--

- Each user has a full local copy of the repository and all history

--

- Almost all operations can be performed locally without network connectivity

--

- For collaborative work, a copy of the repository called a "remote" is established

--

- A remote is any other copy of the repository other than the one you directly work out of

--

- Changes are "pushed" and "fetched" through the remote for sharing

--

- Github is often chosen to host the remote but using Git does not require Github

---

## What is Git?

**Supports Non-Linear Development**

--

- Efficient and powerful branching model

--

- Uses object-tree model with lightweight pointers yielding almost instant branch operations

--

- Development branches easily and inexpensively created, switched, merged, or thrown-away

--

- In Git, every line of history is tracked through at least one branch (default is "master")

--

- The starting branch "master" may be thought of as the main trunk of the tree but is no different in function than any other branch

---

## What is Git?

**Ensures cryptographic integrity**

--

- Every object stored in a Git repository has an object ID<br/>(e.g. `29933f2c3c2653cac59b4d7d325830e6f9fe3f04`)

--

- The object ID is generated from the object's content creating a unique 40 character, 160-bit "Message Digest" or "Checksum Hash"

--

- Since the algorithm Git uses to create this is the Secure Hash Algorithm (SHA-1), the object ID is referred to as simply the "SHA"

--

- The objects are stored in the repository with their filename set to their "SHA" (e.g. `.git/objects/29/933f2c3c2653cac59b4d7d325830e6f9fe3f04`).

--

- This is called "Content Addressable Storage" (CAS) because you reference an object not by a human-readable filename but by its unique SHA that was generated based on its content.

--

- The chance of two objects generating the same SHA, called a Hash Collision, is the same as randomly picking the same atom on the same moon twice if we imagined there were 10 moons orbiting Earth

--

- The commit history uses the SHA of its ancestor as well as the SHAs of all referenced trees and files to calculate its own SHA - this creates a verifiable chain ensuring cryptographic integrity


## What is Git?

**Ensures cryptographic integrity**
