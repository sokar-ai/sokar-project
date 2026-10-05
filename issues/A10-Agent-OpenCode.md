# A10 — Agent OpenCode

**Status:** later; a candidate.

**What must be true.** OpenCode runs in a task as a packaged agent, signed in and brokered like the
supported ones, with its real credential never in the container.

## Why

**Upstream.** https://github.com/sst/opencode.

**Providers.** Many, chosen per session.

**Authentication.** Per provider: a pasted key in one store, a browser sign-in, or an environment
variable. A stored value may be a reference to a variable rather than a literal.

**Can it be brokered?** **Documented.** A base URL is configurable per provider.

## Acceptance

- It ships as its own binary and its own package, in a repository of its own, discovered without
  Sokar changing.
- Nothing outside its own directory names it, enforced by the build.
- A task authenticates without anyone signing in inside the container.
- The real credential is never in the container, seen by searching the container for it.

## To be checked

- The reference-to-a-variable form would need no file placed in the container at
  all, which would be simpler than what the supported agent needs. Worth confirming
  before building anything.
- One credential per provider, which the vault cannot express today.
