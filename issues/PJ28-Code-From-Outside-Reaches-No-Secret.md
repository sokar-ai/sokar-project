# PJ28 — Code from outside reaches no secret

**Status:** now; before the repositories are made public (PJ18). Spans every repository: the settings
are the operator's, the check is `sokar-buildtools`'.

**What must be true.** Once the repositories are public, a change from someone outside never runs with
a secret - no rented machine, no package or artifact published, no token read - until a maintainer has
reviewed it and taken it into `main`; and what may take a change into `main` is fixed in each
repository's settings, not left to habit.

## Why

The builds rent test machines and publish packages and artifacts with secrets held by GitHub. A pull
request from a fork already gets none of them: its token is read-only, and the jobs that rent or
publish run only on a push to `main`, on a release tag or when started by hand. What is missing is
everything around that:

- nothing stops a push straight to `main` by anyone with write access, or a force push;
- a change to a workflow, a build file or the rules needs no particular reviewer;
- a fork's pull request runs its workflow on GitHub's runners as soon as it is opened;
- nothing stops a workflow from using `pull_request_target` with the pull request's code checked out,
  which runs that code with the secrets.

## The shape

- **`main` is protected in every repository** (a ruleset): no force push and no deletion; a change from
  anyone but the operator arrives as a pull request with one approving review. The operator's own
  pushes - how every agent's work reaches `main` today - stay possible as a bypass of the ruleset.
- **`CODEOWNERS` in every repository** names the operator for `.github/`, the build files (`pom.xml`,
  `pubspec.yaml`, build scripts), `AGENTS.md` and `CODEOWNERS` itself, so a pull request touching them
  waits for the operator.
- **A fork's pull request runs no workflow until approved:** each repository's Actions setting "Require
  approval for all external contributors".
- **`check-actions` refuses `pull_request_target`** in any workflow, and a `workflow_run` that checks out
  the triggering pull request's code.
- **What a leaked secret could reach stays small:** the machine provider's token rents in its own
  project only, under the limit on machines at once, and the sweep deletes what a run left behind;
  each secret can be rotated without changing a workflow.

## Acceptance

- In every repository: a force push to `main` is refused; a push to `main` by an account other than
  the operator's is refused; a pull request touching `.github/` asks for the operator's review.
- A pull request from a fork shows its workflow waiting for approval.
- `check-actions` fails on a workflow using `pull_request_target`, seen to fail, and passes on every
  current workflow.

## To be checked

- The ruleset's bypass: the operator's account alone, or also a deploy key or app for the update jobs
  that push today?
