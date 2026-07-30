# Email signature
This repo is intended to be used by me as a CDN to store my assets and the base code of my email signature. This is the result:

<img src="https://github.com/fbuireu/email-signature/blob/main/assets/images/output/index.png?raw=true" alt="Email signature" />

## Related actions
[![Link Checker](https://github.com/fbuireu/email-signature/actions/workflows/link-checker.yml/badge.svg)](https://github.com/fbuireu/email-signature/actions/workflows/link-checker.yml)

## Documentation

Before changing anything under `assets/`, read [ADR 0002](./docs/adr/0002-published-asset-paths-are-immutable.md):
those paths are quoted by every email already sent, so they are never renamed or deleted.

- [ARCHITECTURE.md](./ARCHITECTURE.md) — the two products, the delivery path, and the index of decisions
- [CONTEXT.md](./CONTEXT.md) — domain vocabulary
- [CLAUDE.md](./CLAUDE.md) — working rules, maintenance contract and gotchas
- [docs/adr/](./docs/adr/) — why each irreversible decision was made, and what it cost
