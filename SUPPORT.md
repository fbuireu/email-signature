# Support

This repository is two things: an HTML email signature, and the CDN that serves its images. It is personal, it
is maintained by one person, and most of what could go wrong here is a link.

## If you are reusing the signature

Take it. [`index.html`](./index.html) is [MIT](./LICENSE), and no attribution is expected inside an email
footer ([ADR 0008](./docs/adr/0008-mit-replaces-the-cc0-dedication.md)). Two things to know before you do:

- **The icons under [`assets/images/png/`](./assets/images/png) are third-party brand marks**, and no licence granted here covers
  them. Swap them for the icons of your own links.
- **The markup looks like 2003 on purpose.** Nested tables and inline styles are what mail clients still
  require ([ADR 0004](./docs/adr/0004-email-clients-dictate-the-markup.md)). Modernising it breaks Outlook
  before it breaks anything else.

Questions about adapting it are welcome as an
[issue](https://github.com/fbuireu/email-signature/issues/new?template=bug_report.yml), but the answer is
usually one of those two bullets.

## If a link or an image is broken

That is worth reporting, and it is the failure this repository is built to avoid: every published asset path
is permanent, because emails already sent quote it
([ADR 0002](./docs/adr/0002-published-asset-paths-are-immutable.md)). A 404 on an asset means something was
renamed that never should have been. The [link checker](./.github/workflows/link-checker.yml) runs on every
push and catches most of it first.

## Vulnerabilities

Privately, through the [security policy](https://github.com/fbuireu/email-signature/security/policy). A
hijacked outbound link or a tampered asset is the realistic case here, and it belongs there rather than in a
public issue.
