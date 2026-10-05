# PJ22 — Rented machines behind one interface

**Status:** soon. Spans `sokar-buildtools` (the tooling that rents) and every repository whose build
or rules name the provider.

**What must be true.** Every repository rents a machine - for a build's acceptance legs, a snapshot or
a special test before a push - only through the tooling the repositories share, and nothing outside
that tooling knows which provider the machine comes from. A rule, a document, a workflow or a test
speaks of a *rented machine*, never of the provider.

## Why

The provider is one setup's choice, not the project's. Today it is named across the repositories:

- `sokar-buildtools` keeps the tooling in a directory `hetzner/`, and its README and `AGENTS.md` name
  the provider;
- `sokar`'s, `sokar-frontend`'s and `sokar-message-sluice`'s `AGENTS.md` speak of it, and every agent's
  `.AGENTS.md` carries a chapter named after it;
- the coordination rule "at most five rented machines at once" is phrased as the provider's limit.

A second provider, or another account with other limits, would mean changing every one of these places,
and a rule naming the provider is wrong for any setup that rents elsewhere.

## The shape

- **One interface in `sokar-buildtools`** (`sokar-machines`): rent a machine of a kind, run on it,
  return it, list what this run holds, delete what a run left behind. Callers ask for a kind of machine
  (distribution, CPU level, size), never for a provider's server type or location.
- **The provider sits behind it**, chosen by the setup's configuration and secrets, not by the caller.
  The directory, the artifact's description and its documentation say "rented machines"; the provider's
  name appears only in the one implementation that talks to it.
- **The limits are the setup's**: how many machines may be rented at once is configuration of the
  interface, and the interface itself waits or refuses rather than every agent counting by hand.
- **Rules and documents name no provider**: the shared block of `AGENTS.md` (already so), each
  repository's `AGENTS.md`, `README.md` and `doc/`, and the chapters of `.AGENTS.md`.

## Acceptance

- No repository outside the interface's implementation names the provider, in code, workflows, rules
  or documents (a check in `sokar-buildtools` finds a name that slips in).
- Every build that rents does so through the interface, and its acceptance legs run as before.

## To be checked

- **What the interface offers**: is "a kind of machine" enough (distribution, CPU level, size), or do
  callers need more (a snapshot of their own, a GPU)?
- **Where the limit is kept**: in the interface (it queues or refuses past the limit), or still by
  coordination in the channel?
- **Whether more than one provider is in scope** for the MVP, or only the renaming and the interface,
  with one implementation behind it.
