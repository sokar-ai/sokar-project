# PJ22 — One build, run locally as on the central forge

**Status:** soon. Spans `sokar-buildtools` (the tooling that rents machines and checks the rules), every
repository's workflows and build files, and the local setup.

**What must be true.** A change is built, tested and published the same way before it is pushed as after:
the same workflow files run on a local forge as on the central one, take their artifacts from that forge's
registry, publish into it, and run their acceptance legs on a machine rented through one interface - an
account on a local VM, or a server at a provider. What the central run would find, the local run finds
first. A task can have its work built and tested the same way, and reads the result as it reads any
build's.

## Why

A run on the central forge (GitHub Actions) and a run on a developer's machine differ in three things
only, and each has let faults through to the central forge that a local run never saw:

1. **Where the artifacts come from.** Centrally from Maven Central, Sonatype's snapshots and the package
   repositories; locally from a hand-over directory and what each developer installed by hand. A pom that
   names a release no one published, or a parent that is only installed locally, passes locally and fails
   centrally.
2. **What is published.** Centrally every push publishes; locally nothing does, so a publishing
   configuration that breaks for a child module is first seen on a release tag.
3. **Where the acceptance legs run.** Centrally on a freshly rented server that builds the tree and
   installs it as a package would; locally in a long-used account on a VM, deployed by hand, with steps
   that exist only on the rented server never run at all.

The provider is also named across the repositories today (a directory `hetzner/`, chapters of
`AGENTS.md` and `.AGENTS.md`, the rule "at most five rented machines at once"), so a second provider or a
local one means changing every one of these places.

## The shape

### One interface for rented machines

- **`sokar-machines` in `sokar-buildtools`**: rent a machine of a kind, run on it, return it, list what a
  run holds, delete what a run left behind. A caller asks for a **kind** - distribution, CPU level, size,
  and further requirements such as a GPU or a snapshot of its own - never for a provider's server type or
  location.
- **The provider sits behind it**, chosen by the setup's configuration: a **local VM provider** that
  rents one account of a **pool of run accounts** on a VM (four to start with, each reset when it is given
  back; the sudo of today's agent accounts for installing packages, but **no reboot** - scenarios that
  reboot run centrally only; the pool's size is the local limit), and a **server provider** that rents from a cloud provider. Its name appears only in the one
  implementation that talks to it.
- **The limits are the setup's**: how many machines, or VM accounts, may be rented at once is
  configuration of the interface, which waits or refuses beyond it.

### One build, two places

- **A local forge, Forgejo** with its Actions runner, mirrors the repositories and runs **the same
  workflow files** as the central forge - the one tool that runs them unchanged; where a step our
  workflows use does not run there (an action not reachable, a variable of the central runner's image),
  it is made a variable of the setup or mirrored, never a second workflow. The machine part is
  `sokar-machines`', not another tool's.
- **Its package registry** (Maven, Debian, RPM, generic) stands in for Maven Central, Sonatype's
  snapshots and the package repositories: every local build publishes into it, and every other
  repository's local build resolves from it. It replaces the hand-over directory.
- **What differs is configuration, never the workflow**: the registry addresses, the machine provider
  and its limits, and which scenarios are left out (those that reboot a machine, locally). A repository
  names its repositories itself, so these are variables of the setup.
- **Only the central forge releases**: a release to Maven Central, released by hand in its portal, has
  no local counterpart; locally the chain ends in the local registry.
- **The order is local first**: a change is pushed to the central forge after its local run on the local
  forge is green, and the repositories that build on it were green against what it published locally.

### A task's build, on another machine

- **A build source like any other**: a task's work, taken from its gate, is built and tested by a
  workflow on the local forge or the central one, on a machine of the kind the project declares; the
  verdict, the jobs and a failing job's log reach the task as every build's do (`Task.builds`, its files).
- **The task asks, the host decides**: the project declares the runs and the machine kinds that may be
  used; a task may ask for one of them by name, never for a command of its own, a provider or a key. No
  container gets a credential or a network path to the machine.
- **Each run starts clean**: a fresh account or server, the commit alone, no secret of the host.

## Acceptance

- The same `build.yml` runs green on the local forge and on the central forge, for every repository that
  builds, with no edit between them. Seen to fail: a step that runs only in one place without a variable
  saying so.
- A repository's local build resolves another repository's artifact from the local registry, published
  by that repository's local build, not from a hand-over directory or a local install. Seen to fail: a
  build green with an artifact no local build published.
- A publishing fault of a child module fails the local run before any tag reaches the central forge.
- An acceptance leg runs on a rented VM account locally and on a rented server centrally, through the
  same interface call; no repository outside the interface's implementation names a provider (a check
  in `sokar-buildtools` finds a name that slips in).
- A task asks for its work to be built on a declared machine kind and reads the verdict and the log in
  `/sokar/files`; a request for a kind or a command the project does not declare is refused.

## To be checked

- **What the interface offers**: is "a kind of machine" enough, and how a task's request for one is
  written and checked.
- **Tests that speak to the central forge itself** (the GitHub build reader's live tests): left out
  locally, or run against the local forge's own API.
