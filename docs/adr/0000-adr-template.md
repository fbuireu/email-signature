# 0. ADR template

Date: 2026-07-30

## Status

Template. Not a decision — copy this file, do not edit it in place.

## Context

Copy this file to `NNNN-kebab-title.md`, numbered one above the highest existing ADR, and add a row for it to the index in [`ARCHITECTURE.md`](../../ARCHITECTURE.md). The `# N. Title` heading carries that same number and states the decision in one line; the file slug is the short form of it.

Write an ADR only when the decision is **hard to reverse**, **surprising without context** and **the result of a real trade-off**. All three, or it is not an ADR.

This section holds the forces, not the answer: what the situation was, what the alternatives were, and why the obvious option was not obviously right. Someone reading it two years from now has none of the context you have today — the constraint that made this hard is the part they will be missing.

In this repository the constraint is almost always the same one, and it is worth naming explicitly: **a sent email cannot be edited**. Anything a recipient's mail client fetches at read time is a promise made to every message already in every inbox.

## Decision

What was decided, in the present tense, as a rule the repository follows: "a published asset path is never renamed", not "we decided not to rename asset paths". Name the alternative that was rejected and why, since that is what stops it being re-proposed. Point at the markup by what it is rather than by line number — an `index.html:51` citation rots the moment anything above it moves.

## Consequences

What follows from this, including what it costs. The bullets someone needs before touching the repository:

- What is now load-bearing and must not be removed, and what breaks if it is.
- What this makes harder, slower, or impossible. An ADR with no cost recorded is usually not describing a real trade-off.
- Where the decision bites in the rest of the docs — a Gotchas bullet in [`CLAUDE.md`](../../CLAUDE.md), a [`CONTEXT.md`](../../CONTEXT.md) entry, a section of [`ARCHITECTURE.md`](../../ARCHITECTURE.md) that has to link back here. An ADR that only the index links to will not be read.
