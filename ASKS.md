# ASKS

Distribution work blocked on the human (`@adiled`). This file is a dashboard, not a tracker. The actual work lives on GitHub issues (mechanical, project-scoped, trackable by anyone) or under-the-table (strategic, account-level, relationship-shaped).

## Direct asks (tracked on GitHub)

Mechanical and decision items with clean unblock paths. Filed as issues on the relevant repo so the project's own visitors can see what's needed and pick things up if they want to.

| Repo | # | Subject | Priority |
|---|---|---|---|
| [`distribution`](https://github.com/adiled/distribution/issues/1) | 1 | Set up `adiled/homebrew-tap` repo | P0 |
| [`distribution`](https://github.com/adiled/distribution/issues/2) | 2 | Decide `adils.me` publishing pipeline | P0 |
| [`distribution`](https://github.com/adiled/distribution/issues/3) | 3 | Anthropic MCP registry: research the publishing flow | P1 |
| [`clwnd`](https://github.com/adiled/clwnd/issues/32) | 32 | Restructure README front-matter for cold-visitor comprehension | P1 |
| [`clwnd`](https://github.com/adiled/clwnd/issues/33) | 33 | Add LICENSE (MIT) before any T2 launch beat | P0 |
| [`clwnd`](https://github.com/adiled/clwnd/issues/34) | 34 | Tag v0.1.0 release with handwritten release notes | P0 |
| [`opencode-dir`](https://github.com/adiled/opencode-dir/issues/2) | 2 | Record `/cd` demo (vhs or asciinema) and embed in README | P0 |
| [`opencode-dir`](https://github.com/adiled/opencode-dir/issues/3) | 3 | Tag v0.1.0 release with notes | P0 |
| [`opencode-dir`](https://github.com/adiled/opencode-dir/issues/4) | 4 | Add "why a plugin instead of upstream" framing to README | P1 |
| [`ccft`](https://github.com/adiled/ccft/issues/1) | 1 | Cut README to under 80 lines, move reference content to `docs/` | P0 |
| [`ccft`](https://github.com/adiled/ccft/issues/2) | 2 | Add LICENSE (Apache-2.0) | P0 |
| [`ccft`](https://github.com/adiled/ccft/issues/3) | 3 | Tag release with prebuilt universal Mac binary attached | P0 |
| [`rishta-lang`](https://github.com/adiled/rishta-lang/issues/1) | 1 | Resolve naming collision with the Hindi-keyword bhai-lang | P0 (rename done, downstream cleanup pending) |
| [`rishta-lang`](https://github.com/adiled/rishta-lang/issues/2) | 2 | Add LICENSE (Apache-2.0 recommended) | P0 |
| [`iksir`](https://github.com/adiled/iksir/issues/1) | 1 | Add "Operationally" section to README with concrete mechanics | P0 |
| [`iksir`](https://github.com/adiled/iksir/issues/2) | 2 | Record demo: submit, block, answer, complete | P0 |
| [`orch`](https://github.com/adiled/orch/issues/2) | 2 | Add LICENSE (Apache-2.0 recommended) | P0 |
| [`orchd`](https://github.com/adiled/orchd/issues/1) | 1 | Add comparison block to README: orchd vs docker-compose vs systemd vs nomad | P1 |
| [`orchd`](https://github.com/adiled/orchd/issues/2) | 2 | Add LICENSE (Apache-2.0 recommended) | P0 |
| [`orchd`](https://github.com/adiled/orchd/issues/3) | 3 | Add full runnable example to README (postgres + small app + healthcheck) | P1 |
| [`clwnd-playground`](https://github.com/adiled/clwnd-playground/issues/1) | 1 | Add LICENSE | P0 |
| [`ohlc-resample`](https://github.com/adiled/ohlc-resample/issues/15) | 15 | Update contributors block to use `@adiled` handle only | P1 |

When an issue closes, strike its row through here. Periodically reconcile: any item that goes stale on GitHub should either get a fresh comment, get closed with a note, or get rewritten.

## Held under the table

Items that don't belong on a public issue tracker. Account state, credential checks, strategic decisions in flight, relationship work, launch calendar. These live in `inbox/` (gitignored). Do not migrate them to GitHub issues even if it would feel tidier. The reason they're held privately is precisely because public visibility would harm the work.

Current under-the-table queue (carry over to `inbox/ASKS.md`):

- **crates.io credentials.** Run `cargo login`, confirm token reachable.
- **npm whoami / 2FA.** Verify `@adiled` (or chosen handle) is logged in and 2FA tokens are reachable.
- **PyPI account state.** Set up an API token, save in keyring.
- **Lobste.rs invite.** Request on `lobste.rs/invitations`. Submissions can take weeks.
- **Latent Space pitch shape.** Decide guest essay vs interview vs podcast appearance. Different prep effort, affects timing of pre-launch sequences.
- **Reddit account warming.** `/r/LocalLLaMA`, `/r/algotrading`, `/r/selfhosted`, `/r/rust`. New accounts get spam-filtered on launch day. Build organic activity on the relevant subs now.
- **X / Bluesky / Mastodon handle confirmation.** If dormant, post 5 to 10 organic technical posts before any launch beat.
- **Plausible.io decision.** Whether to set up privacy-respecting analytics on `adils.me` and per-project landing pages.
- **Launch calendar.** Pick a target month for the first T2 launch (recommended: `opencode-dir` first as low-risk pilot, then `clwnd` about 3 weeks later).
- **GitHub profile README (`@adiled/adiled`).** Update to reflect tier-ordered roster, not chronological.
- **Urdu/Pakistani communities outreach list.** For the `rishta-lang` cultural-arc essay distribution.
- **GitHub Sponsors.** Decide whether to enable on the profile. Sponsors-button presence is itself a maintenance signal.
- **GitHub notification routing.** Issues on T2 projects should reach you within an hour during launch windows.

After carrying these over, this section keeps a one-line pointer ("see `inbox/ASKS.md` for the current under-the-table queue") and stops listing them.

## Resolved

*(Empty. Strike-through closed items here when their issues land in a merged PR or a settled decision.)*

## How this file gets used

- **Weekly review.** Walk the table, check whether any issue closed. Strike through resolved rows. Open new issues for anything new and add it to the table.
- **Per-launch.** Surface the launch-blocking subset (P0 issues for the project being launched) into the launch-day checklist.
- **Quarterly.** Reconcile against the issue trackers on each repo. Stale issues get a comment or get closed with a note. The dashboard should not have orphaned entries.
- **Scope discipline.** If an item doesn't fit either column (not a clean public issue, not a clean private note), it doesn't belong here. Either reshape it until it fits or take it out.
