# ASKS

Distribution work blocked on the human (`@adiled`).

The registry of curated direct asks lives in [`asks.json`](./asks.json), not in this file. Markdown tables of `repo / issue-number / priority` go stale the moment GitHub assigns a number you didn't predict. JSON is the source of truth, GitHub is the live status, and this file is a thin pointer.

## How to read the registry

[`asks.json`](./asks.json) curates which GitHub issues across the portfolio are tracked as distribution asks. It does not duplicate state that GitHub already owns (open/closed, title, comments). It tracks only the curation: which repo, which issue number, which priority, optional one-line note for context.

To get the current dashboard, read live status from GitHub against the registry. A one-liner:

```bash
jq -r '.tracked[] | "\(.repo)#\(.number)"' asks.json | \
  xargs -I{} sh -c 'gh issue view {} --json number,state,title --jq "[.state, .title] | @tsv" | sed "s/^/{}\t/"'
```

Or, with `gh search`:

```bash
gh search issues "is:open" --owner adiled --json repository,number,title,state \
  --jq '.[] | "\(.repository.nameWithOwner)#\(.number)\t\(.title)"'
```

## Direct asks

Tracked in `asks.json`. Each entry maps to a GitHub issue on the relevant repo. Open the JSON or run the snippet above for the current state.

## Held under the table

Items that don't belong on a public issue tracker. Account state, credential checks, strategic decisions in flight, relationship work, launch calendar. These live in `inbox/ASKS.md` (gitignored).

## Workflow

- **New direct ask.** File the issue on the relevant repo. Add an entry to `asks.json` with the issue number, priority, and one-line note. No table maintenance, no number guessing.
- **Resolved ask.** Mark the issue closed on GitHub. Optionally update the entry's `state` field in `asks.json` to `closed-resolved` if the resolution carries a story worth keeping in the registry. Otherwise leave it; the live state from GitHub is the authoritative status.
- **Walk-back / supersession.** Update the issue body or close-with-comment on GitHub. Update the `note` in `asks.json` to record the reasoning. Do not edit history into both places.

## Why JSON, not markdown

A markdown table tracking `repo / number / priority / status` requires the author to keep numbers right after every parallel-issue creation, copy-paste error, or repo rename. The fragility surfaces fast: cross-checking the table against actual GitHub state after a single batch of issue creation revealed mis-mapped numbers across most repos.

JSON is structured. The registry is small enough to read by hand. Tooling on top is trivial when needed (`jq`, `gh`, two pipes). Live state belongs to GitHub, not to a checked-in file. This separation is the point.
