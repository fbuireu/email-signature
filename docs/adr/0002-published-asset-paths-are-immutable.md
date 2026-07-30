# 2. Published asset paths are immutable

Date: 2026-07-30

## Status

Accepted.

## Context

A file in `assets/images/png/` is not really a file in a repository. Once a Signature carrying its URL has been pasted into a mail client and a message has been sent, that path is quoted in every copy of that message, in inboxes this repository has no reach into and cannot enumerate. **A sent email cannot be edited.** Deleting or renaming the file does not break a build, does not fail a test, and does not fail the link checker — `index.html` gets updated in the same commit, so CI stays green while mail from three years ago quietly loses an icon.

The normal repository instinct is the opposite: rename freely, the compiler will find the callers. Here the callers are not in the repository. Treating a rename as safe is the single most likely way to cause damage in this project, and the damage is invisible from inside it.

The alternative — allowing renames and accepting broken images in old mail — is defensible on the grounds that nobody rereads four-year-old email. It was rejected because the cost of the discipline is close to zero: these are PNGs of a few kilobytes each, and never deleting them costs nothing anyone will notice.

## Decision

A path under `assets/` that has ever been pushed to `main` is permanent. It is never renamed, never moved and never deleted, and the bytes it serves are never swapped for a different image. Changes are made by **adding** a new path and pointing `index.html` at it; the superseded file stays where it is, serving the mail that still references it.

This makes `assets/` an append-only log rather than a directory of current files, and it makes "unused" an unreliable signal there: a file no `index.html` references may still be the only thing standing between an old email and a broken image.

## Consequences

- **`assets/` accumulates and is never cleaned.** Any tooling or instinct that removes unreferenced files is wrong here; a future dead-asset check must be scoped to something other than "not referenced by `index.html`".
- Replacing an icon means two files where other projects would have one. The visual history of the Signature is therefore recoverable from the tree itself, which is a small unintended benefit.
- Swapping the *bytes* at an existing path is forbidden as well as renaming it, and for a second reason beyond correctness: GitHub raw offers no cache-control contract ([ADR 0001](./0001-github-raw-serves-the-assets.md)), so an in-place replacement is served inconsistently — some recipients see the old image, some the new, for an unknown period.
- Splitting the repository, renaming it, changing the default branch away from `main`, or making it private each rewrite the published URLs and are therefore all governed by this rule, not just file renames ([ADR 0003](./0003-the-signature-and-its-assets-share-one-repository.md)).
- The rule is not enforced by anything. It lives in [`CLAUDE.md`](../../CLAUDE.md) as a gotcha and in the *Published Path* entry of [`CONTEXT.md`](../../CONTEXT.md); there is no test, because no test inside this repository can see the inboxes that would break.
