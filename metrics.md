# Success Metrics

What we count, what counts as winning, what we deliberately don't count.

---

## North-star metric

**Strangers depending on `adiled`'s code in their own work.**

Stars are a popularity signal, not a usage signal. The thing we care about is whether the code is *load-bearing* in someone else's project. A dependency in their `package.json`, a binary in their `~/.local/bin`, a service in their stack, a citation in their post.

The proxy metrics for that, ranked by trustworthiness:

1. **Recurring weekly downloads** of published packages (npm / PyPI / crates). Strangers who installed a thing twice.
2. **External issues and PRs** opened by people who aren't the maintainer. Strangers who hit the code hard enough to engage.
3. **Inbound dep references** (`npm dependents`, GitHub "Used by", reverse-citations). Projects that took the dependency.
4. **Inbound mentions in third-party writing.** Newsletters, blog posts, talks. Counted manually. One good mention beats ten Twitter dunks.
5. **GitHub stars.** Directional, not load-bearing. A spike pattern matters more than the count.

Stars in isolation prove nothing. Stars plus downloads plus issues, *together*, are the real signal.

---

## Tier-by-tier targets

Targets are 90-day forward-looking. Re-baseline quarterly.

### T1: Anchor (`ohlc-resample`)

The job is *don't lose it*. This project's value to the portfolio is the credibility of its track record.

| Metric | Target | Source |
|---|---|---|
| Weekly npm downloads | hold or grow vs trailing 12-week median | `npmjs.com/package/ohlc-resample` |
| Issue response time | < 7 days for first response | manual review |
| Open critical bugs | 0 outstanding > 30 days | GitHub issues |
| Latest published version drift from main | < 2 weeks | GitHub releases vs main HEAD |
| `Used by` count | grow over time | GitHub dependents |

**Failure mode to watch.** The package sliding into "looks abandoned": last release date older than 6 months, unanswered issues. This is the worst tier-degradation. A T1 going quiet teaches everyone visiting the profile that things go quiet.

### T2: Live (`clwnd`, `opencode-dir`, `ccft`)

The job is *prove the thesis*. Each project's launch needs a spike that converts to a tail.

**Per-launch (first 30 days post-launch beat):**

| Metric | Floor | Target | Stretch |
|---|---|---|---|
| GitHub stars added | 25 | 100 | 500+ |
| Unique installers (downloads / curl-pipe-bash hits) | 50 | 250 | 1k+ |
| External issues opened | 1 | 5 | 15+ |
| Distinct contributors (any: issue, PR, comment) | 1 | 3 | 10+ |
| Aggregator inclusions (awesome-list / newsletter / podcast mention) | 0 | 2 | 5+ |
| Mentions on X / Bluesky / blog (not by author) | 0 | 5 | 20+ |

**Per-project sustaining (90-day, post-launch):**

| Metric | Target |
|---|---|
| Weekly active install rate (downloads / curl hits) | non-zero, with recurring weekly cadence |
| Median issue first-response | < 48h while project is in T2 |
| Doc/README freshness | accurate within 1 commit of latest release |

A T2 project that hits the floor on every launch metric for 90 days demotes back to T3. The audience didn't form, the positioning needs work.

### T3: Ambitious / pre-launch (`rishta-lang`, `iksir`, `orch`/`orchd`)

The job is *get launchable*. Metrics here are about readiness, not reach.

**Per-project readiness (binary checklist, not numeric):**

- [ ] One-paragraph pitch that a non-author would forward
- [ ] 30-second demo recorded
- [ ] Install verified clean on a never-seen-it machine
- [ ] First-user persona named (and ideally one real example acquired through private demo)
- [ ] Coordinated launch checklist from `policy.md` §6 fully green

When all five are checked, the project promotes to T2 and the T2 metrics apply.

**Optional engagement metrics for T3 (nice-to-have, not gating):**
- Number of private demos run
- Number of named individuals who've installed and given feedback
- External writing referencing the project (essays, talks, citations)

### T4: Companion (`clwnd-playground`, `awesome-opencode`)

No standalone metrics. Health is measured as the parent project's health.

### T5: Cultural / dormant

Not measured. The work happens because it should, not because numbers ask for it.

---

## Portfolio metrics (rolled up across all tiers)

These are the metrics for the **distribution chief** role, not for any individual project. Read them when a portfolio-level question comes up (a tier reassessment, a launch retro, a quarterly check-in if and when one happens).

| Metric | What it tells us | Acceptable range |
|---|---|---|
| **Active OSS projects** (T1, T2, active T3) | Portfolio breadth | 5 to 10. Below 5: thin portfolio. Above 10: focus problem. |
| **Total weekly download events across all packages** | Portfolio reach | Trend matters more than absolute. Quarter-over-quarter growth is the goal. |
| **Sum of new external contributors across portfolio** | Audience-formation pace | Higher is healthier. A quarter with zero is a flag. |
| **Launches executed per quarter** | Cadence health | 1 to 3 per quarter. Zero means not shipping. Above 3 means scattered. |
| **Distance from policy on each launch** (post-launch self-audit) | Policy coherence | Audited per launch in `projects/<name>.md`. Drift gets fixed in policy.md. |
| **Open distribution asks** (across the asks.json registry, by priority) | Bottleneck health | P0 count should trend toward zero between launches. |

---

## What we explicitly don't measure

- **Vanity stars from drive-by traffic.** A star spike from a Show HN front-page moment is celebrated as a launch event. It does not change the trustworthy-metrics view of the project.
- **X/Twitter follower count.** Not a distribution metric. Follower-count optimization corrupts voice (see `policy.md` §3).
- **Hours spent.** Effort is not value. We measure outcomes, not motion.
- **Email list size for `adils.me`.** Useful long-term, but the portfolio's distribution doesn't yet hinge on it. Re-evaluate when there's a launch where email is the primary channel.
- **Per-tweet engagement.** Posts are means. The only relevant metric is whether they produced installers.

---

## Honest framing

The portfolio is in a specific phase: **`ohlc-resample` is the lone proven asset, everything else is in formation.** The metrics framework above is calibrated to that reality. It is forgiving for new T2/T3 projects and demanding for T1, because the failure modes are different.

When the portfolio matures (multiple T1 projects, recurring T2 launches landing every quarter), this document gets rewritten. The current shape is a 6 to 12 month operating contract, not the permanent regime.
