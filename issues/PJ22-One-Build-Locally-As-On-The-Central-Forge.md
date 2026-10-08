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
  run holds, delete what a run left behind. A caller asks for a **kind**, never for a provider's server
  type or location.
- **A kind says what a run needs of its surroundings, not how large the hardware is**: the operating
  system and its version, the architecture and CPU features (say `no-avx2`), what the run may do
  (install packages, rootless podman, its own user manager, reach the network, reboot), a base snapshot
  where a test assumes one, and special hardware only where needed. CPU and memory appear only as a
  minimum or a limit where they matter: on a VM account they become the run's limits, at a provider the
  server type rented. A project names its kinds, for example:

      machines:
        ubuntu:  { os: ubuntu-26.04, arch: x86_64, may: [packages, podman] }
        fedora:  { os: fedora-44,    arch: x86_64, may: [packages, podman] }
        old-cpu: { os: ubuntu-26.04, cpu: [no-avx2] }
        restart: { os: ubuntu-26.04, may: [packages, podman, reboot] }

  and its runs, each a declared command on a kind (`runs: { acceptance: { machine: ubuntu, command: … } }`).
- **The setup says who meets a kind** - `ubuntu` a run account on the local VM or a rented server,
  `old-cpu` an account on a machine with that CPU, `restart` a rented server only - and a kind it cannot
  meet is refused with that said.
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
  used; a task may ask for one of them by its name, never for a command of its own, a kind not declared, a
  provider or a key. No
  container gets a credential or a network path to the machine.
- **Each run starts clean**: a fresh account or server, the commit alone, no secret of the host.
- **One reader contract, two forges**: the build readers stay behind the one contract they already
  share (`org.fuin.sokar.Build1`, through `sokar-build-api`), abstracted where it still speaks of one
  forge, and a **`forgejo` reader** joins `github`, so a task reads a local build's verdict as a central
  one's. **One contract suite**, in the API's kit, holds what every reader must do - a branch's head, a
  commit's verdict over every run, a failing job and its log, a refused credential said plainly - and
  runs against each reader: against GitHub centrally, against the local Forgejo locally. Only what is
  particular to one forge's API is tested in that reader alone.

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
- The `github` and the `forgejo` reader pass the same contract suite, each against its own forge.
- A task asks for its work to be built on a declared machine kind and reads the verdict and the log in
  `/sokar/files`; a request for a kind or a command the project does not declare is refused.

## To be checked

(none open)
