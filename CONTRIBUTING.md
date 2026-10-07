# Contributing to email-signature

A caveat up front: this is one person's email signature and the CDN that
serves its assets. It is personal infrastructure, not a community project. The
contributions that fit are small and specific: a broken link, a rendering
problem in a mail client, an accessibility gap. Anything bigger, open an issue
first.

If you want the shape of the repo, that is [ARCHITECTURE.md](./ARCHITECTURE.md).
If you want the working rules, that is [AGENTS.md](./AGENTS.md). If you want how
code here is written, and what a review holds a diff to, that is
[CODING_STANDARDS.md](./CODING_STANDARDS.md). If you want the vocabulary, that
is [GLOSSARY.md](./GLOSSARY.md). If you want the *why*, that is
[docs/adr/](./docs/adr/).

## Code of Conduct

By participating you are expected to uphold the
[Code of Conduct](./CODE_OF_CONDUCT.md).

## The one rule that matters

**A push to `main` is an irreversible publication.** GitHub raw serves the
branch directly, so there is no deploy step, and reverting does not unpublish.
From that follows the rule that governs `assets/`:

> A path under `assets/` that has ever been pushed to `main` is **never
> renamed, moved, deleted, or repointed at different bytes.**

Every email already sent quotes those URLs, and nothing in this repository can
see the inboxes that would break. Change an image by *adding* a new path and
updating [`index.html`](./index.html) to reference it. The old file stays, forever.
The Preview, `assets/images/output/index.png`, is the one file replaced in place,
since no email quotes it.

## What a change here looks like

There is no build, no dependencies, and nothing to install: `index.html` is
source and artefact at once. But the constraints are unusual, because mail
clients dictate the markup: inline styles, nested presentation tables, PNG
icons, a meaningful `alt` on every image, and repetition where other code
would factor it out. [CODING_STANDARDS.md](./CODING_STANDARDS.md) lists them
with their reasons, and every pull request is reviewed against it.

**Judge changes in a real mail client, never in a browser.** Chrome happily
renders markup that Outlook mangles. Anything visual also means regenerating
[`assets/images/output/index.png`](./assets/images/output/index.png) in the same change.

## How to contribute

- **A broken or suspicious link** → the [Security Policy](./SECURITY.md) if it
  could harm a recipient, an issue otherwise
- **A rendering bug** → an issue naming the mail client and OS, ideally with a
  screenshot
- **A fix** → fork, branch, PR, respecting the rules above; the PR template
  walks the checklist

Thanks for contributing! 🎉
