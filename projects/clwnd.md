# clwnd

**Tier:** T2 — Live • **Repo:** https://github.com/adiled/clwnd • **License:** *not declared in README* (see Gaps) • **Stars:** 9

## What it is

A sentinel for multiplexed cross-harness AI bots. Manages concurrent Claude Code processes through opencode, with permission gating, idle-process killing, MCP proxying, optional drone-style observer for context-loss detection and auto-recovery. Daemon-shaped — `clwnd update`, `clwnd status`, `clwnd logs`, `clwnd sessions`. Has a working install script and an existing demo gif in the repo.

The pitch in one sentence: **govern your fleet of agentic coding sessions without babysitting each one.**

## Why it matters to the portfolio

This is the most launch-ready agentic project in the portfolio. It already has 9 stars without any coordinated push, runs as a daemon, has a real install path, and addresses a problem that opencode + Claude Code power users *experience daily*. It is the most direct test of the "agentic systems and the substrates they need" thesis.

If `clwnd` lands, it validates the thesis publicly and pulls attention to `opencode-dir`, `ccft`, and `iksir` by association.

## Audience

- opencode users running multiple concurrent Claude Code / opencode sessions.
- Heavy Claude Code users feeling the pain of unmonitored runaway processes.
- Adjacently: people building agent fleets who haven't built their own process supervisor yet.

This audience is **small, niche, and high-engagement** — exactly the shape that converts well on Show HN and `/r/LocalLLaMA`.

## Distribution state — what's done

- ✅ Working `curl | bash` install script.
- ✅ Demo gif checked into repo (`clwnd.gif`).
- ✅ README opens with a clear ASCII identifier and a one-line pitch.
- ✅ Config schema documented (table of keys + defaults).
- ✅ MCP integration documented (with honest disclosure of OAuth-MCP limitation).
- ✅ Cross-references to `opencode-dir` for the `cd`/`mv` flow.

## Distribution state — gaps

- ❌ **No license file.** Mandatory before any T2 launch (`policy.md` §4). MIT is the default for end-user CLI of this shape.
- ❌ Not on npm. The bash install pulls from `raw.githubusercontent.com`. Fine for early adopters; an npm package gives `npm install -g clwnd` as a second install path and dramatically improves discoverability.
- ❌ Not on Homebrew. A tap entry (`adiled/tap`) gives `brew install adiled/tap/clwnd` — the macOS audience for opencode is large; this matters.
- ❌ No landing page. `clwnd.dev` or similar would dominate search for the project name and outrank the GitHub repo for cold visitors.
- ❌ README assumes the reader already knows the problem. Add a "what problem does this solve" paragraph above the install snippet — first-time visitors who don't already feel the pain bounce.
- ❌ Issue templates and a `good first issue` queue do not exist (need to verify).
- ❌ Not on any awesome list.
- ❌ No release on GitHub Releases yet — distribution via `main` branch is fragile.

## Plan — coordinated launch (T2)

**Pre-launch (must all be green before launch beat):**

1. **License.** Add `LICENSE` (MIT). Add license badge to README.
2. **README front-matter.** Restructure: 1-line pitch → demo gif → "what this solves" paragraph → install snippet → quick example. The current order assumes domain familiarity; this order doesn't.
3. **Tagged release.** Cut `v0.1.0` (or whatever the appropriate semver is given current state) on GitHub Releases with handwritten release notes.
4. **npm publish.** Even if the bash installer remains primary, an npm package widens the funnel. Keep the bash installer as the recommended path; document `npm install -g clwnd` as the alternate.
5. **Homebrew tap.** `adiled/tap` repo with a `Formula/clwnd.rb`. ~1 hour of work.
6. **Issue templates.** Bug-report and feature-request templates so first-contact visitors don't have to invent the shape.
7. **Landing page (optional but strongly recommended).** Single-page Vercel deploy: hero, demo gif, install snippet, "who built this" link to `adils.me`. ~2 hours of work.
8. **`adils.me` thesis post.** Title direction: *"clwnd: governing fleets of coding agents without losing your mind."* The post is the primary artifact of the launch — Show HN/Reddit/X all link to it, not to the repo.

**Launch day (Tuesday or Wednesday morning, US Pacific):**

1. Publish the `adils.me` post.
2. Open Show HN with the post URL (not the repo URL — the post explains the why).
3. PR to `awesome-opencode` with an entry for `clwnd`.
4. PR to `awesome-claude-code` with an entry.
5. Post on `/r/LocalLLaMA` — long-form, leading with the problem.
6. X thread: 1 tweet with the demo gif as the lead, 2 tweets explaining the why, 1 tweet linking the post.
7. Be present in the threads for the first 24 hours. Answer every comment. Fix anything that breaks under cold-start traffic.

**Post-launch (first 30 days):**

- Run a second light beat ~2 weeks after if the first one underperforms — `Console.dev` newsletter submission, Latent Space tip line, podcast outreach.
- Convert any non-trivial bug reports into "good first issue" tickets where they fit; the community-formation flywheel runs on those.
- Add a "Who uses this" section to the README as soon as one or more external users surface.

## What we are *not* doing

- **No Product Hunt.** Audience mismatch — PH is end-user-shaped; `clwnd` is a developer tool with config-file rituals.
- **No paid promotion.** As per `policy.md` §3.
- **No premature roadmap publication.** The README links to a "compatibility issue" for current state; that's enough. A speculative roadmap is a debt.

## Metrics

Per `metrics.md` T2 "per-launch" floor / target / stretch.

| Metric | Floor | Target | Stretch |
|---|---|---|---|
| Stars added (30d) | 25 | 100 | 500 |
| Unique installers (30d) | 50 | 250 | 1k |
| External issues (30d) | 1 | 5 | 15 |

## Launches

*(none executed yet — pending pre-launch checklist)*

## Notes

`clwnd` and `opencode-dir` are *technically related* but should **not** be co-launched. Two niche-audience launches in the same week split the attention and dilute both. Stagger by at least 3 weeks; lead with `clwnd` (broader scope, more discussion-friendly), follow with `opencode-dir` riding any awareness `clwnd` produced.
