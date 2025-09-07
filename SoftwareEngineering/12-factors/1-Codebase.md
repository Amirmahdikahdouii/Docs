# 1. Codebase

> One codebase tracked in revision control, many deploys

A **codebase** is any single repo (in a centralized revision control system like Subversion), or any set of repos who share a root commit (in a decentralized revision control system like Git).

There is always a one-to-one correlation between the codebase and the app:

- If there are multiple codebases, it’s not an app – it’s a **distributed system**. Each component in a distributed system is an app, and each can individually comply with twelve-factor.
- **Multiple apps sharing the same code is a violation of twelve-factor**. The solution here is to factor shared code into libraries which can be included through the dependency manager.

There is **only one codebase per app**, but there will be **many deploys of the app**.
A **deploy** is a `running instance` of the app. This is typically a **production** site, and one or more **staging** sites. Additionally, every developer has a copy of the app running in their local development environment, each of which also qualifies as a deploy.

> [!IMPORTANT]
> The codebase is the same across all deploys, although different versions may be active in each deploy. For example, a developer has some commits not yet deployed to staging; staging has some commits not yet deployed to production. But they all share the same codebase, thus making them identifiable as different deploys of the same app.

# 📘 Centralized vs Distributed Version Control Systems

## 1. What is a Version Control System (VCS)?

A **Version Control System (VCS)** is a tool that helps developers manage changes to source code over time. It tracks history, allows collaboration, and makes it easier to revert, branch, or merge changes.

There are two main categories:

* **Centralized VCS (CVCS)** → Example: **Subversion (SVN), Perforce**
* **Distributed VCS (DVCS)** → Example: **Git, Mercurial**

---

## 2. Centralized Version Control (e.g., Subversion)

* A **single central server** stores the official repository.
* Developers check out a working copy from the server.
* All commits and history are stored centrally.
* Without the server, you cannot commit or view full history.

---

## 3. Distributed Version Control (e.g., Git)

* Every developer has a **full copy of the repository** (including history).
* Commits are made locally first, then optionally shared with a central remote.
* Offline work is fully possible.
* Multiple workflows (forks, pull requests, feature branches) are supported.

---

## 4. **Comparison: Git vs Subversion**

| Aspect             | Subversion (SVN – Centralized)                   | Git (Distributed)                            |
| ------------------ | ------------------------------------------------ | -------------------------------------------- |
| **Architecture**   | Single central server, working copies on clients | Each clone is a full repository with history |
| **History**        | Stored only on server                            | Stored fully on every client                 |
| **Offline Work**   | Very limited (mostly read-only)                  | Full commits, branches, history offline      |
| **Branching**      | Heavy, directory-based, server-dependent         | Lightweight, fast, local                     |
| **Speed**          | Slower (depends on server for operations)        | Very fast (local operations)                 |
| **Collaboration**  | Centralized, strict commit access                | Decentralized, flexible, fork/merge model    |
| **Data Integrity** | Relies on server reliability                     | Uses cryptographic hashes for integrity      |
| **Learning Curve** | Simple for beginners                             | More complex but more powerful               |

---

## 5. Why Centralized Systems Still Exist

Although Git dominates modern development, centralized VCS tools like **Subversion** and **Perforce** are **not deprecated**. They are still used in industries and enterprises for several reasons:

### 🔹 1. Centralized Control

* A single source of truth makes **permissions and auditing simpler**.
* Useful in government, financial, or military projects requiring strict control.

### 🔹 2. Handling Large Binary Files

* Git struggles with **large assets** (videos, 3D models, textures).
* Centralized systems like Perforce and SVN handle binaries more efficiently.
* That’s why **game development studios** and **multimedia companies** still rely on them.

### 🔹 3. Simpler Workflow

* SVN’s workflow (`update → edit → commit`) is straightforward for teams that don’t need complex branching.
* Non-technical contributors (e.g., documentation teams) may prefer SVN.

### 🔹 4. Security and Storage

* In Git, every clone contains the entire project history, which may be a **security concern**.
* In SVN, the central server holds history, and clients only see what they need.

### 🔹 5. Legacy and Integration

* Many enterprise CI/CD tools and internal systems are deeply integrated with SVN.
* Migrating large projects from SVN to Git can be costly and risky.

### 🔹 6. Reliable Centralized Networks

* If all developers are on the same local network, SVN’s central model works smoothly without Git’s distributed overhead.

---

## 6. Conclusion

* **Git** is the de facto standard for open-source projects and modern software development, offering speed, flexibility, offline capabilities, and powerful branching/merging.
* **SVN and other centralized VCS** are still valuable in industries that prioritize strict access control, large binary handling, simpler workflows, or deep legacy integrations.

👉 In short: **Git fits modern, distributed, code-centric projects. Centralized VCS fits controlled, asset-heavy, or legacy environments.**
