# 3. The Signature and its assets share one repository

Date: 2026-07-30

## Status

Accepted.

## Context

This repository holds two products with genuinely different jobs. The **Signature** is a document: one file of HTML that gets pasted into a mail client and is finished the moment it renders correctly. The **asset store** is a service: a set of public URLs with an availability obligation and an immutability contract ([ADR 0002](./0002-published-asset-paths-are-immutable.md)), whose job never ends.

They pull in opposite directions. The Signature wants to be edited freely; the asset store wants to never change. The Signature has one consumer; the asset store has as many consumers as there are sent emails. On ordinary repository-hygiene grounds they should be separated — a document repository and an asset repository, each with its own rules.

Separation is nevertheless not available at a reasonable price, and the reason is [ADR 0002](./0002-published-asset-paths-are-immutable.md). Moving `assets/` to `fbuireu/email-signature-assets` would change every published URL, which is precisely the operation that ADR forbids. The only migration that preserves sent mail is to leave every existing file exactly where it is and publish *new* assets elsewhere — which does not separate the two products, it just adds a second place to look for them.

## Decision

Both products live in this repository, and this is treated as a decision with a stated reason rather than as an accident of how the repository started. `index.html` and `assets/` are siblings; neither is subordinate to the other, and the documentation names both as products rather than describing the assets as an implementation detail of the Signature.

## Consequences

- **The repository has two contracts at once**, and they are not the same contract. `index.html` may be rewritten at will; `assets/` may only be appended to. Anyone — human or agent — working here has to know which half they are touching. [`CONTEXT.md`](../../CONTEXT.md) is split into two bounded contexts for exactly this reason.
- The repository name, owner, default branch and visibility are all frozen by the URLs they appear in, so this decision inherits the whole of [ADR 0002](./0002-published-asset-paths-are-immutable.md)'s cost.
- "Is this repository still needed?" has no single answer. The Signature could be abandoned tomorrow and the repository would still have to exist, public and on `main`, to serve mail already sent. Archiving is safe (archived repositories still serve raw); deleting is not.
- The cost is a permanently muddled repository description. A reader arriving at the README sees a signature template and has no reason to suspect the assets carry an obligation the HTML does not — which is why the README links the documentation rather than standing alone.
