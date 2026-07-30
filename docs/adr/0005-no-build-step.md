# 5. No build step

Date: 2026-07-30

## Status

Accepted.

## Context

The obvious improvement to [ADR 0004](./0004-email-clients-dictate-the-markup.md)'s markup is to stop writing it by hand. The established answer for email HTML is a template plus an inliner — MJML, Foundation for Emails, or a small script that expands a YAML description of the links into the nested tables and pushes a shared style definition down onto every element. The duplication that ADR accepts as permanent would then exist only in the generated output.

Three things argue the other way. The artefact is a single short file, and at the time of writing it had been edited **five times in two years** — the maintenance burden the toolchain would relieve is close to nil, while the toolchain itself needs a runtime, a lockfile, dependency updates and a CI job forever. The output would have to be committed anyway, because a mail client cannot run a build, so the repository ends up with both a source and a generated artefact where it currently has one file. And the thing a build cannot do is the only thing that matters: correctness here means "renders in Outlook", which no generator verifies and no CI step can assert.

This decision fails the usual test for an ADR — adding a build later is a Tuesday afternoon, not a migration. It is recorded anyway because it is the first question anyone opening this repository asks, and a paragraph buried in an architecture document is easier to miss than an entry in the decision log.

## Decision

`index.html` is both source and artefact. It is hand-edited, committed as-is, and pasted into the mail client verbatim. There is no generator, no template, no inliner, no package manager, no lockfile and no `node_modules` — the repository has no runtime dependencies of any kind.

The same applies to the preview: `assets/images/output/index.png` is a screenshot taken by hand and uploaded, not a rendered artefact, and keeping it in step with the HTML is a human obligation rather than an automated one.

## Consequences

- **The repository has no install, no test and no local dev loop.** Cloning it gives a working copy of everything; the only "build" is opening `index.html` or pasting it somewhere.
- Editing means touching repeated inline styles by hand in several places, which is exactly the cost [ADR 0004](./0004-email-clients-dictate-the-markup.md) accepts. Nothing here reduces it; this decision declines to pay a standing toolchain cost to reduce it.
- **The preview can silently drift from the Signature.** In practice it has not — both times `index.html` changed, the PNG was regenerated within four minutes — but nothing enforces it, and a stale preview misrepresents the product on the README with no failing check anywhere. The obligation is recorded in [`CLAUDE.md`](../../CLAUDE.md) instead.
- The only automation is `.github/workflows/link-checker.yml`, which verifies URLs rather than markup or rendering. Structural defects in the HTML are caught by reading it, and nothing else.
- If this is ever revisited, the trigger to watch for is edit *frequency*, not file size. A signature that changes monthly justifies a generator; one that changes twice in two years does not.
