# PJ03 — An attachment may be permitted

**Status:** later.

**What must be true.** Whether a message may carry something other than text is a decision an
operator makes, per direction, per project and per peer - not a constant compiled into the filter.

## Why

The envelope refuses every part that is not text: a part with `raw` or `data` is blocking, one with
`url` blocking or suspicious by `check.urlPartSeverity`. A2A 1.0 already carries the field - its
`Part` holds exactly one of `text`, `raw`, `url` or `data` - so the format does not grow; what is
missing is the policy that would permit one, and `check.urlPartSeverity` shows the kind of setting it
is.

**The two directions are two questions.** The filter stops the agent from sending data out; a person
sending a file to an agent is not what it exists to stop. So inbound and outbound are separate
settings, and outbound is the one that carries the weight.

## The shape

- **Per project and per peer, in both directions**: a project-level switch deciding whether
  attachments may pass at all, and a per-peer entry deciding which peers. A project-wide switch is
  what an operator turns off when something has gone wrong; the per-peer entry expresses "this agent
  may send files to the reviewer and to nobody else".
- **The host decides, never the agent.** A task that writes a `raw` part where none is permitted is
  held, not corrected, as a task writing `ROLE_USER` for itself is.
- **The default is today's behaviour**: every non-text part refused.
- **A permitted attachment is fetched by the host from a declared host** - over Matrix an `mxc://` on
  the homeserver in `describe.hosts`, never an arbitrary URL and never from inside a container.
- **The detectors still see what they can see**: file names and media types go through them.
- **The size limit is the transport's** (`describe` reports `max_bytes`); Sokar sets no second one,
  and the limit in force is shown by `check` and `sokar doctor`, and named in a refusal for size with
  where it came from.
- **Where the work lands:** the severity settings and envelope rules in `sokar-message-sluice`; the
  permission, the host-side fetch and the held-with-a-reason path in `sokar`. The transports carry
  bytes and decide nothing.

## Acceptance

- Whether `raw`, `url` and `data` parts are permitted is configuration, per direction, per project
  and per peer, with every one refused by default.
- A person can send a file to a task where an operator permitted it, and the task receives it.
- An agent cannot send one out unless an operator permitted that separately; an attempt without the
  permission is held with a reason naming what is missing.
- A `url` part resolving to anything but the declared host is refused at every severity.
- Each new setting is tested with the permission off and on, and a test that passes both ways is
  seen to fail.
- The exposure, why it is taken and what would change the answer are in `doc/decisions.md`.
