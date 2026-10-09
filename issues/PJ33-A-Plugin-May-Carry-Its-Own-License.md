# PJ33 — A plugin may carry its own license

**Status:** soon; the license is decided: Apache-2.0 for the parts others link.

**What must be true.** Whoever writes an agent, a build reader, a messaging transport or a client for
Sokar may license it as they choose, proprietary included, while Sokar itself stays under the GPL
v3 or later. The documentation says where that boundary is.

## Why

Sokar promises that adding an agent - "including a proprietary agent that will never be upstreamed" -
means adding a directory, not patching Sokar (`sokar` `doc/why.md`). Today that holds only for some
kinds of plugin:

| Plugin | What it takes from Sokar | Sokar code in the plugin |
|---|---|---|
| Messaging transport | the protocol in `sokar` `doc/transports.md` (`describe`, JSON on standard input and output, exit codes, secrets as environment variables) | none |
| Client (an interface, an IDE plugin) | the varlink interface definitions and the socket | none, unless it uses `sokar-wire` |
| Agent | `sokar-agent-api` and `sokar-wire`, started through `AgentMain` (`sokar` `agents/README.md`) | compiled in, also into the native image |
| Build reader | `sokar-build-api` (`BuildMain`) and `sokar-wire` | compiled in |
| Build tooling (`sokar-parent`, `sokar-release`, `sokar-machines`) | used while building and checking | none in what ships |

Programs that talk only through pipes, sockets, files or command lines are, as the FSF reads the
GPL, separate works. A program that links a GPL library and is distributed is a combined work and
must be offered under a GPL-compatible license. So an agent or a build reader built the documented
way is bound to the GPL today, and a transport or a client is not.

All of Sokar is GPL-3.0-or-later today, with no exception and no differently licensed module.

## The shape

1. **The parts others link under Apache-2.0:** `sokar-agent-api`, `sokar-build-api`, `sokar-wire` and
   the `.varlink` interface definitions. Everything else in Sokar stays GPL-3.0-or-later. A plugin may
   then carry any license, and a plugin built as one native image needs nothing further: Apache-2.0
   asks for the license and notices to be kept, not for the library to be replaceable, which the LGPL
   would ask of a statically linked native image. Apache-2.0 code may be combined into GPL-3.0 work,
   so Sokar itself keeps using these libraries unchanged.
2. **The boundary written down**, in `sokar`'s documentation and its agent and transport guides: the
   protocols - the transport JSON, the varlink interfaces, the agent and reader descriptions - may be
   implemented by anyone; a plugin that only talks through them is a work of its own.
3. **Each artifact names its license** in its POM, its packages and its source headers, and the
   build checks that a module of the interface libraries depends on nothing GPL- or LGPL-only.

Changing the license of existing code needs every contributor's consent; today that is one person,
and every outside contribution makes it harder, so this comes before contributions are invited
(`sokar-project` PJ27).

## Acceptance

- Apache-2.0 is applied to each interface library, checked with legal advice: `LICENSE` and `NOTICE`
  in the module, the POM's `<licenses>`, the packages, the source headers.
- `sokar`'s documentation states the boundary in one place, linked from the agent, reader and
  transport guides.
- A build check fails when an interface library gains a dependency that is GPL- or LGPL-only.
