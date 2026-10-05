# PJ14 — Work starts without a project

**Status:** now; built in `sokar` and `sokar-frontend` and measured, three points of the acceptance
left.

**What must be true.** A developer who has a repository checked out, or picks one in the interface,
starts a task on it without writing a project first. That work belongs to the project **`default`**,
which every machine has, which Sokar keeps itself, and which nobody configures.

## Why

What is measured already: in a fresh account, in a checkout with an ssh `origin`, a task starts with
no project named, the repository is listed under `default`, and approved work arrives at `origin`;
the same from the interface, with a repository named by hand and one picked from GitHub's list;
following a project called `default` is refused; egress for `default` is refused, naming a project
repository as the way.

## The shape

1. **`default` is the one project that does not come from a repository.** Sokar creates it on every
   machine and keeps it there; no repository, signature or follow is involved, and its configuration
   comes from Sokar and the person at this machine, never from anything a task or a forge can write.
2. **Its name is fixed and reserved**: no followed project may be called `default`.
3. **Its settings are Sokar's defaults and cannot be changed**: class `guarded`, the default base
   image, no egress beyond the agent's provider. Whoever needs more makes a project repository.
4. **No messaging**: its tasks are standalone and nobody's peers.
5. **Approved work goes to the repository's `origin`**, reached with what the developer already has
   (an ssh key in their agent, or an https token set once); a repository picked in the interface is
   bound with its own deploy key.
6. **A repository stays in `default` until removed there, or until a followed project names it**;
   then the followed project takes new tasks, and the offer to remove it from `default` is made. A
   running task keeps its project. `default` cannot be deleted or unfollowed.

## Acceptance

- Giving `default` a transport or a peer is refused, naming the way that does it - a project
  repository - as egress already is.
- A `project.yml` that names itself `default` is refused at the follow, naming the reason.
- A repository that a followed project names starts its next task in that project, and the offer to
  remove it from `default` is made - measured, not only built.
