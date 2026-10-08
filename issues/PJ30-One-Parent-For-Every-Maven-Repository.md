# PJ30 — One parent for every Maven repository

**Status:** now; before the next push. Spans every Maven repository; the parent is a repository of its own,
`sokar-parent` (`git@github.com:sokar-ai/sokar-parent.git`, issue prefix `PA`).

**What must be true.** Every Sokar Maven repository takes one parent, `org.fuin.sokar:sokar-parent`, which holds
what they share - and `sokar-bom` manages only the versions of what `sokar` itself publishes.

## Why

Every repository takes `org.fuin:pom` as its parent, a parent from outside the project with a release cycle of its
own, and then repeats in its own `pom.xml` what that parent does not give: flatten, the Java release, the encoding,
the plugin versions, `skipPublishing` and `maven.deploy.skip`, the `ci-tools` profile, `sokar.buildtools.version`.
The repetition drifts, and one repository took the BOM as its parent to get some of it.

`sokar-bom` today also manages the build tools (`sokar-release`, `sokar-machines`) at its own version, so whoever
imports it and wants newer tools has to override that with a property of its own. A BOM that manages what it does
not publish is pinned by the wrong release.

## The shape

- **`sokar-parent`, a repository of its own**, holding only the parent pom: what `org.fuin:pom` sets today,
  copied in, so it no longer inherits from it; the Java release, the encoding, the managed plugin versions;
  flatten for every module that publishes; `skipPublishing` and `maven.deploy.skip` `true` unless a module opts
  in; the `ci-tools` profile; `sokar.buildtools.version`; `junit-bom`. **It imports no other BOM**: a BOM in the
  parent decides every library version a repository does not name, under its own dependencies; a repository that
  wants one imports it itself, after `junit-bom`.
- **It uses nothing of another Sokar repository**, so it is built and published before all of them: as a
  snapshot to Sonatype while the others move onto it, then released to Maven Central first in every release
  chain. Its own pom names no parent.
- **Every Maven repository takes it as its parent** and drops what it now inherits. No `groupId` or `artifactId`
  changes; only the `<parent>` and what it no longer repeats.
- **`sokar-bom` stays in `sokar`** and manages only the versions of the artifacts `sokar` publishes together
  (`sokar-wire`, `sokar-agent-api`, `sokar-build-api`, `sokar-acceptance-kit`), so it is released with them. The
  build tools' versions move to `sokar-parent`.
- **The shared rules** say which parent a Maven repository takes, beside "A BOM is imported, never a parent".

## Acceptance

- Every Maven repository's parent is `sokar-parent`, and none inherits from `org.fuin:pom`. Seen to fail: a
  search of every `pom.xml` for `<artifactId>pom</artifactId>` under `<parent>` finds one.
- Each repository's effective pom is, apart from the parent's own coordinates, what it was before: the same
  dependency and plugin versions, the same publishing flags, every module's coordinates unchanged. Seen to fail:
  a list taken before and after differs.
- `sokar-bom` manages nothing `sokar` does not publish. Seen to fail: its flattened pom names `sokar-release`,
  `sokar-machines` or a third-party artifact.
- Each repository's resolved dependencies (`dependency:list`, transitive, every scope and profile) are what they
  were before. Seen to fail: a version that moved, or one library family split across versions.
- `sokar-parent`'s released pom names no parent and no snapshot, and every repository builds against the
  released parent before it pushes. Seen to fail: a release of it with a snapshot version in it.
