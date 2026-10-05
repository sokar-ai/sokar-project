# A09 — Agent Grok Build

**Status:** later; a candidate.

**What must be true.** Grok Build runs in a task as a packaged agent, signed in and brokered like the
supported ones, with its real credential never in the container.

## Why

**Upstream.** https://github.com/xai-org/grok-build - Rust rather than npm, installed from
`x.ai/cli`, so it pins differently from every agent listed here so far.

**Providers.** One vendor, whose model is also on its public API.

**Authentication.** A subscription account.

**Can it be brokered?** Unverified.

## Acceptance

- It ships as its own binary and its own package, in a repository of its own, discovered without
  Sokar changing.
- Nothing outside its own directory names it, enforced by the build.
- A task authenticates without anyone signing in inside the container.
- The real credential is never in the container, seen by searching the container for it.

## To be checked

- Whether a subscription account yields anything storable, or only a session.
- Too recent for usage to mean anything; revisit before spending effort on it.
