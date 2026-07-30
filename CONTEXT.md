# Domain vocabulary

This repository holds two products with different obligations ([ADR 0003](./docs/adr/0003-the-signature-and-its-assets-share-one-repository.md)), and they do not share a language. The **Signature** context is about a document that has to survive a hostile renderer; the **Distribution** context is about public URLs that have to survive being quoted in mail nobody can edit any more. The same English word means different things on either side of that line, so the two are stated separately and the crossing terms are named at the end.

Everything here is vocabulary. How the pieces fit together is [ARCHITECTURE.md](./ARCHITECTURE.md); why they are the way they are is [docs/adr/](./docs/adr/); how to work on them is [CLAUDE.md](./CLAUDE.md).

---

## Signature context

The domain of a document that renders correctly inside somebody else's mail client. Everything here is about what survives the renderer — not about where the images are hosted.

**Signature**:
The block of HTML pasted into a mail client's signature setting, and the only thing this context produces. It is a single file, `index.html`, which is simultaneously the source and the artefact ([ADR 0005](./docs/adr/0005-no-build-step.md)) — there is no version of it that is not also what gets shipped.
_Avoid_: template, email, snippet, footer

**Mail Client**: The program that renders a Signature for a recipient, and the authority this context answers to. It is never a browser: the capability floor is set by Outlook on Windows, which renders with Word's engine ([ADR 0004](./docs/adr/0004-email-clients-dictate-the-markup.md)). "It looks right" always means "in a Mail Client", never "in Chrome".
_Avoid_: browser, viewer, renderer, email app

**Layout Table**: A `<table role="presentation">` used purely to position things, because table cells are the only layout primitive a Mail Client can be trusted with. It carries no data and must never be read as tabular content.
_Avoid_: grid, container, wrapper, layout div

**Inline Style**: A `style` attribute written directly on the element it affects — the only styling mechanism that reliably survives delivery. Repetition across elements is the expected state, not duplication to be factored out. A `<style>` block or an external stylesheet is not an Inline Style and does not work here.
_Avoid_: CSS, stylesheet, rule, class

**Icon**: A small square raster image standing in for a contact method or a social profile, at a fixed pixel size. Icons are PNG because Mail Clients render neither SVG nor icon fonts in a body ([ADR 0004](./docs/adr/0004-email-clients-dictate-the-markup.md)).
_Avoid_: logo, glyph, image, symbol, badge

**Alt Text**: The `alt` attribute on an Icon, and part of the visible design rather than an accessibility extra — most Mail Clients block remote images by default, so on first read the Alt Text *is* the Icon. It is also the entire fallback if the assets ever stop being served.
_Avoid_: alt tag, fallback text, description, title

**Contact Link**: An `<a>` in the Signature pairing an Icon with a destination. It comes in two shapes: the labelled kind, where the Icon and a text label sit in adjacent `<span>`s (the phone number and the personal site), and the icon-only kind used for social profiles, where the `<a>` wraps the Icon alone.
_Avoid_: entry, item, social icon, button

**Preview**: `assets/images/output/index.png` — a screenshot of a rendered Signature, taken by hand and shown in the README. It illustrates the product but is not the product, and nothing verifies that it still matches ([ADR 0005](./docs/adr/0005-no-build-step.md)).
_Avoid_: render, output, screenshot, example, demo

---

## Distribution context

The domain of serving public files at stable URLs to consumers this repository cannot see or contact. Everything here is about permanence — the *contents* of the files belong to the Signature context.

**Asset**: A file under `assets/` that is served publicly. In this context an Asset is defined by its URL, not by its pixels: replacing the bytes at a given location produces a different image at the same Asset, which is forbidden, rather than a new Asset.
_Avoid_: image, file, resource, static file

**Published Path**: The location under `assets/` at which an Asset is served, and the unit of promise in this context. Once pushed to `main` it is permanent: never renamed, never moved, never deleted, and never repointed at different bytes ([ADR 0002](./docs/adr/0002-published-asset-paths-are-immutable.md)).
_Avoid_: filename, path, location, URL slug

**Publication**: The act that makes a Published Path permanent — a push to `main`, since GitHub raw serves the branch directly ([ADR 0001](./docs/adr/0001-github-raw-serves-the-assets.md)). There is no upload, no deploy and no build between committing and publishing, which is why an accidental push cannot be taken back.
_Avoid_: deploy, release, upload, ship

**Consumer**: Anything fetching a Published Path. Exactly one Consumer is visible from inside this repository (`index.html`); the ones that matter are not — every message already sent quotes these URLs from an inbox nobody here can reach.
_Avoid_: client, user, caller, dependent

**Sent Mail**: Messages already delivered that still reference Published Paths. It is the reason this context exists and the constraint behind every rule in it: **Sent Mail cannot be edited**, so it can only be broken, never fixed.
_Avoid_: history, old emails, archive, legacy

**Supersession**: How an Asset is changed, given that it cannot be modified: add a new Published Path and repoint the Signature at it, leaving the old one serving Sent Mail. It makes `assets/` append-only, and it makes "unreferenced" a meaningless signal there — an Asset no Signature mentions may still be the only thing an old message has.
_Avoid_: replacement, update, overwrite, migration

**Ignored Domain**: A host listed in `.lycheeignore` and therefore never verified by the link checker. These are excluded because they defend against bots and answer CI with `403` or another `4xx`, not because the links are broken — a genuinely dead one on those hosts fails silently and forever.
_Avoid_: allowlist, exception, skipped link, false positive

---

## Terms that cross the boundary

Three words mean different things depending on which product is being discussed, and each has caused, or is likely to cause, the wrong instinct:

| Word | In the Signature context | In the Distribution context |
| --- | --- | --- |
| **Asset** | An Icon — a picture chosen for how it renders, freely swappable for a better one | A Published Path — a permanent public promise, never swappable |
| **Publish** | Pasting the Signature into a mail client, a private and reversible act | Pushing to `main`, an irreversible public one |
| **Unused** | An Icon no longer referenced by `index.html`: dead weight | An Asset no longer referenced by `index.html`: still load-bearing for Sent Mail, and must be kept |

The word **Asset** on its own is the trap. Preferring *Icon* when discussing what renders and *Published Path* when discussing what is served keeps the two obligations from being confused, and the confusion is not academic: deleting an "unused asset" is a routine cleanup in one context and permanent damage in the other.
