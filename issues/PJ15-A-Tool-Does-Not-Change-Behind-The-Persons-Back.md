# PJ15 — A tool does not change behind the person's back

**Status:** soon; the measurement of whether the broker carries an MCP session comes first.

**What must be true.** What an MCP server tells an agent about its tools is something a person has
seen and accepted, and a server that changes it afterwards - a rug pull - is stopped until a person
accepts the change. A tool description written to steer the agent is caught before it reaches it.

## Why

**Tool poisoning** puts instructions into a tool's description or answers, which the agent reads and
may follow; **a rug pull** does the same later, after the server was approved.

What Sokar does already limits the damage: no real key in the container (the broker attaches it, and
refuses a request that would mint a token of its own); egress denied by default; work leaving only
through the gate in a `guarded` project; messages passing the filter; a followed project's
configuration signed.

What it does not do: Sokar never sees a tool's definition (the broker passes `tools/list` through
unread); nothing is pinned or compared; the MCP server is itself an allowed destination, which is the
main channel left; an MCP server started inside the container is neither pinned nor checked.

## The shape

1. **Tool definitions pinned**: the broker hashes a server's `tools/list`; a person accepts it once, as
   a grant is accepted (`sokar` B31); a later change holds the session and asks under *Needs you*,
   showing what changed.
2. **Tool descriptions checked** by fixed rules of the filter's kind - hidden instructions, invisible
   or look-alike Unicode, encoded payloads - before the agent receives them; deterministic, not
   another model.
3. **An MCP server inside the container only from a package the project pins.**

The broker then has to understand MCP rather than pass bodies through unread: a deliberate design
change. **Where the work lands:** the broker in `sokar`; the description rules in
`sokar-message-sluice` or a catalog of their own; acceptance under *Needs you* in `sokar-frontend`;
how agents start MCP servers in the agent repositories.

## Acceptance

- Whether the broker carries an MCP session is measured first, and the answer is written down.
- A server whose `tools/list` changes after acceptance holds its session until a person accepts the
  change, shown what changed; an unchanged set asks nothing.
- A description carrying a hidden instruction, an invisible character or an encoded payload is
  caught before the agent sees it, and each check is seen to fail on a crafted description.
- An MCP server inside the container starts only from a package the project pins.

## To be checked

- Whether the description rules are the filter's own catalog, or separate.
- Whether a changed tool set holds the session (safe, but stops work) or only flags it.
