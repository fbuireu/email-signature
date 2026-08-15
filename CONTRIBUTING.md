# Contributing to email-signature

A caveat up front: this is one person's email signature and the CDN that
serves its assets — personal infrastructure, not a community project. The
contributions that fit are small and specific: a broken link, a rendering
problem in a mail client, an accessibility gap. Anything bigger, open an issue
first.

If you want the shape of the repo, that is [ARCHITECTURE.md](./ARCHITECTURE.md).
The working rules are [CLAUDE.md](./CLAUDE.md); the vocabulary is
[CONTEXT.md](./CONTEXT.md); the *why* is [docs/adr/](./docs/adr/).

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
updating `index.html` to reference it — the old file stays, forever.

## What a change here looks like

There is no build, no dependencies, and nothing to install: `index.html` is
source and artefact at once. But the constraints are unusual, because mail
clients dictate the markup:

- **Inline `style` attributes only** — mail clients strip `<style>` blocks
  and external stylesheets
- **Layout is nested `<table role="presentation">`** — no float, flex, grid
  or position; Outlook renders with Word's engine
- **PNG icons at fixed pixel sizes** — no SVG, no icon fonts
- **Every `<img>` needs a meaningful `alt`** — most clients block remote
  images by default, so the alt text is what recipients see first
- **Repetition is correct** — the same inline declarations recur because
  there is nowhere to share them; factoring them out is the failure mode

**Judge changes in a real mail client, never in a browser.** Chrome happily
renders markup that Outlook mangles. Anything visual also means regenerating
`assets/images/output/index.png` in the same change.

## How to contribute

- **A broken or suspicious link** → the [Security Policy](./SECURITY.md) if it
  could harm a recipient, an issue otherwise
- **A rendering bug** → an issue naming the mail client and OS, ideally with a
  screenshot
- **A fix** → fork, branch, PR — respecting the rules above; the PR template
  walks the checklist

Thanks for contributing! 🎉
