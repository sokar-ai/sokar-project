# A06 — Agent Codex CLI

**Status:** later; a candidate.

**What must be true.** Codex CLI runs in a task as a packaged agent, signed in and brokered like the
supported ones, with its real credential never in the container.

## Why

**Upstream.** https://github.com/openai/codex, npm `@openai/codex`.

**Providers.** One vendor.

**Authentication.** An account sign-in or an API key.

**Can it be brokered?** Unverified.

## Acceptance

- It ships as its own binary and its own package, in a repository of its own, discovered without
  Sokar changing.
- Nothing outside its own directory names it, enforced by the build.
- A task authenticates without anyone signing in inside the container.
- The real credential is never in the container, seen by searching the container for it.

## To be checked

- Whether the endpoint can be redirected at all.
- Whether the account sign-in verifies a session before use, as the supported agent
  does.
