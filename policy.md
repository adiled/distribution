# Distribution Policy

The constraints under which every launch operates. Updated quarterly.

---

## 1. Project tiering

Every project lives in exactly one tier. The tier dictates the channel mix, the voice, the cadence, and the metrics. Tier is reassessed on every milestone.

| Tier | Definition | Examples (today) | Distribution mode |
|---|---|---|---|
| **T1, Anchor** | Mature, used by strangers, demonstrates credibility for the rest of the portfolio. | `ohlc-resample` | Maintain quietly. Light incremental promotion. The asset is its track record. |
| **T2, Live** | Working, installable today, audience exists, ready for launch beats. | `clwnd`, `opencode-dir`, `ccft` | Concentrated launch effort. Multi-channel coordinated push. Track install to retain. |
| **T3, Ambitious / pre-launch** | Working code, but the *positioning* is not yet sharp enough for a wide audience. Needs a thesis post and a real demo before any launch beat. | `bhai-lang`, `iksir`, `orch`/`orchd` | Audience seeding (private shares, niche lists), not broad launches. Promote to T2 only after the positioning artifact exists. |
| **T4, Companion** | Supports a T1/T2/T3 project. Not promoted on its own. Mentioned only in the parent project's material. | `clwnd-playground`, `awesome-opencode` | No standalone push. Linked from parent. |
| **T5, Cultural / dormant** | Not part of the agentic-systems thesis. Ship if and when complete; otherwise leave alone. | `khowarpedia`, `adils.me`, `viteer`, `web3-elements` | No active distribution. README quality only. |

**Promotion rule.** A project moves T3 to T2 only when **all four** are true:
1. A one-paragraph pitch exists that someone outside the project would forward.
2. A 30-second demo exists (asciinema / vhs / video) showing the *result*, not the install.
3. Install works clean on a machine that has never seen the project.
4. There is a clear *first user* the project is for. "Anyone interested in agents" is not a first user.

**Demotion is not a failure.** A T2 project that didn't land moves back to T3, the lessons go in its dossier, and the next pass uses them. We do not relaunch the same way.

---

## 2. Naming, branding, identity

- **One name per project.** No internal codenames leaking into READMEs. No "formerly known as." Pick the name once.
- **Disambiguate aggressively against existing projects.** Names that collide with established projects (e.g. `bhai-lang` collides with the popular Hindi-keyword project of the same name) need either a rename or a clear positioning subtitle on every public surface so search results don't merge them.
- **Keep cultural / linguistic identity where it's load-bearing.** `بھائی-lang`, `iksir`, `khowarpedia`: these are not decorative. The vocabulary *is* the thing. Distribution copy in English explains the system. It does not flatten the cultural register out of the project itself.
- **Author identity is the handle.** Public-facing identity across every project, every registry, every social surface is **`adiled`**, not the legal name. Existing READMEs that currently print the legal name (e.g. `ohlc-resample`'s contributors block) are candidates for revision. Track those in `ASKS.md`.
- **Single canonical contact.** GitHub `@adiled` plus `hello@adils.me` (or whichever email is named on `adils.me` at the time). Same line on every project's contributors block, same handle on every social surface that allows it.

---

## 3. Voice

Distribution copy has three settings. Use exactly one per surface.

| Setting | When | Example |
|---|---|---|
| **Plain technical** | npm/PyPI/crates description, GitHub topic line, awesome-list entry | "Resample OHLCV/tick data to different time frames. CCXT-compatible. CLI + library." |
| **Demonstrative** | Landing page hero, README opening | "10 lines of code, one minute of data, every timeframe you wanted." |
| **Project's native register** | Inside the project itself, especially for `iksir`, `bhai-lang` | The README in the project's own vocabulary. |

**Cross-mixing is forbidden.** A README that opens in the project's mythic register and pivots to a marketing tagline reads as inauthentic. Pick one and hold it.

**Hard nos:**
- No "AI-powered" in copy unless the AI is the *product*. Hand-wave is detected.
- No "blazingly fast." If it's fast, show numbers.
- No "we" when "I" is true. Single-maintainer projects say "I built X."
- No emojis unless the project's existing aesthetic uses them. (`ohlc-resample` uses 🕯️; `iksir`'s alchemical register doesn't. Honor each.)

---

## 4. License defaults

| Project type | Default | Notes |
|---|---|---|
| Library to be embedded (npm/PyPI/crates) | **MIT** | Maximum reach. No friction at the import line. |
| End-user CLI / agent | **MIT** or **Apache-2.0** | Apache when patent-grant clarity matters (anything model-related, anything cryptographic). |
| Substrate / framework where wholesale embedding into surveillance platforms is undesirable | **AGPL-3.0** or a written custom license | Trade some adoption for non-cooption. Conscious choice, not default. |
| Data lineage / cryptographic projects (`bhai-lang` rishta runtime) | **Apache-2.0** | Patent grant matters here. |

A project shipping without a license is publishing nothing. `LICENSE` file before any launch beat.

---

## 5. Release cadence rules

- **Don't launch on Fridays.** Weekend traffic is dead and questions stack up unanswered. Tuesday to Thursday morning (US Pacific) for HN and Twitter, midweek for newsletters.
- **One launch per project per quarter, max.** Re-launches without new substance burn audience.
- **Re-launch needs new substance.** A version bump alone does not justify a second post. A new capability, a benchmark, an integration, a public user. Those do.
- **Don't fire two T2 launches in the same week.** They steal each other's attention.
- **Always pre-stage.** Version cut, changelog written, demo recorded, README polished, screenshots updated *before* posting anywhere. A broken demo on launch day is a quarter-loss.

---

## 6. Coordinated launch checklist (T2)

The following must all be green for a T2 project before posting publicly:

- [ ] Repo `description` field set on GitHub (not just in README)
- [ ] Repo topics/tags set (5 to 8, mix of broad and niche)
- [ ] README opens with a 1-line pitch and a demo (gif/svg/asciinema) above the fold
- [ ] Install works on a machine that has never seen the project (verify in fresh container/VM)
- [ ] Latest tagged release on GitHub (semver), with release notes a non-author can read
- [ ] Package published to its language registry (npm / PyPI / crates) with provenance attestation
- [ ] License file present and named in the registry metadata
- [ ] Contact path: at least one of email / X DM / GitHub issues, named in README
- [ ] Issue templates so first-contact users don't have to invent the bug-report shape
- [ ] First-issue label exists for at least one issue ("good first issue" or equivalent)
- [ ] An entry in the relevant awesome-list is at least *drafted* (PR not yet sent, coordinate with launch)

---

## 7. Anti-patterns (don't do)

- **Cross-promote a not-yet-shipped project from a shipped one.** Every README link is a promise. Don't link to vapor.
- **Self-star or buy stars.** Detectable, embarrassing, irreversible.
- **Post to /r/programming.** Hostile to self-promotion. The hit-rate on self-posts is near zero. There are better subreddits for niche audiences.
- **Splash-launch a project that needs handholding.** If a real first user needs a 30-minute call to use it, broad launches go negative. Tier T3 first, do private demos.
- **Launch and disappear.** First 48 hours decide a launch. Be present, answer comments, fix the install bug someone finds.

---

## 8. The thesis

The portfolio has a center of gravity: **agentic systems and the substrates they need.** `clwnd`, `opencode-dir`, `ccft`, `iksir`, `orch`/`orchd` all sit on this axis. `bhai-lang`'s rishta runtime is adjacent (it is *what agent lineage should look like* in a language).

`ohlc-resample` is the credibility anchor. It is not part of the thesis. It proves the maintainer ships things people use.

Distribution should reinforce the thesis. When a launch is in doubt, ask: does this advance the agentic-systems story for an outsider reading the GitHub profile? If no, defer.
