# A08 — Agent Copilot CLI

**Status:** later; a candidate, its brokering answered.

**What must be true.** GitHub Copilot CLI runs in a task as a packaged agent, signed in and brokered
like the supported ones, with its real credential never in the container.

## Why

**Upstream.** https://github.com/github/copilot-cli, npm `@github/copilot`. One subscription, which
itself fronts models from several vendors.

**Measured, from a real sign-in with a personal subscription on a VM:**

- **It can be brokered by environment variable.** `COPILOT_PROVIDER_BASE_URL` (with
  `COPILOT_PROVIDER_TYPE`, `COPILOT_PROVIDER_API_KEY`, `COPILOT_MODEL`) switches it to a custom
  provider (BYOK) and turns GitHub authentication off - the easy shape, like the first agent's. But
  **BYOK is not the Copilot subscription**: building it reaches no new provider and not the Copilot
  subscription Sokar already brokers as a provider.
- **Sign-in has three modes**: a browser loopback redirect with PKCE (default on a desktop), a device
  code (default in SSH, containers and CI, though detection chose the web flow in a container over
  SSH), and a token on standard input (`--with-token`). `COPILOT_GITHUB_TOKEN`, `GH_TOKEN` and
  `GITHUB_TOKEN`, in that order, outrank anything stored. So the sign-in never has to happen in a
  task: a credential is obtained on the host and given to the broker.
- **What it stores**: one file, `~/.copilot/config.json`, mode 0600, holding a `gho_…` token in
  plaintext; no refresh token, no expiry. **A fine-grained PAT with the "Copilot Requests"
  permission is accepted** and is the better credential: the sign-in's token also carries `repo`,
  `gist`, `codespace` and `read:org`.
- **The model API is reached only after an exchange at `api.github.com`**, the same number of calls,
  with nothing new written to disk. So the credential Sokar swaps in is presented at `api.github.com`,
  and the short-lived token that comes back is held by the agent: a task would hold a real credential,
  scoped to Copilot and short-lived, for the requests that cost money - weaker than every other
  provider, and to be stated plainly.
- **Hosts contacted:** `api.individual.githubcopilot.com` (the model API), `api.github.com`,
  `telemetry.individual.githubcopilot.com`, `exp.individual.githubcopilot.com`, and `github.com` for
  the sign-in.
- **It updates itself by default** (`COPILOT_AUTO_UPDATE`), so any definition turns it off, or a
  pinned version would silently run another one.

## Acceptance

- It ships as its own binary and its own package, in a repository of its own, discovered without
  Sokar changing.
- Nothing outside its own directory names it, enforced by the build.
- A task authenticates without anyone signing in inside the container.
- The real credential is never in the container, seen by searching the container for it; where the
  subscription leaves a short-lived token in the agent, that is said in its documentation.
- Auto-update is off in its definition, and the version that runs is the one pinned.

## To be checked

- Which model names the subscription serves, since BYOK requires an explicit model.
- Whether `telemetry.individual.githubcopilot.com` can be refused without breaking it.
