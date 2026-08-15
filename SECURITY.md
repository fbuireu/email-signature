# Security Policy

## Supported Versions

There are no versions. `main` is the product: GitHub raw serves the branch
directly, so what is on `main` right now is what every already-sent email
fetches. A fix ships by pushing.

## Reporting a Vulnerability

**Please do not report security vulnerabilities through public GitHub
issues.**

This repository contains no executable code — static HTML and images only —
but it is still worth a private report if you find:

- A **link in the signature pointing somewhere it shouldn't** — an expired or
  taken-over domain, a redirect through something untrustworthy
- An **asset integrity problem** — a published image whose bytes changed, or
  a path serving something other than what it claims
- Anything that could make an email carrying this signature dangerous to the
  person receiving it

### Preferred Method: GitHub Private Vulnerability Reporting

1. Go to the [Security tab](https://github.com/fbuireu/email-signature/security)
2. Click "Report a vulnerability"
3. Fill in the details

### Alternative: Email

Send an email to **fbuireu@gmail.com** with what you found, where, and how to
reproduce it.

### What to Expect

- **Acknowledgment**: we'll acknowledge receipt within 48 hours
- **Timeline**: link and asset problems are fixable with a push, so fixes for
  confirmed reports should land within days, not weeks
- **Credit**: we'll credit you (unless you prefer to remain anonymous)
