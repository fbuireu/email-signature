# 1. GitHub raw serves the assets

Date: 2026-07-30

## Status

Accepted.

## Context

The Signature is HTML pasted into a mail client. Mail clients do not bundle assets: every image is a URL the recipient's client fetches, from an origin that must stay reachable for as long as the email exists. That origin therefore has to outlive any given hosting whim, and it has to cost nothing to keep alive, because nobody renews a subscription for a signature they set up four years ago.

A real object store behind a CDN (S3 + CloudFront, R2, Cloudinary) was the obvious alternative. It offers a written cache and availability contract, which is exactly what this use case wants. It also means an account to keep alive, credentials to rotate, a billing relationship, and a publish step between "edit the file" and "the image is live" — all to serve a handful of small PNGs to the correspondents of a single sender.

`raw.githubusercontent.com` is free, requires no account beyond the one already hosting the repository, and inherits GitHub's uptime. It is also **not a supported CDN**: GitHub documents no cache-control contract, no bandwidth guarantee and no stability promise for it, and rate-limits traffic it considers abusive at its discretion.

## Decision

Every image the Signature owns is served straight out of this repository on `main`, over GitHub's own hosting. There is no CDN account, no bucket and no upload step: pushing to `main` publishes. The managed-CDN alternative is rejected on operational cost rather than on technical merit — the guarantee it buys is not worth the standing maintenance it imposes at this scale.

Two URL forms are in use, and they are not equivalent. The direct `raw.githubusercontent.com/<owner>/<repo>/main/…` form is the canonical one. The `github.com/<owner>/<repo>/blob/main/…?raw=true` form, used by the social icons, resolves to the same bytes only by redirect — an extra hop that some mail clients' image proxies handle less predictably than a direct fetch. New assets use the direct form; the existing ones are not rewritten, because both are already published and neither may be repointed ([ADR 0002](./0002-published-asset-paths-are-immutable.md)).

## Consequences

- **The URL host is load-bearing in a way nothing in this repository can fix.** If GitHub withdraws or throttles raw serving, every already-sent email loses its icons at once, and no edit here repairs them. The mitigation is not technical: every `<img>` carries an `alt`, so a failed fetch degrades to readable text rather than to a blank.
- Cache behaviour is whatever GitHub decides. An image replaced in place may be served stale for an unknown period — one of the reasons paths are append-only instead ([ADR 0002](./0002-published-asset-paths-are-immutable.md)).
- Assets must live in a **public** repository for the URLs to resolve without credentials. Making this repository private would break every sent email; that is a constraint on the repository itself, not just on its contents.
- Because the host is fixed and baked into sent mail, splitting the assets into their own repository stops being a free refactor ([ADR 0003](./0003-the-signature-and-its-assets-share-one-repository.md)).
- `.github/workflows/link-checker.yml` verifies these URLs on every push, and is the only automated evidence that the delivery path still works.
