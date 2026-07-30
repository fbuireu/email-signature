# CLAUDE.md

Agent-facing guide for **email-signature**. See [CONTEXT.md](./CONTEXT.md) for the domain vocabulary
(Signature, Published Path, Supersession, Sent Mail…) and do not duplicate it here.
[ARCHITECTURE.md](./ARCHITECTURE.md) is the big picture: the two products, the delivery path and the ADR
index.

## What this is

Two products in one repository ([ADR 0003](./docs/adr/0003-the-signature-and-its-assets-share-one-repository.md)):
`index.html`, an HTML email signature pasted into a mail client, and `assets/`, a set of public URLs served
by GitHub raw that every already-sent email still fetches from.

**The second product is the one that can be damaged.** Nothing about it fails loudly, and nothing in CI can
see the inboxes that would break.

## Stack

None. No package manager, no lockfile, no dependencies, no build, no tests, no local dev server
([ADR 0005](./docs/adr/0005-no-build-step.md)). `index.html` is source and artefact at once; cloning gives a
complete working copy. The only automation is four GitHub Actions workflows, none of which builds anything.

There is nothing to run. Verification is opening `index.html`, and — for anything that changes rendering —
sending it to a real mail client.

## The one rule that matters

**A push to `main` is an irreversible publication.** GitHub raw serves the branch directly
([ADR 0001](./docs/adr/0001-github-raw-serves-the-assets.md)), so there is no deploy step between committing
and publishing, and reverting does not unpublish — the URL was live in between.

From that follows the rule that governs `assets/`
([ADR 0002](./docs/adr/0002-published-asset-paths-are-immutable.md)):

> A path under `assets/` that has ever been pushed to `main` is **never renamed, moved, deleted, or
> repointed at different bytes.**

Change an image by *adding* a new path and updating `index.html` to reference it. The old file stays. This
is the opposite of the normal instinct — the callers that would break are not in this repository, so no
search, no test and no CI check will find them.

## Conventions

- **Inline `style` attributes only.** Never add a `<style>` block, an external stylesheet, or class-based
  styling; mail clients strip them ([ADR 0004](./docs/adr/0004-email-clients-dictate-the-markup.md)).
  `class="wrapper"` in the markup is an inert label, not a hook.
- **Layout is nested `<table role="presentation">`.** No `float`, `flex`, `grid` or `position` — Outlook on
  Windows renders with Word's engine and supports none of them.
- **Repetition is correct.** The same declarations recur across elements because there is nowhere to share
  them. Factoring them out is the failure mode, not the fix.
- **PNG icons at fixed pixel sizes.** No SVG, no icon fonts, no CSS-drawn shapes — none render in an email
  body.
- **Every `<img>` needs a meaningful `alt`.** Most clients block remote images by default, so the `alt` is
  what recipients see first and the whole fallback if the assets stop being served. An `<img>` without one
  is a defect.
- Assets are named for what they depict, lowercase and hyphenated (`stack-overflow.png`), and live flat in
  `assets/images/png/`.
- Judge changes in a **mail client**, never in a browser. Chrome renders markup that Outlook mangles.

## Maintenance contract

| If you change | Update |
| --- | --- |
| Anything visual in `index.html` | Regenerate `assets/images/output/index.png` and commit it in the same change |
| An icon | Add a new Published Path; never edit or rename the old one |
| A link target | Check whether the host belongs in `.lycheeignore`, and say why in the commit |
| A domain word's meaning, or introduce one | [`CONTEXT.md`](./CONTEXT.md) — vocabulary only |
| The delivery path, the workflows, or the file structure | [`ARCHITECTURE.md`](./ARCHITECTURE.md) |
| A behaviour a doc states as an invariant or a gotcha | that bullet, or delete it if it stopped being true |
| A decision an ADR records | that ADR — amend it, or supersede it with a new one and say so in both `## Status` blocks |

Propose an ADR in [`docs/adr/`](./docs/adr/) when a decision is **hard to reverse**, **surprising without
context** and **the result of a real trade-off**. All three, or it is not an ADR. Copy
[ADR 0000](./docs/adr/0000-adr-template.md), number it one above the highest existing file, add it to the
index in [`ARCHITECTURE.md`](./ARCHITECTURE.md), and link it from wherever it bites — an ADR only the index
points at will not be read.

Prefer naming what you mean over citing a line: `index.html:51` rots the moment anything above it moves.

## Gotchas

- **Unbalanced tags cancel out and pass every count.** Two defects — an extra `</span>` in the phone link
  and a missing one in the website link — survived here for over a year because the document-level totals
  matched (11 open, 11 close). Inside an `<a>`, Word's engine leaks the unclosed style into the rest of the
  block. Check balance *per element*, never per file.
- **"Unused asset" is a meaningless signal.** A file `index.html` no longer references may be the only thing
  standing between an old email and a broken image. `assets/images/png/` already contains icons the current
  Signature does not use, and that is the expected state, not debt. Never clean this directory.
- **Two URL forms are in use and they are not equivalent.** The social icons are referenced as
  `github.com/…/blob/main/…?raw=true`, which reaches the bytes only by redirect; the phone and website icons
  use the direct `raw.githubusercontent.com/…` form. Write new references in the direct form, and do not
  "tidy" the existing ones — both are already published, and repointing either is forbidden
  ([ADR 0001](./docs/adr/0001-github-raw-serves-the-assets.md)).
- **The Preview is not generated.** `assets/images/output/index.png` is a hand-taken screenshot uploaded by
  hand. Nothing checks it matches the Signature, and a stale one misrepresents the product on the README
  with no failing check anywhere.
- **The avatar is not in this repository.** It is fetched from `avatars.githubusercontent.com`, so it
  changes whenever the GitHub profile picture changes, with no commit here.
- **Four hosts are never link-checked.** Reddit, Medium, Unsplash and LinkedIn sit in `.lycheeignore`
  because they defend against bots and answer CI with `403` or another `4xx` — not because the links are
  broken. If one of those dies for real, nothing notices, ever.
- **A pinned action's SHA and its version comment must move together**
  ([ADR 0007](./docs/adr/0007-actions-are-pinned-by-digest-and-auto-merged.md)). The SHA is what runs; the
  comment is the only thing making it legible. Updating one without the other leaves a workflow that lies
  about what it executes.
- **Dependabot is half-configured.** `.github/workflows/dependabot-auto-merge.yml` exists but there is no
  `.github/dependabot.yml`, so Dependabot opens no version-update pull requests here. Renovate does the
  work; that workflow only ever sees GitHub's own security updates.
- **The automation runs on `secrets.PAT`**, because a token cannot approve its own pull request. It is a
  standing write credential whose expiry nothing monitors — when it lapses, auto-approve and auto-merge stop
  silently.
- **`link-checker.yml` cannot open its issue — confirmed defect.** The *Create Issue From File* step reads
  `./reports/link-checker-output.md`, but the workflow never sets lychee's `output` input, whose default is
  `lychee/out.md` (verified in the `action.yml` of the pinned `lycheeverse/lychee-action` SHA). The report
  is therefore never at the path the next step looks in, so issue creation fails exactly when a link breaks
  — the one moment it exists for. The build still goes red via `fail: true`, so breakage is not silent, but
  the issue never appears. Fix by setting `output: ./reports/link-checker-output.md` on the lychee step, or
  by pointing `content-filepath` at `./lychee/out.md`.

## Repository identity is part of the contract

Owner, repository name, default branch and public visibility all appear inside the published URLs. Renaming
the repository, transferring it, changing the default branch away from `main`, or making it private each
break every already-sent email exactly as deleting a file would. Archiving is safe — archived repositories
still serve raw. Deleting is not.
