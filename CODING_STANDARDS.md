# Coding standards

What a review checks a diff against. The words are the ones [GLOSSARY.md](./GLOSSARY.md) defines, the reasons
are in [docs/adr/](./docs/adr/), and what an implementer needs while working is in [AGENTS.md](./AGENTS.md).

**hard** marks a rule whose breach is a defect: report it with the rule. **judgement** marks a call the
reviewer weighs against the diff: report it as a question. A rule here outranks the smell baseline; where it
endorses something a smell would flag, the case is listed under *Deliberate overrides of the smell baseline*
at the end.

## Tooling already enforces

No rule below restates these, and a diff that breaks one fails CI:

- The pull request title, a Conventional Commit, linted by
  [`commit-message.yml`](./.github/workflows/commit-message.yml).
- Every link the documents and the Signature carry, resolved by lychee in
  [`link-checker.yml`](./.github/workflows/link-checker.yml), bar what
  [`.lycheeignore`](./.lycheeignore) names.
- zizmor's audit of the workflows ([`zizmor.yml`](./.github/workflows/zizmor.yml)).

Nothing checks the markup or the assets: there is no build and no test here
([ADR 0005](./docs/adr/0005-no-build-step.md)), so every rule below is the reviewer's to catch.

## Every change

- **hard**: A change carries everything the maintenance contract in [AGENTS.md](./AGENTS.md) asks of it, in
  the same commit, and keeps every guardrail that guide states: a new Preview for a visual change, the ADR for a
  decision it overturns, the repository's owner, name, default branch and visibility left as they are. A
  follow-up commit is a promise, not a fix.

## Published Paths

- **hard**: Changes under `assets/images/png/` are additions only. An existing Published Path keeps its
  name, its location and its bytes; a changed Icon arrives as a new path plus a Signature that points at it
  ([ADR 0002](./docs/adr/0002-published-asset-paths-are-immutable.md)). A diff that modifies, renames or
  deletes a file there is a defect, whether or not `index.html` still references it.
- **hard**: `assets/` keeps every file it has ever published. An Asset the Signature no longer references
  still serves Sent Mail, so a diff that removes one as unused is a defect.
- **hard**: The Preview, `assets/images/output/index.png`, is the one file rewritten in place, because no
  Signature quotes it ([ADR 0002](./docs/adr/0002-published-asset-paths-are-immutable.md)).
- **judgement**: A new Icon is named for what it depicts, lowercase and hyphenated, and sits flat in
  `assets/images/png/`.

## Signature markup

The capability floor is Outlook on Windows ([ADR 0004](./docs/adr/0004-email-clients-dictate-the-markup.md)).

- **hard**: Styling is an Inline Style on the element it affects. `class="wrapper"` is an inert label;
  a new class used as a hook, a `<style>` block or a stylesheet is a defect.
- **hard**: Layout is nested Layout Tables (`<table role="presentation">`), since Word's engine renders
  no `float`, `flex`, `grid` or `position`.
- **hard**: Icons are PNG at fixed pixel sizes, since SVG, icon fonts and CSS-drawn shapes do not render
  in a message body.
- **hard**: Every `<img>` carries an Alt Text that names what the Icon stands for: with remote images
  blocked by default, it is what most recipients see first.
- **hard**: Tags balance per element: a `<span>` opened inside an `<a>` closes inside it. Per-file counts
  hide pairs of defects that cancel out, as two did here for over a year, and Word's engine leaks the unclosed
  style into the rest of the block.
- **hard**: Every non-zero CSS length carries its unit. `index.html` has no doctype, so a browser opening it
  renders in quirks mode and reads a bare number as pixels, while an engine in standards mode drops the whole
  declaration: the disclaimer band's `padding: 10 0px …` was 10px where the Signature is copied from and
  nothing in a standards-mode client.
- **judgement**: A new asset reference uses the direct `raw.githubusercontent.com/…` form. Existing
  references keep the form they were published with ([ADR 0001](./docs/adr/0001-github-raw-serves-the-assets.md)).

## Workflows

- **hard**: Every `uses:` names a full commit SHA with its version in a trailing comment, or its branch for a
  pin that follows one, and the two move together: the SHA is what runs, the comment is the only thing that
  makes it legible, and Renovate maintains both halves
  ([ADR 0007](./docs/adr/0007-actions-are-pinned-by-digest-and-auto-merged.md)).
- **hard**: YAML carries no explanatory comments; the reason for a line goes in the commit message, the
  pull request, an ADR or a rule here, and a gotcha an implementer would otherwise trip on goes in the
  *Gotchas* of [AGENTS.md](./AGENTS.md). The trailing comment on a SHA pin is the one exception.

## Docs

- **judgement**: Point at the markup by what it is rather than by line number, because an `index.html:51`
  citation rots the moment anything above it moves.
- **hard**: Hold every Mermaid diagram to the `layout: dagre` it was drawn with, set in its front matter, so a
  renderer that defaults to ELK cannot redraw it.
- **judgement**: Propose an ADR only for a decision that is hard to reverse, surprising without context and the
  result of a real trade-off, and link it from where it bites.

## Deliberate overrides of the smell baseline

- **Duplicated Code**: `index.html` repeats its inline declarations, because a Mail Client offers nowhere to
  share them, so a change that factors them out is the defect, not the fix
  ([ADR 0004](./docs/adr/0004-email-clients-dictate-the-markup.md)).
