# 8. MIT replaces the CC0-1.0 dedication

Date: 2026-08-23

## Status

Accepted. Supersedes [ADR 0006](./0006-cc0-1-0-licence.md).

## Context

[ADR 0006](./0006-cc0-1-0-licence.md) put this repository in the public domain under CC0-1.0, on the reasoning
that a signature template is too small to be worth an obligation, and that an attribution clause nobody honours
is worse than no clause at all.

Things pull the other way. The first is consistency: every other repository under this account is licensed,
not dedicated, and a reader who checks one licence expects the next to work the same way. The second is the
warranty disclaimer. CC0 waives copyright and offers no warranty language of its own beyond a short paragraph;
MIT states the disclaimer plainly, which is what a reuser pasting markup into a mail client is actually reading
for.

The trade-off ADR 0006 named has not disappeared: MIT does ask for a copyright notice in copies, and nobody is
going to carry one inside an email footer.

## Decision

The repository is released under **MIT**, and [`LICENSE`](../../LICENSE) carries the MIT text.

The attribution clause is accepted as unenforced in practice for the Signature itself: what it protects is the
repository as a whole rather than the markup a reuser pastes into their mail client. Nothing about the reuse
this repository invites changes.

## Consequences

- **The CC0 dedication cannot be withdrawn from copies already made.** ADR 0006 said this and it is still true:
  anyone who took this repository while it was CC0 keeps those terms for that copy, forever. This decision binds
  the tree from this commit onward, and nothing else. Relicensing is therefore additive, not a revocation.
- **The licence is now stricter than it was**, which is the honest way to describe going from a public-domain
  dedication to a permissive licence. A reuser who wants the old terms can take any pre-relicensing commit.
- **MIT still cannot reach the brand marks.** Almost everything in [`assets/images/png/`](../../assets/images/png) is a third-party logo
  (LinkedIn, GitHub, X, Reddit, Medium, Strava, Unsplash, Bluesky, Stack Overflow, 500px), and no licence
  granted here places someone else's trademark under it. Those files remain governed by their owners' brand
  guidelines whatever [`LICENSE`](../../LICENSE) says. What the licence actually covers is
  [`phone.png`](../../assets/images/png/phone.png), [`web.png`](../../assets/images/png/web.png) and the markup.
- Reusers copying [`index.html`](../../index.html) wholesale still inherit personal contact details. That follows
  from publishing a signature at all rather than from the licence.
