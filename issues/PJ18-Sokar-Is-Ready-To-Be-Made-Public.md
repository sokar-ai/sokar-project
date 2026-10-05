# PJ18 — Sokar is ready to be made public

**Status:** now; released and public. Left, all the operator's: secret scanning, push protection,
private vulnerability reporting and Dependabot alerts in each repository; the secret `SOKAR_DOCS_READ`
deleted, its token revoked and the site's workflow run green without it; the organization's URL and
each repository's website link to its chapter; each repository's squash commit message set to "Pull
request title and commit details" and merged branches deleted; then the final test from a fresh
machine.

**What must be true.** Every Sokar repository can be opened to the public: its documentation is
short and easy for a person to read, its history is its first commit and one more, its changelog starts with that
version, and its first release is 0.4.0.

## The steps, in this order

1. **The documentation is reduced to what matters.** Every `README.md` and `doc/` page is read
   through and cut to what a person needs: not too much, and easy to read. Where the structure does
   not serve that, it is reworked together with the operator, who reads it through afterwards (read).
2. **The operator reviews each `AGENTS.md`** before the release (done), and **every finished issue is
   deleted** in every repository, what outlives it moved into `doc/`, `doc/decisions.md` or
   `AGENTS.md`, and a question still open kept as an issue of its own.
3. **The changelog is emptied** and holds one entry: "Initial public version".
4. **All commits except the very first (the one made when the repository was created) are squashed
   into one**, with the message "Initial public version", and pushed with a forced push. This is the
   one deliberate exception to "the repair is never a force push", for this step only, and the
   operator pushes it.
5. **The first release is 0.4.0.**
6. **The documentation is published** at `https://sokar-ai.github.io` (PJ21): once the repositories
   are public, the operator sets the variable `PUBLISH` to `true` in `sokar-ai.github.io`, deletes the
   secret `SOKAR_DOCS_READ` and revokes its token.

## Before step 4

- **The full history is kept** where only the team can reach it (a `git bundle` per repository,
  in the backup), since a forced push leaves no other copy.
- **The history is searched for secrets.** A commit that leaves the branch is not necessarily gone
  from the forge: it may stay reachable by its hash. Anything that must not become public is found
  before the repository is.
- **Where the work lands:** each repository's owner does steps 1, 3 and 5 for its own, and prepares
  step 4 (the squashed commit, the bundle) without pushing; the operator does step 2, reads step 1's
  result, and pushes step 4.

## The shape

- **Every repository releases 0.4.0 together.** Afterwards each moves on its own when it changes.
- **The published snapshots are deleted before the release**, from both indexes, so no machine is
  held at a `1.0.0~snapshot` that orders above 0.4.0. The packages' version lines become 0.4.0.
- **The snapshot channel stays** for CI and the acceptance legs, and after the release its builds are
  numbered `0.4.1~snapshot.N`: above the release, below the next one.
- **A release is published to its own channel, `releases`, beside `snapshots`**, in the same
  repositories and signed with the same key. It is built from a tag `v<version>` that must match the
  version; a version already released is never uploaded again; `main` keeps publishing to
  `snapshots`. A machine chooses by one word, and `releases` is the default in the README and
  `sokar-setup.sh`. Java artifacts go to Maven Central's staging, where the operator checks and
  releases them by hand.
- **A release waits for every leg; a snapshot does not.** On a tag, Publish needs every job of the run,
  the legs on rented machines included, since a release is never overwritten and is what people
  install. On `main`, a snapshot may publish once the build, the tests and the install check pass,
  beside a leg still running, so a snapshot is not held by a rented machine.
- **Everything is prepared on `main`, and only the version waits.** The changelog, the docs and
  the release workflow go onto `main` as they are ready. Every version line is `0.4.0-SNAPSHOT`
  (packages `0.4.0~snapshot.N`, which orders below `0.4.0`) until the operator approves the
  release; only then is it set to `0.4.0`. No release branch is kept.

## Acceptance

- Each repository's `main` is its first commit and one "Initial public version" commit.
- Each changelog holds only "Initial public version".
- Each repository's documentation has been read and accepted by the operator, and each `AGENTS.md`
  reviewed by the operator.
- Release 0.4.0 is published, installable from the index on a fresh machine, and a new machine set up
  with the wizard gets it.
- No secret is found in any history that becomes public.
