# 2. Published asset paths are immutable

Date: 2026-07-30

## Status

Accepted. Amended 2026-09-27: the Preview under `assets/images/output/` is outside the rule, since no Signature quotes it. Amended 2026-11-06: a path the Signature no longer quotes is deleted instead of kept for Sent Mail; the bytes a path serves still never change.

## Context

A file in [`assets/images/png/`](../../assets/images/png) is not really a file in a repository. Once a Signature carrying its URL has been pasted into a mail client and a message has been sent, that path is quoted in every copy of that message, in inboxes this repository has no reach into and cannot enumerate. **A sent email cannot be edited.** Deleting or renaming the file does not break a build, does not fail a test, and does not fail the link checker: [`index.html`](../../index.html) gets updated in the same commit, so CI stays green while mail from three years ago quietly loses an icon.

The normal repository instinct is the opposite: rename freely, the compiler will find the callers. Here the callers are not in the repository, and the damage is invisible from inside it.

The first version of this decision kept every file ever published, on the grounds that these are PNGs of a few kilobytes each and never deleting them costs nothing anyone will notice. It did cost something: `assets/` became an append-only log in which "unused" meant nothing, every audit had to argue each file out of the tree by hand, and icons the Signature dropped long ago looked exactly like the ones it still sends. The alternative it rejected, accepting a broken image in old mail, is the one this amendment takes, on the grounds it already named: a message old enough to quote an icon the Signature dropped is rarely reread, and what it loses is one icon, not its text.

## Decision

The bytes a path under `assets/` serves never change. A changed Icon arrives as a **new** path plus a Signature that points at it, never as different bytes at the old path.

A path the Signature no longer quotes is deleted, in the same change that stops quoting it, so [`assets/images/png/`](../../assets/images/png) holds exactly the Icons `index.html` references. Sent Mail that quotes a deleted path shows a broken image for that Icon, and that cost is accepted.

The Preview, [`assets/images/output/index.png`](../../assets/images/output/index.png), is the one exception to both. The README shows it and no Signature has ever quoted it, so no Sent Mail can break when its bytes change, and it is replaced in place whenever the Signature changes visually ([ADR 0005](./0005-no-build-step.md)).

## Consequences

- **`assets/images/png/` mirrors the Signature.** A file `index.html` does not reference is dead and is removed, so "unused" is a reliable signal there again, and a dead-asset check may use exactly that test.
- Replacing an Icon adds the new path and deletes the old one in one commit. The Signature's visual history lives in git, not in the tree.
- A pasted copy of an older Signature in a mail client keeps quoting the paths it was given, so once one of them is deleted, mail sent from that client shows a broken image for that Icon until the Signature is installed again ([README](../../README.md#using-the-signature)).
- Swapping the *bytes* at an existing path stays forbidden, for a reason beyond correctness: GitHub raw offers no cache-control contract ([ADR 0001](./0001-github-raw-serves-the-assets.md)), so an in-place replacement is served inconsistently: some recipients see the old image, some the new, for an unknown period.
- Splitting the repository, renaming it, changing the default branch away from `main`, or making it private each rewrite every published URL at once, the ones the current Signature quotes included, and are therefore all governed by this rule ([ADR 0003](./0003-the-signature-and-its-assets-share-one-repository.md)).
- The rule is not enforced by anything. [`AGENTS.md`](../../AGENTS.md) states it for whoever changes the repository, [`CODING_STANDARDS.md`](../../CODING_STANDARDS.md) for whoever reviews the change, and it defines the *Published Path* entry of [`GLOSSARY.md`](../../GLOSSARY.md); there is no test, because no test inside this repository can see the inboxes that would break.
