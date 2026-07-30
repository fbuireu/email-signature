# 4. Email clients dictate the markup

Date: 2026-07-30

## Status

Accepted.

## Context

`index.html` looks like it was written in 2003: nested `<table>` elements for layout, every style repeated inline on the element it applies to, `width="120"` attributes alongside CSS, `role="presentation"` on tables that carry no data, and raster PNGs for icons that would obviously be SVG anywhere else. Read as a web page it is indefensible. It is not a web page.

An email body is rendered by whatever engine the recipient's client happens to use, and the worst of those set the floor:

- **Outlook on Windows renders HTML with Microsoft Word's engine.** It has no meaningful support for `float`, `flex`, `grid` or `position`. Table cells are the only layout primitive that behaves predictably across it and everything else.
- **Gmail strips `<style>` blocks and external stylesheets** in significant contexts, and rewrites the markup it keeps. A stylesheet is not a reliable delivery mechanism; a `style` attribute on the element itself is.
- **Neither Gmail nor Outlook renders SVG in an email body.** Icon fonts fare worse: the font never loads, and the glyph falls back to a replacement character.
- **Most clients block remote images by default** until the recipient opts in, so on first read the icons are absent whatever format they are in.

Every modern alternative fails at the first of these, and failing means the signature is visibly broken for a large share of professional recipients — the exact audience the artefact exists for.

## Decision

The Signature is written to the capability floor of the worst mainstream mail client, not to any standard. Concretely, and as rules this repository follows:

- Layout is **nested tables** with `role="presentation"`. No `float`, `flex`, `grid`, or `position`.
- Styling is **inline `style` attributes only**. No `<style>` block, no external stylesheet, no class-based styling — `class="wrapper"` survives in the markup as an inert label, not as a styling hook.
- Icons are **PNG rasters at fixed pixel dimensions**, served as remote images ([ADR 0001](./0001-github-raw-serves-the-assets.md)). No SVG, no icon font, no CSS-drawn shapes.
- Every `<img>` carries a meaningful `alt`, and the `alt` text is treated as part of the design rather than as an accessibility afterthought.

Legibility of the source is knowingly sacrificed. This markup is an output format that happens to be hand-written, and it is read by rendering engines far more often than by people.

## Consequences

- **The `alt` attributes are load-bearing, not decorative.** With images blocked by default, `alt` is what most recipients see on first read, and it is the whole of the degradation story if GitHub raw ever stops serving ([ADR 0001](./0001-github-raw-serves-the-assets.md)). An `<img>` without one is a defect.
- Icons are fixed-resolution and fixed-colour. They do not adapt to a client's dark mode, and they are not crisp on high-density displays beyond the size they were exported at. Both are accepted costs of the format, not bugs to fix.
- Every visual change means editing repeated inline styles by hand across several elements, with no shared definition to change once. Duplication is the expected state of this file; deduplicating it is the failure mode.
- Nothing here can be verified by inspection in a browser. Chrome will happily render markup that Outlook mangles, so the only meaningful test is sending the signature to a real client — which is why there is no build and no automated rendering check ([ADR 0005](./0005-no-build-step.md)).
- Malformed nesting is unusually dangerous and unusually easy to miss. Unbalanced `<span>` tags inside an `<a>` cancel out at document level and pass every counting check, while Word's engine leaks the unclosed style into the rest of the block. Two such defects existed undetected in this file for over a year.
