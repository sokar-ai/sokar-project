# Project requirements

**What belongs here.** A requirement belongs in this repository while it is about the project rather
than one of its repositories: the shape of the product, a decision that spans several repositories,
or something still too unclear to say which repository would build it; and the candidate agents
(`A`) that have no repository yet.

**A requirement leaves.** Once it is clear which repository does the work, it moves there as an
issue, a handover rather than a copy.

Within each group, ordered by what to do next; the number is identity, not sequence.

**How Sokar measures up against security guidelines** is in [security-guidelines.md](security-guidelines.md); every gap to be closed links its issue there.

## Now

| # | Status | Blocked by | What it covers | Open questions |
|---|---|---|---|---|
| [PJ17](PJ17-The-MVP.md) | now | — | The MVP: one person, one machine, Claude Code with Anthropic, local Matrix, a project from a repository - what is now, soon and later in every repository. | 0 |
| [PJ14](PJ14-Work-Starts-Without-A-Project.md) | now; built, three points left | — | Work starts in a checked-out or picked repository without a project, in the project `default`; left: refusals naming the way for a transport or a peer, a `project.yml` named `default`, and the move to a followed project measured. | 0 |
| [PJ28](PJ28-Code-From-Outside-Reaches-No-Secret.md) | now; the repositories are public | — | Before the repositories are public: `main` protected with the operator as the only bypass, `CODEOWNERS` for workflows, build files and rules, a fork's workflow waits for approval, and `check-actions` refuses `pull_request_target`. | 1 |

## Soon

| # | Status | Blocked by | What it covers | Open questions |
|---|---|---|---|---|
| [PJ27](PJ27-A-Contributor-Knows-The-Rules.md) | soon | — | One guide for contributors in `sokar-project`'s `doc/`, linked from every repository's README and `CONTRIBUTING.md`: how the work is organised, how a change is made, what it must satisfy. | 2 |
| [PJ29](PJ29-One-Code-Style-For-IDE-And-Build.md) | soon | — | One shared `.editorconfig` with the Google style in every repository, followed by the IDE and checked by the build with Spotless and `google-java-format` (AOSP: four spaces, 100 columns) in `validate`; IntelliJ with the `google-java-format` plugin. | 0 |
| [PJ33](PJ33-A-Plugin-May-Carry-Its-Own-License.md) | soon; Apache-2.0 decided | — | An agent, build reader, transport or client may carry its own license: the interface libraries (`sokar-agent-api`, `sokar-build-api`, `sokar-wire`, the varlink definitions) under Apache-2.0, Sokar itself GPL, the boundary written down. | 0 |
| [PJ15](PJ15-A-Tool-Does-Not-Change-Behind-The-Persons-Back.md) | soon | — | An MCP server's tools are pinned when a person accepts them, a change is held until accepted again, and descriptions written to steer the agent are caught. | 2 |
| [PJ08](PJ08-An-Unattended-Jobs-Refusal-Reaches-A-Person.md) | soon | — | An unattended job's refusal reaches a person. | 2 |
| [PJ09](PJ09-A-Gates-Green-Says-Nothing-Until-Its-Reference-Set-Can-Change.md) | soon | — | Every gate names its reference set; one that is empty, cannot change or is read by nothing is not counted as coverage. | 0 |
| [PJ22](PJ22-One-Build-Locally-As-On-The-Central-Forge.md) | soon | — | One build, run locally as on the central forge: the same workflows on a local Forgejo, its registry instead of the hand-over and the public repositories, acceptance legs on a machine rented through one interface (a local VM account or a provider's server), and a task's work built the same way, read through one reader contract by a `github` and a `forgejo` reader. | 0 |

## Later

| # | Status | Blocked by | What it covers | Open questions |
|---|---|---|---|---|
| [PJ32](PJ32-A-Vulnerability-Has-A-Way-In.md) | later | — | Whoever finds a vulnerability reports it privately through a `SECURITY.md` in every repository and one page in `doc/`; a fix gets a GitHub advisory and a changelog line. | 0 |
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
