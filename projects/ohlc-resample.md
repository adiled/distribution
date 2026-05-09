# ohlc-resample

**Tier:** T1 — Anchor • **Repo:** https://github.com/adiled/ohlc-resample • **npm:** [`ohlc-resample`](https://www.npmjs.com/package/ohlc-resample) • **License:** LGPL-3.0 • **Stars:** 26

## What it is

A TypeScript library + CLI that resamples market data — OHLCV candles, raw trades, ticks — between time frames. CCXT-compatible. Single dependency. Has its own CLI (`ohlc-resample -i in.csv -b 60 -n 300`), supports JSON & CSV, supports pipe input, supports two equivalent JSON shapes (object and CCXT-tuple).

## Why it matters to the portfolio

This is the credibility anchor. It is the one project in the portfolio that strangers depend on for real work. Its job in the distribution strategy is **to keep working, keep being maintained, and keep showing up first** when someone visits the GitHub profile and asks "do they ship?".

Everything else in the portfolio is downstream of the trust this project produces.

## Audience

- Quantitative traders / algotrading hobbyists building backtest pipelines.
- CCXT users (this is a known tag in their ecosystem).
- Anyone with `[time, open, high, low, close, volume]` data who wants different timeframes without rolling their own group-by.

## Distribution state — what's done

- ✅ Published on npm with a real version history.
- ✅ Changesets-based release flow with changelog discipline (see README §Releasing).
- ✅ npm provenance attestation enabled in the release workflow.
- ✅ TypeScript types exported.
- ✅ Coverage badge wired to Coveralls.
- ✅ CCXT-compatible signature documented up-front (prime SEO term for the audience).

## Distribution state — gaps

- ❌ No landing page (`ohlc-resample.dev` or similar). GitHub README is the only surface. Low-cost win.
- ❌ Not yet listed on awesome-algotrading / awesome-quant / awesome-ccxt curated lists. Each is a 30-minute PR.
- ❌ No reverse-citation tracking — we don't know which projects depend on it (`Used by` count not actively monitored).
- ❌ Author name in README is the legal name, not `adiled`. Conflicts with `policy.md` §2. (See `ASKS.md`.)
- ❌ No demo content beyond code blocks. A 30-second asciinema of the CLI converting CSV→JSON resampled would be a strong README addition.

## Plan (next 90 days)

The mode is **maintain + light promotion**, not relaunch.

1. **Week 1:** README pass — replace legal-name contributor block with `@adiled`; add a short asciinema/`vhs` demo above the install instructions; add a "Who uses this" section if any external dependents exist (check `npm view ohlc-resample dependents` and GitHub's "Used by").
2. **Week 2:** PRs to relevant awesome-lists:
   - `awesome-algotrading` (well-maintained, accepts PRs)
   - `awesome-ccxt` (smaller but direct fit)
   - `awesome-quant` (large, lower per-PR yield but worth a single submission)
3. **Week 3:** Add `ohlc-resample` to the `adils.me` portfolio above the fold, with one-line pitch + npm badge.
4. **Ongoing:** Issue triage within 7 days. Any version that introduces a non-bug-fix change gets a paragraph on `adils.me`.

## What we are *not* doing

- **No Show HN.** This project is a library for a niche; HN does not love niche libraries unless they're solving a marquee problem. The ROI doesn't justify the launch effort, and a tepid HN reception is worse than no HN.
- **No version-bump-as-launch-beat.** Releases are routine. Posting on social for every patch dilutes the signal.
- **No rewriting it in Rust to "modernize."** The asset *is* the stable, working, npm-published TypeScript library that just keeps working. Rewrite ≠ improvement.

## Metrics

Per `metrics.md`:
- Hold or grow weekly npm downloads vs trailing 12-week median.
- 0 open critical bugs > 30 days.
- < 7 day median issue first-response.
- Latest release within 2 weeks of main HEAD.

## Launches

*(no launch beats scheduled — maintenance mode)*

## Notes

The repo's existing release pipeline (Changesets + GitHub Actions + npm provenance) is the gold-standard template the rest of the TS portfolio (`clwnd`, `opencode-dir`) should adopt. When standardizing release tooling across the portfolio, copy from here.
