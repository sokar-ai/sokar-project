# Project requirements

**What belongs here.** A requirement belongs in this repository while it is about the project rather
than one of its repositories: the shape of the product, a decision that spans several repositories,
or something still too unclear to say which repository would build it; and the candidate agents
(`A`) that have no repository yet.

**A requirement leaves.** Once it is clear which repository does the work, it moves there as an
issue, a handover rather than a copy.

Within each group, ordered by what to do next; the number is identity, not sequence.

## Now

| # | Status | Blocked by | What it covers | Open questions |
|---|---|---|---|---|
| [PJ17](PJ17-The-MVP.md) | now | — | The MVP: one person, one machine, Claude Code with Anthropic, local Matrix, a project from a repository - what is now, soon and later in every repository. | 0 |
| [PJ14](PJ14-Work-Starts-Without-A-Project.md) | now; built, three points left | — | Work starts in a checked-out or picked repository without a project, in the project `default`; left: refusals naming the way for a transport or a peer, a `project.yml` named `default`, and the move to a followed project measured. | 0 |
| [PJ21](PJ21-One-Documentation-Site-For-All-Of-Sokar.md) | now; built, publishing waits for PJ18 | — | One documentation site, `https://sokar-ai.github.io`, built in its own repository from every repository's `doc/`. | 0 |
| [PJ18](PJ18-Sokar-Is-Ready-To-Be-Made-Public.md) | now; last | — | Every repository ready to be made public: documentation cut, finished issues deleted, the changelog and the history one "Initial public version", the first release 0.4.0. | 0 |

## Soon

| # | Status | Blocked by | What it covers | Open questions |
|---|---|---|---|---|
| [PJ15](PJ15-A-Tool-Does-Not-Change-Behind-The-Persons-Back.md) | soon | — | An MCP server's tools are pinned when a person accepts them, a change is held until accepted again, and descriptions written to steer the agent are caught. | 2 |
| [PJ08](PJ08-An-Unattended-Jobs-Refusal-Reaches-A-Person.md) | soon | — | An unattended job's refusal reaches a person. | 2 |
| [PJ09](PJ09-A-Gates-Green-Says-Nothing-Until-Its-Reference-Set-Can-Change.md) | soon | — | Every gate names its reference set; one that is empty, cannot change or is read by nothing is not counted as coverage. | 0 |
| [PJ20](PJ20-Work-Is-Reviewed-In-IntelliJ.md) | soon | — | An IntelliJ plugin in a new repository `sokar-intellij`: what waits at the gate reviewed in IntelliJ, with no project and no working tree, approved or rejected through the daemon's contract. | 0 |
| [PJ22](PJ22-Rented-Machines-Behind-One-Interface.md) | soon | — | Every repository rents machines only through `sokar-buildtools`' shared interface, which hides the provider; the setup's limits live in the interface. | 3 |

## Later

| # | Status | Blocked by | What it covers | Open questions |
|---|---|---|---|---|
| [PJ16](PJ16-An-Organisation-Decides-Which-Providers-Its-Machines-Use.md) | later | — | A root-owned machine policy names the providers tasks may use, and narrows follows, prompts and the `online` class; a developer cannot widen it. | 0 |
| [PJ03](PJ03-An-Attachment-May-Be-Permitted.md) | later | — | Whether a message may carry more than text becomes an operator's decision, per direction, project and peer, refused by default. | 0 |
| [A06](A06-Agent-Codex-CLI.md) | later | — | Codex CLI as a packaged agent: a candidate, brokering unverified. | 2 |
| [A07](A07-Agent-Gemini-CLI.md) | later | — | Gemini CLI as a packaged agent: a candidate, brokering unverified. | 2 |
| [A08](A08-Agent-Copilot-CLI.md) | later | — | Copilot CLI as a packaged agent: brokerable by environment variable (BYOK), which is not the Copilot subscription. | 2 |
| [A09](A09-Agent-Grok-Build.md) | later | — | Grok Build as a packaged agent: a candidate, brokering unverified. | 2 |
| [A10](A10-Agent-OpenCode.md) | later | — | OpenCode as a packaged agent: a base URL configurable per provider. | 2 |
| [PJ24](PJ24-Where-The-Agent-Protocols-Touch-This.md) | later | — | For A2A, MCP, ACP and AG-UI each: adopted, answered otherwise, or refused, with the reason and what would change it. | 6 |
| [PJ25](PJ25-McSokar-Apple-Containers.md) | later | — | McSokar: the same guarantees on Apple Containers, driven by one client through the daemon's contract. | 6 |

## What was decided along the way

- **Messaging is not one repository's job.** The format is A2A 1.0, an external standard; what
  remains divides into the handover at the container boundary (`sokar`), what may pass (the filter)
  and the carrying (the transports).
- **An adapter's repository is an ownership question, not an architectural one.** Transports are
  discovered executables speaking a CLI, so nothing compiles against one and moving it breaks nothing.
- **No project-wide message store**: a Matrix room is one.
- **No chat client is shipped.** Any Matrix client will do, and no agent needs one.
- **Room membership is not authorization.** Matrix distributes; Sokar decides, on the signature.
