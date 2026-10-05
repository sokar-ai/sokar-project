# PJ08 — An unattended job's refusal reaches a person

**Status:** soon.

**What must be true.** Where a job nobody is watching refuses something, the refusal reaches a person.

## Why

The weekly update runs of `sokar-pi` and `sokar-omp` end with GitHub refusing to let Actions create a
pull request. `sokar-pi`'s licence gate answered correctly, and the pull request that would have
carried its verdict to somebody could not be opened, so the finding existed only in a run log nobody
opens. That is worse than a gate that fails, because every place a person looks reports success.

The mechanism is an organisation setting - *Allow GitHub Actions to create and approve pull
requests* - which belongs to no repository. The class is wider: what made it expensive is that the
run was **unattended**. A workflow that runs because somebody pushed or dispatched it has a reader by
construction; a scheduled one needs a place its refusal lands.

## The shape

- **The organisation setting is on**: *Settings → Actions → General → Workflow permissions → Allow
  GitHub Actions to create and approve pull requests* in `sokar-ai`. It covers every workflow that
  opens a pull request, and needs no token of its own; whether the weekly update now opens its pull
  request is measured by running it once by hand. It does not address
  the class on its own: a refusal that is not a pull request still needs a place to land.
- **Every unattended job is listed**, per repository, with where its refusal lands.

## Acceptance

- No scheduled or otherwise unattended job in any repository can refuse something without the refusal
  reaching a person, seen by making one refuse on purpose.
- Every unattended job is listed, per repository, with where its refusal lands; a job whose answer is
  only its run log fails this.

## To be checked

- Which place a refusal can land that a person actually reads: a pull request, an issue or a
  notification - the agents' channel is not reachable from a hosted runner.
- How many unattended jobs there are: `sokar-message-sluice` has none, `sokar-pi` and `sokar-omp`
  have the weekly update; each other repository reports its own.
