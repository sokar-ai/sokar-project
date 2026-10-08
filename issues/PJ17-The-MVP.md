# PJ17 — The MVP

**Status:** now; each repository sorts its own index by it.

**What must be true.** One person, one machine, one agent: Sokar is installed, work is started, the
agent works safely, its result is reviewed and pushed - smoothly, from the interface as much as from
the command line, for someone who has never used Sokar. Everything that serves that is **now**; what
follows right after it is **soon**; the rest is **later**.

## The shape

1. **The standard pair is Claude Code with Anthropic, signed in with a Claude subscription** - the
   browser login the interface runs in its login terminal, so no key is typed anywhere. An Anthropic
   API key, Pi, Oh My Pi and the other providers work, and are polished after it.
2. **Messages over the local Matrix homeserver are part of it:** tasks of a project talk in the
   project's room on the machine's own homeserver. A central homeserver for several machines is
   later.
3. **A project from a repository a person already has is part of it**, so nobody writes a
   `project.yml` by hand to begin; so is work without a project (PJ14).
4. **A rented machine set up from the interface behaves like a local one.** The same steps, the same
   result; that a machine is remote changes nothing for the person.
5. **The person using it is new to Sokar:** anything that needs Sokar's own words, a terminal where
   the interface should do, or a step not said, is a defect of the MVP.
6. **A built requirement that only waits for a measurement keeps its group**: the measurement is
   part of the work.

## Now

- **`sokar`:** B102, B76, B121, B122, B125, B126, B127, B128.
- **`sokar-frontend`:** F79, F71, F68, F69.
- **`sokar-claude-code`:** CC25. **`sokar-pi`:** PI22. **`sokar-omp`:** OM17.
- **`sokar-intellij`:** IJ01.
- **`sokar-project`:** PJ14, PJ28.

## Soon

- **`sokar`:** B108, B98, B99, B100, B95, B79, B117, B118, B119, B120, B51, B55, B50, B13, B40, B10, B34, B48,
  B33, B26, B49, B30, B31, B46.
- **`sokar-frontend`:** F101, F100, F99, F93, F91, F94, F95, F96, F97, F77, F78, F74, F59, F81, F84.
- **`sokar-buildtools`:** BT01.
- **`sokar-claude-code`:** CC23.
- **`sokar-message-sluice`:** SL07, SL23.
- **`sokar-ai.github.io`:** SI01.
- **`sokar-project`:** PJ27, PJ29, PJ15, PJ08, PJ09, PJ22.

## Later

- **`sokar`:** B123, B112, B101, B86, B14, B38, B18, B06, B15, B23, B41, B56, B59, B63, B116; providers P09,
  P05-P08.
- **`sokar-frontend`:** F88, F64, F75, F72, F37, F52, F62.
- **`sokar-claude-code`:** CC22, CC24. **`sokar-pi`:** PI21. **`sokar-omp`:** OM18, OM19, OM24.
- **`sokar-message-matrix`:** MX12.
- **`sokar-message-sluice`:** SL08, SL04, SL12.
- **`sokar-project`:** PJ16, PJ03, PJ24, PJ25; candidate agents A06-A10.

## Acceptance

- Every open issue of every repository is in exactly one of the three groups here, as its own index
  has it, and no number here names an issue that no longer exists.
- Everything in *Now* is built, measured and released in 0.4.0.
