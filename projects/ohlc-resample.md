# ohlc-resample

**Tier:** T1 (Anchor) • **Repo:** https://github.com/adiled/ohlc-resample • **npm:** [`ohlc-resample`](https://www.npmjs.com/package/ohlc-resample) • **License:** LGPL-3.0 • **Stars:** 26 • **Latest published:** v1.2.1 on npm (over a year old) • **Staged next:** v2.0.0 on `feat/cli-improvements`

## What it is

A small TypeScript library for resampling OHLCV candles and tick streams between time frames, with a CLI (`ohlc`) wrapped around the same functions. The library exposes three named functions (`resampleOhlcv`, `resampleTicksByTime`, `resampleTicksByCount`) and a small set of types (`OHLCV`, `IOHLCV`, `TradeTick`). It accepts the CCXT tuple shape (`[time, open, high, low, close, volume]`) and the equivalent object shape (`{time, open, ...}`), and the return type follows the input shape via TypeScript overloads. The CLI (`ohlc`) reads CSV or JSON from a path or a pipe, auto-detects format and shape, runs the same resampler, and writes CSV or JSON to a path or stdout.

The repo's README emoji is the candle 🕯️, which honors the project's stated aesthetic per `policy.md` §3 and stays. The package name does not expand to anything; treat the four words `ohlc-resample` as the name.

The substance, in plain technical terms: a tight grouping/aggregation routine on six-tuples keyed by time-bucket boundary, plus a tick-to-candle aggregator with optional gap-fill, plus a CLI that wraps the library with format and shape adapters. Single dependency at runtime is `lodash`, plus `commander` for the CLI. The library is pure Node JS, no native bindings, no platform binaries, no per-host build step.

## Why it matters to the portfolio

This is the credibility anchor under `policy.md` §8. It is the project in the portfolio that strangers actually depend on for real work, the one where load-bearing usage exists today (584 npm downloads in the trailing 30 days as of 2026-05-08, 140 in the trailing week, with at least one external dependent surfaced via GitHub's network/dependents page). Its job in distribution is to keep working, keep being maintained, and to keep showing up first when a stranger lands on the GitHub profile and asks "do they ship things people use." Everything else in the portfolio leans on the trust this project produces.

The recent work on the project also functions as the gold-standard release-tooling template for the rest of the TS portfolio. The `.github/workflows/release.yml` direct-commit Changesets flow with npm provenance, the hosting-independence layering documented in `CLAUDE.md`, the `install.sh` that brings its own Node from nodejs.org when the host's runtime is too old: these patterns will be reused in `clwnd`, `opencode-dir`, and any future TS package the portfolio publishes.

## Audience

The audience is narrow and specific. It does not match a single market category cleanly.

The first kind is the algotrading hobbyist or freelance quant building backtest pipelines on retail crypto data. They already have a CCXT install or a CSV dump of `[time, open, high, low, close, volume]` rows and they want 1m candles aggregated into 5m, 15m, or 1h without writing a custom group-by. They live on `r/algotrading`, in CCXT's GitHub issues and Discord, and on the awesome-systematic-trading list. They install the library, not the CLI.

The second kind is the data analyst or notebook user who has CSVs on disk and does not want to bring a JS toolchain into a Python or shell pipeline. They install the CLI through `install.sh`, get a single `~/.local/bin/ohlc` launcher with a Node runtime alongside it, and use it in the same shape as `csvkit` or `jq`: `ohlc -i in.csv -b 60 -n 300 -f json`. The `install.sh` channel was added in the staged 2.0.0 specifically for this user, who is not a JS developer at all.

The third kind is the JS or TS developer who needs OHLCV resampling for a charting frontend, a paper-trading sim, or a one-off conversion tool. They `npm install ohlc-resample`, import `resampleOhlcv`, get types out of the box, and never touch the CLI. The package's `engines.node >=22.12.0` is a deliberate cut from the staged 2.0.0; older Node deployments stay on 1.2.1 without disruption.

The audience does not include high-frequency traders running production pipelines with millions of candles per second. The library is array-in/array-out, sorts in place, has no streaming surface yet (issue #9 names the gap), and is not load-bearing infrastructure for that workload. It is the tool for the in-between case where the data is already on disk or in memory, and the question is "give me the same data on a different clock."

## Distinguishing claim

Honest answer: there is no novelty claim here, and that is fine for a T1 anchor.

The closest thing to a distinguishing property is shape preservation across the two CCXT-adjacent JSON shapes. `resampleOhlcv` is overloaded so a tuple-array input returns a tuple-array, an object-array input returns an object-array, and a union-typed input still compiles for 1.x callers. The CLI auto-detects shape on JSON input and preserves it through the pipeline by default, with `-s array|object|auto` to override. Most adjacent libraries (including the actively-maintained `candlestick-convert`, which is the project's lineage parent) pick one shape and force callers to convert. This is a usability property, not a novelty.

The CLI plus library plus install.sh combination is also unusual at this size. `candlestick-convert` is library-only on npm. Most CCXT-adjacent OHLCV utilities ship as Python notebooks or Python packages. Shipping the same code as a JS library, a registry-installable CLI, and a `curl | sh` Node-bundling installer in one repo gives the project three reach surfaces from one source. It is structural to the staged 2.0.0, not a bonus.

The right framing is the one in `metrics.md`'s honest framing paragraph: this project's distinguishing property is its track record. Four published versions on npm, recurring weekly downloads, surviving from 2021 to 2026 without going stale, and now a release pipeline that produces a new version every time a changeset lands. That is the asset.

## Distribution state, current

The library is live on npm at v1.2.1, has been since the 1.2.x line stabilized, and continues to receive recurring traffic without any active promotion. The npm tarball ships TypeScript declarations alongside the JS, the package author field carries both legal name and email per `policy.md` §2, and the package keywords (`ohlc`, `ohlcv`, `resample`, `tickchart`, `candlestick`, `ccxt`, `stock`, `chart`, `analysis`, `cli`) are correct for npm search. The repo description on GitHub reads ":candle: Resample (inter-convert) market trades, ticks or OHLCV data to different time frames." Topics on the repo are `ohlcv`, `ohlcv-resample`, `ohlcv-convert`, `crypto`, `trading`, `technical-analysis`, `candlestick-chart`, `candlestick`, which is dense and on-target.

License is LGPL-3.0, declared in `package.json` and present as `COPYING` at the repo root. The license is locked per `policy.md` §4 ("already shipped, leave alone"). LGPL-3.0 is a slightly unusual choice for an npm library and may surface occasionally as a question; the answer is that the project shipped under it from day one and changing it now would be a breaking action against existing consumers.

The recent code work, on the `feat/cli-improvements` branch as of 2026-05-09 and not yet merged or published, is substantial. The branch contains a single staged changeset (`.changeset/working-cli-and-modern-tooling.md`) marked as a major version bump to 2.0.0, with a hand-written body that becomes the GitHub Release note. The branch turns the previously non-functional CLI stub into a working `ohlc` binary with `--input-format`, `-s, --shape`, pipe input that respects `-i` over stdin even in non-TTY contexts, and stderr warnings on malformed CSV rows. The library side gets overloaded signatures preserving 1.x union-call compatibility, type re-exports from the package entrypoint, and a JSDoc fix on `resampleOhlcvArray`. Build and tooling: jest replaced with vitest 4 (cold-start ~9s to ~2.5s), TypeScript bumped to 6.x, `engines.node` raised to `>=22.12.0`, `chalk` and `coveralls` and `fast-csv` and `ts-node` removed, CI matrix on Node 22.x and 24.x.

Release machinery is layered correctly per `CLAUDE.md`. The Changesets CLI handles version bump, CHANGELOG regeneration, and consumed-changeset cleanup. The npm publish step has provenance via OIDC. The release workflow is a direct-commit flow on `main` with a `[skip ci]` self-bump commit, tag, and `gh release create` with the just-prepended CHANGELOG entry as release-note body. Hosting independence is documented: the only GitHub-specific layer is `release.yml`, and migrating forges is a single-file replacement.

The `install.sh` pathway is real. It detects host Node, accepts it if `>=22.12`, otherwise downloads the matching official Node tarball from nodejs.org into `~/.ohlc/runtime/`, runs `npm install --prefix` to drop the package into `~/.ohlc/lib/`, and writes a `~/.local/bin/ohlc` launcher that execs the chosen Node against the installed CLI. macOS x64/arm64 and Linux x64/arm64 are covered; Windows is not. The installer is the path for the second-kind audience above.

Open functional issues are #8 ("Feature: Extend package with CLI") and #9 ("Add support for streams and large data formats"). #8 was filed 2022-12-01 and its checklist is essentially done in the staged 2.0.0, so it should be closed when 2.0.0 ships. #9 is a real future enhancement and stays open.

The previously-flagged distribution issue #15 ("Update contributors block to use @adiled handle only") was filed and closed on 2026-05-09 (a same-day pass), so the prior dossier's "to-do" inheritance from that issue is already cleared. The README contributors block correctly carries both the legal name with email and `@adiled` handle on separate lines per `policy.md` §2.

## Distribution state, gaps

The biggest gap is that v2.0.0 is staged but not shipped. The branch `feat/cli-improvements` has 13 commits ahead of `origin/main`, with a major version bump and a working CLI that the README already documents. Every other distribution gap below assumes 2.0.0 lands first; promoting the project against a stale 1.2.1 npm version while a working 2.0.0 sits in a feature branch would be confusing.

Once 2.0.0 ships, the README's "CLI Usage" section names the binary as `ohlc-resample` in every example. The actual binary, declared in `package.json#bin` and written by `install.sh`, is `ohlc`. The npm tarball post-install puts `ohlc` on PATH, the install.sh launcher writes `~/.local/bin/ohlc`, and `ohlc-resample` does not resolve to anything. This is a small correctness fix to the README and worth catching before any awesome-list PR sends a stranger to the wrong command.

The README claims "CCXT support" in the bullet list. That phrase is true in the narrow sense (the tuple shape `[time, open, high, low, close, volume]` matches `exchange.fetchOHLCV()` output), but it does not mean the library imports or talks to CCXT. A first-time visitor scrolling the README and skipping to "CCXT support" might expect a deeper integration. Tightening to "CCXT-compatible OHLCV shape" or similar is one line of copy that prevents that misread.

There is no tagged GitHub Release. The release workflow will create one as soon as 2.0.0's changeset lands on `main`. Until then, the GitHub Releases page is empty, which contradicts the npm version history visible at npmjs.com. Strangers checking GitHub Releases as a freshness signal currently see nothing.

Not on `awesome-ccxt` (suenot/awesome-ccxt). The list's Utils section already lists the project's lineage parent `candlestick-convert`. `ohlc-resample` belongs in the same row, with a one-line description that names the CCXT tuple compatibility, the additional CLI surface, and the Node-floor difference. The list's last push was 2023-09; cadence is slow but PRs still merge.

Not on `wangzhe3224/awesome-systematic-trading`. The "Basic Components" section is the right slot, and the list explicitly invites PRs ("Please raise a PR if you found some good fit projects"). The list is active.

Not on `joelowj/awesome-algorithmic-trading`. This list does not have a JS/TS section visible in the README, leans heavily Python and educational. Lower fit, lower yield, skip unless someone explicitly asks.

Not on `wilsonfreitas/awesome-quant`. The list covers OHLCV-related tooling but is dominated by Python and Julia entries; the JS subsection is small. A submission is plausible but lower-yield than `awesome-ccxt` and `awesome-systematic-trading`. Worth one PR after the higher-fit lists land.

`r/algotrading` is named in `channels.md` as a `ohlc-resample` fit. The audience there is friendly to OSS tools but allergic to repeat self-promotion. One thoughtful post per major version with a real before/after example is the cadence ceiling.

No demo asset. The README has code blocks but no asciinema or vhs recording of the CLI converting a real CSV to a different timeframe and writing JSON to stdout. Twenty seconds of `ohlc -i btc-1m.csv -b 60 -n 300 -f json | head` would do more for the README than any prose.

The `adils.me` portfolio does not yet feature `ohlc-resample` above the fold. The first thing a hiring manager or peer-developer sees on the personal site should include the npm-installable, downloads-bearing project alongside the agentic-systems work. This is a one-line addition to the portfolio page and a `policy.md` §8 reinforcement (the anchor's job is to be visible).

There is no reverse-citation tracking. GitHub's `network/dependents` view shows one external dep (`trasherdk/node-test-snippets`) at the time of this dossier, and `npm view` does not surface dep counts directly. Knowing which projects depend on `ohlc-resample` would let the README add a "Used by" section with one or two real names, which both `metrics.md` §North-star and `policy.md` §6 push for.

## Distribution state, aspirational

Streams and large-data support is the real future feature, captured in open issue #9. Today the API is array-in array-out, which means a 100MB tick CSV has to fit in memory before resampling can run. A streaming variant of `resampleOhlcv` keyed by the same time-bucket boundary logic, plus a CLI flag to read line-delimited JSON or chunked CSV, would extend the audience to operators handling exchange-firehose-shaped data. This is a substantial code change, not a launch beat, and belongs after 2.0.0 stabilizes.

A small landing page at `ohlc-resample.dev` or under `adils.me/ohlc-resample` would outrank GitHub for the project name in search, collect optional email signups, and act as the canonical link target for awesome-list PRs and the rare blog post that picks the project up. This is a `channels.md` "Per-project landing page" optional but listed under SEO. Not a launch dependency.

A short blog post on `adils.me` titled in plain technical register (a candidate: "ohlc-resample 2.0: working CLI, install.sh, Node 22 floor") would do double duty. It would announce 2.0.0 to anyone subscribed to the blog, and it would give awesome-list submissions a permanent link target richer than the GitHub README. The post would document the behavioural change (the CLI was a non-functional stub in 1.x and ships now), the install paths (`npm install` and `curl | sh`), the shape-preservation overloads, and the engines bump. This is light promotion, not relaunch, and matches the T1 distribution mode in `policy.md`.

A "Used by" README section, even with two or three real names, is the highest-trust signal a T1 project can carry per `metrics.md` §North-star. It triggers when external dependents surface organically. The right time to add the section is the first time someone lands a real production usage that they are willing to be named in.

## Plan

The mode is maintain plus light promotion, not relaunch. The project does not get a Show HN, an X thread launch, or a coordinated multi-channel push. The 2.0.0 ship and the awesome-list PRs are the ceiling on active distribution work this quarter.

Pre-ship for 2.0.0, in order:

1. Fix the README CLI examples to use `ohlc` (the actual `bin` name) instead of `ohlc-resample`. One-line correctness fix on the `feat/cli-improvements` branch before merge.
2. Tighten the README's "CCXT support" bullet to "CCXT-compatible OHLCV shape" or similar, so a first-time visitor does not expect a CCXT integration.
3. Merge `feat/cli-improvements` into `main`. The release workflow consumes the staged changeset, bumps `package.json` to 2.0.0, regenerates `CHANGELOG.md`, deletes the consumed `.changeset/*.md`, publishes to npm with provenance, tags `v2.0.0`, and creates the GitHub Release with the prepended CHANGELOG entry as body. No human action between merge and live release once `NPM_TOKEN` is set.
4. Verify the published 2.0.0 tarball boots clean. `npx ohlc@2.0.0 --version` from a fresh shell, plus `curl -fsSL .../install.sh | sh` on a fresh macOS user with no Node and on a Linux box with Node 18.x (forces the bundled-Node path).
5. Close issue #8. The CLI feature checklist is done.

Light promotion, after 2.0.0 is live, in order over the next 30 to 60 days:

1. Add `ohlc-resample` to the `adils.me` portfolio above the fold with one-line pitch, version badge, and npm install hint. This is the cheapest visibility win and matches the `channels.md` per-project guidance.
2. PR to `suenot/awesome-ccxt` Utils section. One-line entry naming the CCXT tuple compatibility and the CLI surface. Submit alongside (not as a duplicate of) the existing `candlestick-convert` entry.
3. PR to `wangzhe3224/awesome-systematic-trading` "Basic Components" section. List is open to PRs and the section is the right fit.
4. Post a `r/algotrading` thread on the day 2.0.0 ships with a concrete before/after: "I had 1m BTC candles in CSV, here is one command to get 5m, 15m, and 1h candles in JSON via the new CLI." Include the install one-liner. One post, no follow-up.
5. Optional, lower priority: PR to `wilsonfreitas/awesome-quant`. Lower yield given the JS subsection's size, but a single submission costs little.

Sustaining work, ongoing:

1. Issue first-response within 7 days per `metrics.md` T1 target. Current open issues are #8 (close on 2.0.0 ship) and #9 (acknowledge with the streaming roadmap, leave open as the real future feature).
2. Monitor npm weekly downloads against the trailing 12-week median per `metrics.md`. A drop is the leading indicator of T1 degradation.
3. Watch GitHub `network/dependents` for new external dependents. The first one credible enough to ask for a "Used by" namedrop triggers the README addition.
4. Any non-trivial release (a streaming surface from #9, a CCXT format that surfaces in a new exchange shape, a Windows install.sh path) gets a paragraph on `adils.me`. Routine patches do not.

## What we are not doing

Not running a Show HN. The project is a niche library; HN does not love niche libraries unless they solve a marquee problem, and a tepid HN reception is worse than no HN. This is consistent with the prior dossier's call and with `policy.md` §7's "splash-launch" warning.

Not posting on every patch. Releases are routine. Posting on social for every patch would dilute the audience signal and burn the trust the project's quiet reliability has built.

Not rewriting the library in Rust or Bun or any other runtime "to modernize." The asset is a stable, working, npm-published, Node-runtime TypeScript library with a four-year track record. The `CLAUDE.md` constraint is explicit on this point and the constraint is correct: rewriting is not improvement, it is a reset on the trust accumulated. The same logic applies to a wholesale switch to a non-Node JS runtime.

Not switching off LGPL-3.0. The license is locked per `policy.md` §4. LGPL-3.0 attracted no friction in four years and changing it would be a breaking action against existing consumers without offsetting benefit.

Not pitching the project as a "trading utility" or "OHLCV utility" or "CCXT helper library" without specifics. Those category labels were imported in earlier framing passes and they flatten the project into the same row as five or ten other libraries that look superficially similar. The honest pitch is the one in this dossier's "What it is" paragraph: a small TypeScript library and CLI for resampling OHLCV candles between time frames, with shape-preserving overloads, two install paths, and a four-year track record.

Not coordinating an X thread for 2.0.0. T1 mode is light promotion. A single `r/algotrading` post and the `adils.me` portfolio refresh are the ceiling. Saving X-thread effort for the T2 launches that need spike-to-tail conversion.

## Metrics

Per `metrics.md` T1 anchor targets.

| Metric | Target |
|---|---|
| Weekly npm downloads | hold or grow vs trailing 12-week median |
| Issue first-response | < 7 days |
| Open critical bugs > 30 days | 0 |
| Latest published version drift from main | < 2 weeks |
| `Used by` count (GitHub dependents) | grow over time |

Project-specific signals to watch.

The 2.0.0 download curve. Major versions on long-stable libraries sometimes see a brief dip as old consumers stay on the prior major and new installers ramp up. The trailing 12-week median is the right baseline; a sustained drop after 2.0.0 is the signal that the engines bump or a behavioural CLI change closed an audience door.

The `engines.node >=22.12.0` constraint. Older Node deployments cannot install 2.0.0; they stay on 1.2.1 and the 1.x line is implicitly frozen. If issues open from users on Node 18 or 20 asking for backports, that is a signal to keep 1.x patchable rather than archive it. The current expectation is that the audience is on current LTS or self-hosting Node via `install.sh`.

External issue shape. Issues from named users about real production usage are the highest-trust signal `metrics.md` §North-star captures. Drive-by duplicate-of-#9 issues do not count; one real backtest pipeline reporting a behavioural quirk does.

## Launches

None. T1 mode is maintain plus light promotion. The 2.0.0 ship is a release, not a launch beat. The post-ship awesome-list PRs and the `adils.me` portfolio update are continuous distribution, not coordinated launches.

## Notes

The previous dossier described the project as "CCXT-compatible. Single dependency. Has its own CLI." The first claim is correct in the narrow tuple-shape sense and was tightened in the "What it is" paragraph above. The second claim ("single dependency") is approximately true, since `lodash` is the only runtime dependency the library imports, but `commander` ships alongside for the CLI and is also runtime. The README would be more accurate as "two runtime dependencies" or could simply drop the count.

The previous dossier flagged the contributors-block issue (#15) and the lack of a landing page. Issue #15 is closed (2026-05-09); the contributors block is correct under `policy.md` §2. The landing page absence remains, downgraded to optional aspirational rather than a gap to close before any specific beat.

The previous dossier also implied that a real version history exists on the GitHub Releases page. It does not. The npm version history is real (1.1.1, 1.1.3, 1.2.0, 1.2.1) but no GitHub releases or tags have been cut. The release workflow will produce them starting at 2.0.0; the four prior npm releases will not retroactively appear on the Releases page.

The genealogy fact is worth surfacing once for the awesome-list PRs. `ohlc-resample` originated as a fork of `candlestick-convert` (now maintained by `valamidev`). The README's "Past authors of `candlestick-convert`" line is the maintainer's correct acknowledgment. `candlestick-convert` is a sibling project with its own active 7.x track. Awesome-list submissions should not pretend the two are unrelated and should not pretend they are the same; both belong on the lists, with adjacent one-line descriptions that name the difference (CCXT tuple plus object shape preservation, CLI surface, Node 22 floor).

The 2.0.0 staged changeset closes issue #8 explicitly. When 2.0.0 ships, the GitHub Release body (auto-generated from the changeset body) will name "Closes #8" and GitHub's auto-close mechanism will close the issue. No manual close needed.

## Research log

Sources consulted, in order:

- Local clone at `~/ohlc-resample`. `package.json`, `README.md`, `AGENTS.md` and `CLAUDE.md` (symlinked), `COPYING`, `install.sh`, `tsconfig.json`, `vitest.config.ts`, `.changeset/config.json`, `.changeset/working-cli-and-modern-tooling.md`, `.github/workflows/nodejs.yml`, `.github/workflows/release.yml`.
- Source code: `src/index.ts`, `src/lib.ts`, `src/types.ts`, `src/cli.ts`. Tests at `__tests__/cli.ts`, `__tests__/ohlcv.ts`, `__tests__/ticks.ts`, `__tests__/utils.ts`.
- Git history: full log on `feat/cli-improvements` (13 commits ahead of `origin/main`), with attention to `43672e6` (drop bun-compile, install.sh BYO-Node), `c899689` (install.sh added), `674d0d1` (Node LTS bump, deps refresh, TS 6, vitest 4), `843a868` (preserve union-typed signature for resampleOhlcv), `0cc9b6c` (PR-driven release flow switched to direct-commit), `22f5603` (Changesets adoption), `88317d8` (2.0.0 changeset draft), `540f98e` (CLI feature), `8c7c932` (overloaded signatures, default-export aliases preserved), `0f24b18` (the security PR that capped 1.2.1 to its current state).
- GitHub issues, all three: #8 (CLI feature, open, essentially done by 2.0.0), #9 (streams support, open, real future enhancement), #15 (contributors block, closed 2026-05-09).
- `gh repo view adiled/ohlc-resample` for description, license, topics, default branch, stargazers.
- `gh release list --repo adiled/ohlc-resample` (empty: no releases).
- `npm view ohlc-resample` for published versions and registry metadata.
- `https://api.npmjs.org/downloads/point/last-week/ohlc-resample` and `last-month`: 140 weekly, 584 monthly downloads as of 2026-05-08.
- `https://github.com/adiled/ohlc-resample/network/dependents` HTML scrape: one external dependent (`trasherdk/node-test-snippets`).
- Existing dossier at `projects/ohlc-resample.md`.
- `policy.md` (T1 anchor mode, license lock, voice settings, identity rules), `channels.md` (per-project guidance for ohlc-resample, plus aggregator list cross-reference), `metrics.md` (T1 targets), `projects/_index.md`, `projects/ccft.md` and `projects/clwnd.md` (dossier shape and rigor reference).
- Web research scoped to the project's actual claim: `suenot/awesome-ccxt` Utils section content, `wangzhe3224/awesome-systematic-trading` contribution policy and section structure, `wilsonfreitas/awesome-quant` JS subsection density, `joelowj/awesome-algorithmic-trading` README structure, valamidev/candlestick-convert sibling project status.

Sources rejected as imported-category framings:

- "CCXT helper library" frame. The project does not import CCXT, does not call any exchange API, and does not depend on `ccxt` at runtime. Calling it a CCXT helper imports an integration claim the code does not back up. The honest claim is "CCXT-compatible OHLCV shape," which is shape compatibility on a six-tuple, not a helper relationship. Using the helper-library frame would land awesome-list PRs on the wrong section (helpers and toolkits) instead of the right one (utils).
- "Trading utility" frame. The category label is too broad and lands the project next to backtest engines, exchange clients, and indicator libraries, none of which are the project's actual neighbours. The right neighbours are `candlestick-convert` and a small handful of OHLCV-resample-shape Python utilities (TA-Lib's `resample`, pandas-finance's resamplers). The category label was in the prior dossier as shorthand and is dropped here.
- "OHLCV utility" frame. Slightly better than "trading utility" but still too broad. The README's keyword list (`ohlc`, `ohlcv`, `resample`, `tickchart`, `candlestick`, `ccxt`, `stock`, `chart`, `analysis`, `cli`) is the right granularity. "Resampling between time frames" is the operative claim; "OHLCV utility" loses the time-frame conversion specifically.
- "Algorithmic trading library" frame. The code does no trading. It does data shape conversion. Shipping it to `joelowj/awesome-algorithmic-trading` under that frame would be off-target and would land in a list dominated by Python frameworks where the JS subsection is thin.

What surprised me in step 4, when writing the project's-own-voice description.

The previous dossier's framing was thinner than the actual state of the project. The repo's top-of-tree work is a major-version rewrite of the CLI that moves it from a non-functional 1.x stub to a real working binary, plus a Node-LTS bump, plus a hosting-independent release workflow with provenance, plus an `install.sh` that solves the "I'm not a JS user" problem. None of this is in the prior dossier, because the prior dossier predates the `feat/cli-improvements` branch landing. The work is staged but not yet shipped, which means the project's distribution posture should anticipate the ship rather than describe the 1.2.1 status quo.

The other surprise was how clean the release machinery already is. The `CLAUDE.md` describes a hosting-independent layered release flow (Changesets at the data layer, npm at the publish layer, GitHub Actions only at the trigger layer) that is materially better-engineered than the publish flows in `clwnd` or `ccft` today. The note in the prior dossier ("the gold-standard template the rest of the TS portfolio should adopt") is correct, and the template is more substantive than the prior dossier credited. The direct-commit release flow with `[skip ci]` self-bump and `gh release create --notes-file` is the right shape and worth copying verbatim.

The third surprise was the genealogy. `ohlc-resample` is the successor to `candlestick-convert`, which is still actively maintained on its own track by `valamidev`. The README acknowledges this in the contributors block ("Past authors of `candlestick-convert`"). The two projects are not in conflict; `candlestick-convert` is on its 7.x line with breaking changes from 6.x, `ohlc-resample` ships shape-preserving overloads and a CLI. Awesome-list submissions should be honest about the two coexisting and should not present `ohlc-resample` as a strict replacement. This is a correctness call that affects the wording of every awesome-list PR going forward.
