# PJ09 — A gate's green says nothing until its reference set can change

**Status:** soon.

**What must be true.** Every gate says what it compares against, and whether that reference set is
able to change. A gate whose reference set cannot change, is empty, or is read by nothing is named as
such rather than counted as coverage.

## Why

Four gates in four repositories are green in the same way, and only one of them can report anything:

    a reference set that is EMPTY        everything passes forever          sokar-message-sluice
    one that CANNOT CHANGE               libraries that never move          sokar-omp, sokar-claude-code
    one that NOBODY READS                a bill shipped, compared by no job sokar
    one that WORKS                       a licence gate that fired          sokar-pi

Nothing in the output distinguishes them. The working gate is what made the other three measurable:
green cannot be calibrated without one gate that has ever gone red.

## The shape

- **Empty:** an assertion that the walk found anything at all.
- **Cannot change:** the gate says so where it reports (`sokar-omp`'s does; `sokar-claude-code`'s
  reads as working), or is removed - each agent's call, depending on whether a real reference set is
  coming.
- **Nobody reads:** `sokar` ships and uploads a bill that no job of its own compares, while
  `buildtools/check-packages.sh` justifies shipping it by a comparison that exists only in the agent
  repositories. The sentence is corrected, and whether a comparison job is added there is the same
  operator decision as PJ08's.
- **The coordinating agent's standing duties** (the shared-block diff, cross-repository citations,
  stale statuses, renames) are duties, not gates, and each is counted as coverage only once it has
  fired.

## Acceptance

- Every gate in every repository names its reference set and what would make that set change.
- A gate whose reference set cannot change, is empty, or is read by nothing says so where it reports,
  and is counted as coverage nowhere.
- No justification in any repository asserts a check that repository does not perform.
- Each kind has at least one gate seen to fire.
