# AGENTS.md

Agent-facing guide for **email-signature**. See [CONTEXT.md](./CONTEXT.md) for the domain vocabulary
(Signature, Published Path, Supersession, Sent Mail…) and do not duplicate it here.
[ARCHITECTURE.md](./ARCHITECTURE.md) is the big picture: the two products, the delivery path and the ADR
index.

Reviewing a diff: [CODING_STANDARDS.md](./CODING_STANDARDS.md).

## What this is

Two products in one repository ([ADR 0003](./docs/adr/0003-the-signature-and-its-assets-share-one-repository.md)):
[`index.html`](./index.html), an HTML email signature pasted into a mail client, and `assets/`, a set of public URLs served
by GitHub raw that every already-sent email still fetches from.

**The second product is the one that can be damaged.** Nothing about it fails loudly, and nothing in CI can
see the inboxes that would break.

## Stack

None. No package manager, no lockfile, no dependencies, no build, no tests, no local dev server
([ADR 0005](./docs/adr/0005-no-build-step.md)). `index.html` is source and artefact at once; cloning gives a
complete working copy. The only automation is a handful of GitHub Actions workflows, none of which builds anything.

There is nothing to run. Verification is opening `index.html`, and, for anything that changes rendering,
sending it to a real mail client.

## The one rule that matters

**A push to `main` is an irreversible publication.** GitHub raw serves the branch directly
([ADR 0001](./docs/adr/0001-github-raw-serves-the-assets.md)), so there is no deploy step between committing
and publishing, and reverting does not unpublish: the URL was live in between.

From that follows the rule that governs `assets/`
([ADR 0002](./docs/adr/0002-published-asset-paths-are-immutable.md)):

> A path under `assets/` that has ever been pushed to `main` is **never renamed, moved, deleted, or
> repointed at different bytes.**

Change an image by *adding* a new path and updating `index.html` to reference it. The old file stays. This
is the opposite of the normal instinct: the callers that would break are not in this repository, so no
search, no test and no CI check will find them. The Preview, `assets/images/output/index.png`, is the one
exception: no Signature quotes it, so it is replaced in place.

## Writing the markup

`index.html` is written to the capability floor of Outlook on Windows: nested presentation tables, inline
styles, PNG icons and a meaningful `alt` on every image
([ADR 0004](./docs/adr/0004-email-clients-dictate-the-markup.md)). Read that ADR before editing the markup.
Judge a change in a **mail client**, never in a browser: Chrome renders markup that Outlook mangles.

## Conventions

- **Conventional commits**, on the pull request title that a squash merge commits, linted by
  [`commit-message.yml`](./.github/workflows/commit-message.yml). Do NOT add a Co-Authored-By / Claude trailer to
  commits or PRs.

## Maintenance contract

These documents are not generated. When you change code, update the docs **in the same commit**: a follow-up commit
is a promise, not a fix.

| If you change | Update |
| --- | --- |
| Anything visual in `index.html` | Regenerate [`assets/images/output/index.png`](./assets/images/output/index.png) and commit it in the same change |
| An icon | Add a new Published Path; never edit or rename the old one |
| A link target | Check whether the host belongs in [`.lycheeignore`](./.lycheeignore), and say why in the commit |
| What a domain word means, or introduce a new one | [`CONTEXT.md`](./CONTEXT.md): the glossary, vocabulary only |
| A rule about how code is written: the markup, the assets, the workflows | [`CODING_STANDARDS.md`](./CODING_STANDARDS.md) |
| The delivery path, the workflows, or the file structure | [`ARCHITECTURE.md`](./ARCHITECTURE.md) |
| A behaviour a doc states as an invariant or a gotcha | that bullet, or delete it if it stopped being true |
| A decision an ADR records | that ADR: amend it, or supersede it with a new one and say so in both `## Status` blocks |

A new ADR starts as a copy of [ADR 0000](./docs/adr/0000-adr-template.md), the template, which says when a
decision earns one and where to link it from.

## Gotchas

- **"Unused asset" is a meaningless signal.** A file `index.html` no longer references may be the only thing
  standing between an old email and a broken image. `assets/images/png/` already contains icons the current
  Signature does not use, and that is the expected state, not debt. Never clean this directory.
- **Two URL forms are in use and they are not equivalent.** The social icons and the GitHub link's are referenced as
  `github.com/…/blob/main/…?raw=true`, which reaches the bytes only by redirect; the phone and website icons
  use the direct `raw.githubusercontent.com/…` form. Both are published, so neither is rewritten
  ([ADR 0001](./docs/adr/0001-github-raw-serves-the-assets.md)); the form a new reference takes is in
  [CODING_STANDARDS.md](./CODING_STANDARDS.md).
- **The Preview is not generated.** `assets/images/output/index.png` is a hand-taken screenshot uploaded by
  hand. Nothing checks it matches the Signature, and a stale one misrepresents the product on the README
  with no failing check anywhere.
- **The avatar is not in this repository.** It is fetched from `avatars.githubusercontent.com`, so it
  changes whenever the GitHub profile picture changes, with no commit here.
- **Some hosts are never link-checked.** Reddit, Medium, Unsplash and LinkedIn sit in `.lycheeignore`
  because they defend against bots and answer CI with `403` or another `4xx`, not because the links are
  broken. If one of those dies for real, nothing notices, ever.
- **Dependabot is half-configured.** [`.github/workflows/dependabot-auto-merge.yml`](./.github/workflows/dependabot-auto-merge.yml) exists but there is no
  `.github/dependabot.yml`, so Dependabot opens no version-update pull requests here. Renovate does the
  work; that workflow only ever sees GitHub's own security updates.
- **The Dependabot auto-merge runs on `secrets.PAT`**, the same shared workflow every sibling repository carries, so a
  merge is the Owner's rather than the workflow identity's. It is a standing write credential whose expiry nothing
  monitors: when it lapses, that auto-merge stops silently. Renovate needs no such thing: the `main` ruleset requires
  no approval, only the checks, so its pull requests merge through the platform.
- **In [`link-checker.yml`](./.github/workflows/link-checker.yml), two paths must agree and nothing checks that they do.** lychee's `output` and the
  *Create Issue From File* step's `content-filepath` both name `./reports/link-checker-output.md`. Change
  one without the other and issue creation fails on a missing file. It fails silently, because that step
  only ever runs when a link is already broken. Setting `output` explicitly is load-bearing: lychee's own
  default is `lychee/out.md`, so dropping the input breaks issue creation.

## Known defects

Open, in the Signature as it stands. Fix and delete the entry: deleting it is part of the fix.

- **The disclaimer band's top padding is silently dropped.** The `<td>` wrapping the confidentiality notice
  asks for `padding: 10 0px 0px 0px !important`, with no unit on the first value, so every engine discards
  the whole declaration and the band sits flush against the GitHub link. Adding the `px` moves the layout by
  10px, which makes it a visual change and therefore one to judge in a mail client and land with a new
  Preview.

## Repository identity is part of the contract

Owner, repository name, default branch and public visibility all appear inside the published URLs. Renaming
the repository, transferring it, changing the default branch away from `main`, or making it private each
break every already-sent email exactly as deleting a file would. Archiving is safe: archived repositories
still serve raw. Deleting is not.
