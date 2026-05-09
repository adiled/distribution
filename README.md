# distribution

Operational source of truth for distributing `adiled`'s open-source projects. The role this repo enacts:

> **Distribution Chief.** Analyze the active project portfolio, formulate a distribution policy, map every viable channel to the projects that suit it, define the metrics that say whether a launch worked, and surface the human-only asks (account creation, OAuth grants, posting) that block execution.

This repo is not a launcher and not a CRM. It is a written mind: every public-facing decision about what gets shipped, where, when, in what voice, against which target gets recorded here. Commits are the changelog of distribution thinking.

## Layout

```
distribution/
├── README.md             # this file. charter and how to use the repo
├── policy.md             # the operating policy (tiering, voice, license, naming)
├── channels.md           # every distribution channel under consideration, with effort/yield
├── metrics.md            # what we count and what counts as winning, per tier
├── ASKS.md               # human-only tasks blocking distribution work
└── projects/
    ├── _index.md         # roster of active projects with tier and current status
    └── <project>.md      # per-project dossier: positioning, audience, plan, current state
```

## How to use it

- **`projects/_index.md`** is the wide view. Open this first to see what's in play.
- **`policy.md`** is the constraint surface. Read it before drafting a launch. It tells you what to and what not to do.
- **`channels.md`** is the catalog. Each channel has a one-line pitch, a per-project fit note, and an effort tier so you don't accidentally schedule a launch heavier than the project warrants.
- **`metrics.md`** is the scoreboard. Numbers that mean something, plus numbers that don't.
- **`ASKS.md`** is the dashboard. Direct asks are tracked as GitHub issues on the relevant project repos and indexed here. Strategic and account-shaped items are held under the table and noted only by pointer.

## Cadence

- **Weekly.** Review `projects/_index.md`, update tier and status. Clear or escalate items in `ASKS.md`.
- **On every release.** The originating project's dossier gets a "shipped" entry with the channels that fired and what they returned.
- **Quarterly.** Revisit `policy.md` and `metrics.md`. The portfolio changes, and the policy must too.
