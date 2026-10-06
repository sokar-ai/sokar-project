# PJ29 — One code style for the IDE and the build

**Status:** soon. Spans every repository with code; the shared tooling is `sokar-buildtools`'.

**What must be true.** Every repository states its code style once, in a committed `.editorconfig`
following the Google style guides, and the IDE formats by it while the build refuses code that does
not match it - so a change never carries formatting noise, and nobody's local settings decide how
the code looks.

## Why

Today the style lives in nobody's file. The Java code is consistent - four-space indents, lines up
to 120 columns - only because every agent copies what it sees; an IDE with other defaults, or a
contributor from outside, reformats what they touch. The rule "only the files you edited are
formatted" limits the damage but cannot prevent it, and nothing checks it.

## The shape

- **`.editorconfig` at every repository's root, one shared content**, the source for the IDE: charset,
  line endings, final newline, trailing whitespace, indent and line length per file type. The file
  is identical everywhere, like the shared rules block, and its copies are compared.
- **Java follows the Google Java Style in its AOSP variant**: four-space indents, lines wrapped at
  100 columns. Every Java file here already indents by four; the 100 columns reformat the longer
  lines once.
- **The build checks Java with Spotless and `google-java-format` in its AOSP style**, the formatter
  the Google Java Style is defined by: `spotless:check` bound to Maven's `validate`, so every local
  build and every CI build fails on a misformatted file, and `spotless:apply` fixes it. Spotless is a
  Maven plugin, so no command of `sokar-release` is needed. Spotless does not derive Java formatting
  from `.editorconfig`; the formatter's style is what decides.
- **Other languages use their own formatter, pinned:** `dart format` for the frontend, the Google
  Python style's formatter for the site's scripts; `.editorconfig` carries their indent and length.
- **One reformatting commit per repository**, nothing else in it, listed in `.git-blame-ignore-revs`,
  so `git blame` skips it.
- **IntelliJ formats Java with the `google-java-format` plugin, set to AOSP**, so what the IDE
  produces is exactly what `spotless:check` expects; `.editorconfig` covers everything else. VS Code
  and Eclipse read `.editorconfig` natively or with the EditorConfig extension. The contributor guide
  (PJ27) names the plugin and its setting.

## Acceptance

- Every repository with code has the shared `.editorconfig`, and a copy that differs fails a check.
- A Java file indented or wrapped against the style fails `validate`, locally and in the build,
  naming the file; after `spotless:apply` it passes. Seen to fail once in each repository.
- Code formatted by IntelliJ with the `google-java-format` plugin set to AOSP passes `spotless:check`
  unchanged.
- The reformatting commit changes whitespace and line breaks only, and `git blame` skips it.

