# 7. Actions are pinned by digest and auto-merged

Date: 2026-07-30

## Status

Accepted.

## Context

This repository has no runtime dependencies ([ADR 0005](./0005-no-build-step.md)), so its entire supply chain is the set of third-party GitHub Actions its four workflows call: `actions/checkout`, `lycheeverse/lychee-action`, `peter-evans/create-issue-from-file`, `dependabot/fetch-metadata` and `zizmorcore/zizmor-action`. Those actions run with a token in a repository whose contents are, per [ADR 0002](./0002-published-asset-paths-are-immutable.md), promises made to already-sent mail.

The standard `uses: actions/checkout@v7` is a **mutable** reference: the tag can be repointed at any commit at any time, so a compromised upstream account can change what runs here without any change landing in this repository. Pinning to a digest closes that, at the cost of an unreadable reference that only a bot can sensibly maintain — and once a bot is opening those pull requests every fortnight, a human reviewing each one by hand is a review that degrades into rubber-stamping within a month.

That is the real tension: pinning demands automation to stay current, and automation is precisely the thing that would merge a malicious update unattended. The two mitigations that make it survivable are that a digest cannot be silently repointed (a new digest is a visible diff) and that a compromised release is usually discovered within days of publication.

## Decision

Every third-party action is pinned to a **full commit SHA** with the human-readable version as a trailing comment, and Renovate keeps those digests current under an automerge policy calibrated by blast radius:

- `pin`, `pinDigest`, `digest`, `patch`, `minor` and lock-file maintenance updates are approved and squash-merged unattended.
- `major` updates are labelled `major-update` / `review-required` and never merged automatically.
- `minimumReleaseAge` is **4 days** — nothing is eligible until it has been public long enough for a compromised release to surface.
- Renovate runs on the 1st and 15th, but `vulnerabilityAlerts` are scheduled `at any time` so security fixes bypass the cadence.
- `zizmor` (`.github/workflows/zizmor.yml`) statically audits the workflows themselves on every push and pull request, and `permissions:` is declared explicitly per workflow rather than inherited.

## Consequences

- **A version bump is a two-part edit and the comment is not decorative.** The SHA is what runs; the `# v7.0.0` comment is the only thing making the reference legible. Changing one without the other produces a workflow file that lies about what it executes.
- The 4-day `minimumReleaseAge` means this repository is deliberately never on the newest release. That is the point, and it is a cost — a genuine fix waits four days too.
- **Auto-merge is only as safe as the window it leaves.** A malicious patch release that survives four days undetected merges here without a human ever looking at it. The policy accepts that risk for patch and minor; the line is drawn at major, which is a proxy for blast radius rather than for maliciousness, and a poor proxy at that. It was chosen because reviewing everything means reviewing nothing.
- `.github/workflows/dependabot-auto-merge.yml` exists but there is **no `.github/dependabot.yml`**, so Dependabot is not configured to open version-update pull requests here at all. That workflow currently applies only to the security updates GitHub raises on its own, and Renovate is the bot doing the actual work. Two bots are configured where one is running.
- Auto-approval and auto-merge run on `secrets.PAT` rather than `GITHUB_TOKEN`, because a token cannot approve its own pull request. That PAT is a standing credential with write access, and its expiry is an unmonitored single point of failure: when it lapses, the automation stops silently.
