# PJ21 — One documentation site for all of Sokar

**Status:** now; built, checked and public at `https://sokar-ai.github.io`. Left: every repository's
chapter on it (`github` once `sokar-build-github` is pushed), and proof that a change to a repository's
`doc/` reaches the site by its next build.

**What must be true.** A person finds the documentation of every part of Sokar - the CLI and daemon,
the interface, each agent, the message transport and the filter - on one site,
`https://sokar-ai.github.io`, in one order, and it is current.

## Why

Each repository documents itself in its own `doc/`, where its code is. A site built from one of them
misses the others, and is not rebuilt when they change.

## The shape

- **Each repository keeps its documentation in its own `doc/`.** It is written and checked where the code
  is; `sokar`'s strict mkdocs build stays as the check of its own pages.
- **The site is built in `sokar-ai/sokar-ai.github.io` itself**, by a workflow there. It reads the list of
  repositories from `sokar-ai/sokar-project`'s `project.yml`, fetches each one's `doc/`, builds one mkdocs
  site with a section per part, and publishes it with GitHub Pages ("GitHub Actions" as the source).
- **When it is built:** nightly, by hand, and when any repository tags a release. A release's
  documentation is the one published; the snapshot state is not.
- **Built and checked from now, published after PJ18:** until the repositories are public, the workflow
  reads them with one read-only token (`SOKAR_DOCS_READ`, Contents: read) and builds and checks the whole
  site without publishing it. After PJ18 publishing is switched on, and the token is deleted: public
  repositories need none. No token that writes anywhere.
- **As in every repository:** actions pinned to commits, a time limit per job, mkdocs and its plugins
  pinned by hash, Dependabot, and the shared `AGENTS.md` block.

## Acceptance

- The site shows every repository's documentation in one order, each section from that repository's
  `doc/` at the commit or release it was built from.
- A change to any repository's `doc/` reaches the site by the next build, and a release by its tag.
- The workflow holds no token that can write anywhere but its own Pages.
- After PJ18, the site is public at `https://sokar-ai.github.io`; `SOKAR_DOCS_READ` stays, read-only.
