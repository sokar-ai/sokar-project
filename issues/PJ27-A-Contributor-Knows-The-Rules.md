# PJ27 — A contributor knows the rules

**Status:** soon. Spans every repository (each links the guide).

**What must be true.** Someone who wants to change Sokar finds, from any repository, one guide that
says how the work is organised, how a change is made, what it must satisfy before it is accepted, and
where to ask. They do not have to read `AGENTS.md`, which is written for agents, to learn it.

## Why

The rules a change must follow exist, but only in the shared block of each repository's `AGENTS.md`,
addressed to the agents that work there. A person arriving at a public repository finds a README and a
documentation site for users, and nothing that tells them what a commit, a test or an issue must look
like here, or which of the ten repositories their change belongs in.

## The shape

- **One page, `doc/contributing.md` in `sokar-project`**, so it appears in this repository's chapter of
  the documentation site; this repository is about how Sokar is developed as a whole.
- **Every repository links it:** a `CONTRIBUTING.md` at its root holding one paragraph and the link
  (GitHub shows that file when an issue or a pull request is opened), plus what is particular to that
  repository - how it is built and tested, linking its `build.md`.
- **The rules are not copied.** The page explains the process and the reasons, and points at the shared
  block of `AGENTS.md` as the one authoritative list, so there is no second copy to drift.
- **Proposed sections of the page:**
  1. **How the work is organised** - one project, ten repositories, what each holds (the table in
     `sokar-project`'s README), which repository a change belongs in.
  2. **Issues** - every open task is a file in its repository's `issues/`, grouped Now, Soon and Later;
     the prefixes per repository (`project.yml`); a requirement not yet placed waits in
     `sokar-project`; how someone outside proposes one.
  3. **Making a change** - build and test locally (each repository's `build.md`); a fix starts with a
     test seen to fail; one task, one commit when it is finished; the commit message (what changes
     and why, its length, no issue numbers); the changelog.
  4. **What a change must satisfy** - pinned dependencies and tools, no secret in a file or a command
     line, documentation describing the current state only, decisions in `doc/decisions.md`, rules in
     `AGENTS.md`; the shared block linked for the full list.
  5. **What the build checks** - the checks every push runs, and which builds rent a test machine and
     when (only on `main` and on a release tag, so a pull request from a fork builds without them).
  6. **Pull requests and review** - who reviews, how a change from outside is taken in, signing.
  7. **Releases** - versions, the `releases` and `snapshots` channels, who releases.
  8. **Reporting a vulnerability** - where, and not as a public issue.
  9. **Licence** - the code is under the GPL v3 or later; what a contribution is licensed under.
- **Decided, and what the page says:**
  - **A contribution comes in under the project's licence and stays under it.** Every commit carries
    a `Signed-off-by:` line (`git commit -s`): the Developer Certificate of Origin, by which its author
    certifies the right to submit it. No rights are transferred and there is no contributor agreement;
    Sokar is offered under the GPL only.
  - **Work written with an AI's help is accepted, and says so.** A person vouches for every commit with
    their sign-off - never the agent - and the commit names the help in an `Assisted-by: <tool, model>`
    line. Sokar's own agents' commits follow the same rule.
  - **A pull request from outside follows an issue.** Someone who wants to change something opens a
    GitHub issue first; once it is agreed there, a pull request is welcome. One that arrives without
    that is closed with a pointer to this page.

- **The repositories' settings that a contributor meets**, set by the operator in every public
  repository:
  - secret scanning and push protection on, private vulnerability reporting on (section 8 points to
    it), Dependabot alerts on;
  - a squashed pull request's commit message is "Pull request title and commit details", and a merged
    branch is deleted.

## Acceptance

- The page is on the documentation site in `sokar-project`'s chapter, and `mkdocs build --strict` is
  clean.
- Every repository has a `CONTRIBUTING.md` linking the page, and its README links it too.
- A pull request whose commits lack a `Signed-off-by:` line is marked failing by a check, seen to fail
  on one without it.
- The shared block of `AGENTS.md` says that every commit carries the sign-off and, where an agent wrote
  it, the `Assisted-by:` line.
- Every public repository has the settings above; seen to fail: one that lacks any of them.
- No rule is stated on the page that contradicts the shared block; a rule the page states is linked
  to it rather than restated.

## To be checked

- Where is a vulnerability reported: a `SECURITY.md` per repository, GitHub's private reporting, or an
  address?
- A code of conduct: wanted, and which?
