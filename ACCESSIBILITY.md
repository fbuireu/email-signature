# Accessibility

This repository holds the HTML of Ferran Buireu's email signature and the icons it loads. The people who read it
are whoever receives a message from him, in their own mail client, often with remote images blocked and sometimes
with a screen reader or zoom. This page says what the signature does for them, what mail clients keep it from
doing, how to report a barrier, and what a change has to keep.

## What the signature does

- **Every fact is text.** The name, role, phone number, website and disclaimer are live text, not part of an
  image, so they can be read, zoomed, copied and searched with images blocked.
- **Every image has alt text** that names what it stands for, such as "Phone" or "LinkedIn". With images blocked,
  the alt text stands in for the icon, and a screen reader reads it as the name of the link the icon sits in.
- **Layout tables are marked as layout**, with `role="presentation"`, so a screen reader does not announce their
  rows and columns.

## What mail clients keep it from doing

Mail clients render a small part of HTML, and Outlook for Windows sets the floor
([ADR 0004](./docs/adr/0004-email-clients-dictate-the-markup.md)). So:

- The icons are PNG images of a fixed colour and size. They do not adapt to a client's dark mode, and they blur
  when zoomed well past their size.
- How far a reader's own settings, such as zoom, text size or high contrast, reach the signature depends on their
  mail client, not on this markup.

## What is tested

- A link checker runs on every push to `main`, on every pull request and once a week, so a link in the signature
  that stops working is reported.
- Each change is read in Outlook and Gmail, once with remote images blocked and once with them allowed, as the
  [README](./README.md#using-the-signature) describes.
- No screen reader is part of that check, and the signature has not been audited against WCAG, so this page makes
  no conformance claim.

## Known barriers

Barriers already found are tracked as
[open issues with the `accessibility` label](https://github.com/fbuireu/email-signature/issues?q=is%3Aissue%20state%3Aopen%20label%3Aaccessibility).

## Reporting a barrier

If the signature is hard or impossible for you to read or use,
[open a bug report](https://github.com/fbuireu/email-signature/issues/new?template=bug_report.yml). Without a
GitHub account, reply to the email that carried it and say the reply is about accessibility. You don't need to say
anything about a disability.

Include as much of this as you can:

- What you were trying to read or do, and what happened instead.
- Your mail client, operating system and device, in the form's *Mail client and OS* field.
- Any assistive technology you use, such as a screen reader, magnifier or voice control, and its version. It goes
  in the same field.
- Settings that change how mail looks: zoom or text size, high contrast or forced colours, dark mode, remote images
  blocked.
- A screenshot, if you're comfortable sharing one.

### What happens next

- The issue gets the `accessibility` label, so it joins the list above.
- Questions about missing details come in the issue.
- No date is promised for a fix. The fix links the issue. Mail already sent keeps the signature it was sent with,
  so a fix reaches new messages only.

## For contributors

A change to the signature keeps the above working:

- Give every `<img>` alt text that names what the icon stands for, as
  [CODING_STANDARDS.md](./CODING_STANDARDS.md) requires.
- Keep every fact in live text. An image never carries something the text does not.
- Keep every layout table `role="presentation"`.
- Check a new text colour's contrast against its background.
- Read the change in Outlook and Gmail with remote images blocked, then allowed, before opening the pull request.
- If a change adds a barrier it cannot avoid, say so in the pull request and open an issue with the
  `accessibility` label.

## Ownership

Ferran Buireu maintains the signature and this page. The page changes in the same commit as the behaviour it
describes.

## Feedback on this page

If something here is wrong, unclear or missing,
[open a documentation issue](https://github.com/fbuireu/email-signature/issues/new?template=documentation.yml). To
report a barrier, see [Reporting a barrier](#reporting-a-barrier).
