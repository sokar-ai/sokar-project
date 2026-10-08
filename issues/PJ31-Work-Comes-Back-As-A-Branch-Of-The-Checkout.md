# PJ31 — Work comes back as a branch of the checkout it started from

**Status:** now; before the next push. The work is `sokar`'s.

**What must be true.** A person who started a task in a checked-out repository brings the agent's work back into
that same checkout, as a branch, with one short command typed in that checkout - after seeing what comes and
saying yes.

## Why

The way exists, but its command is too long for anybody to remember:

    sokar gate approve -p default <task> --upstream /path/to/the/checkout --branch sokar/<task>

Everything in it but the command itself is known already when the person stands in the checkout the task started
from: the project is `default`, the task is the one started there, the upstream is this checkout, the branch is
named after the task. Without `--upstream` it even goes to the checkout's `origin` - a forge, usually - which is not
where a person working alone expects it.

Leaving the task is no signal to bring anything back: a person leaves to come back later and work on.

## The shape

- **One short command, typed in the checkout**, e.g. `sokar approve` (the name is `sokar`'s to choose, short and
  memorable). From the current directory it derives: the project (`default`, or the project this checkout belongs
  to), the task started from this checkout, the upstream (this checkout), and the branch `sokar/<task>`.
- **It shows what comes** - the commits waiting at the gate, the files and a short diff - and asks; only yes brings
  it back. A flag answers yes for scripts.
- **Several tasks from this checkout:** it names them and asks which one, or takes the task's name as an argument.
  None, or nothing waiting at the gate: it says so and does nothing.
- **The agent commits and pushes to the gate**, as it does today; the guide every task is given says so. The
  command brings back what is at the gate, never what is only in the container.
- **Never onto the branch checked out there**, which git refuses anyway; a `sokar/<task>` that exists already moves
  only when it fast-forwards, and otherwise the person is told and nothing moves.
- **Nothing reaches a forge through it.** The long `sokar gate approve` stays for every other case.

## Acceptance

- In the checkout a task started from, with work at the gate: the short command shows the commits and the diff,
  and after yes the checkout has a branch `sokar/<task>` holding them; its working copy and checked-out branch are
  untouched. Seen to fail: after yes, no such branch, or a changed working copy.
- After no, nothing changes, and the work is still pending at the gate.
- Nothing waiting, or no task from this checkout: it says so, exit non-zero, nothing changes.
- Two tasks from the checkout: it asks which, or takes the name given.
- A `sokar/<task>` that does not fast-forward is not overwritten, and nothing goes to `origin` or any remote of the
  checkout. Seen to fail: either happens.
- The guide tells the agent to commit and push its work to the gate when it is done.
