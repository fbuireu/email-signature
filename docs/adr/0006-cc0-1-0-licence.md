# 6. CC0-1.0 licence

Date: 2026-07-30

## Status

Accepted.

## Context

A licence choice is close to irreversible in the direction that matters. A public-domain dedication cannot be withdrawn — anyone who received the work under it keeps those terms forever — so the decision is effectively permanent from the first push, and it is worth being deliberate about even for something this small.

What is actually being licensed is a personal signature: a name, a job title, a phone number and a row of social links arranged in a table. There is no commercial derivative to guard against, so the copyleft reasoning that would apply to a working tool does not apply here. The only genuinely reusable part is the *arrangement* — the demonstration that email HTML has to be built out of nested tables and inline styles ([ADR 0004](./0004-email-clients-dictate-the-markup.md)) — and that is precisely the part worth handing over without friction to whoever has just discovered the same constraints.

Even a permissive licence with an attribution clause (MIT, CC-BY) was rejected. Requiring attribution on a signature template means requiring a copyright notice inside somebody else's email footer, which is absurd in practice and would simply be ignored — an obligation nobody honours is worse than no obligation, because it makes the licence a lie about what is expected.

## Decision

The repository is released under **CC0-1.0** — a public-domain dedication rather than a licence. Anyone may take this markup, adapt it, ship it commercially, and owes nothing: no attribution, no notice, no share-alike. The attribution-bearing permissive licences are rejected as unenforceable in this medium, and copyleft as disproportionate to what is being protected.

## Consequences

- The dedication is irrevocable. Relicensing later binds nobody who already holds a copy, so the choice is permanent in every way that matters.
- **CC0 cannot reach the brand marks.** Almost everything in `assets/images/png/` is a third-party logo — LinkedIn, GitHub, X, Reddit, Medium, Strava, Unsplash, Bluesky, Stack Overflow, 500px — and no dedication granted here can place someone else's trademark in the public domain. Those files remain governed by their owners' brand guidelines whatever `LICENSE` says. In practice a reuser swaps them for the icons of their own links; what the dedication actually covers is `phone.png`, `web.png` and the markup.
- Reusers copying `index.html` wholesale inherit personal contact details. That follows from publishing a signature at all rather than from the licence, but the licence removes any friction that might have made someone stop and edit first.
- No CLA, no contributor agreement and no relicensing question ever arises. That is the practical benefit: a personal repository with one author and no dependencies gains nothing from licence machinery.
