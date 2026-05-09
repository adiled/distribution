# orch / orchd

**Tier:** T3 — Ambitious / pre-launch • **Repos:** [`orch`](https://github.com/adiled/orch) (parser/spec) + [`orchd`](https://github.com/adiled/orchd) (execution engine) • **License:** *not declared in README* (verify) • **Stars:** 0 + 0

## What they are

A two-repo Rust toolchain for **declarative, platform-agnostic service orchestration**:

- **`orch`** — parser + spec for *Orchfiles*. Reads an Orchfile, validates constraints, expands variables, detects dependency cycles, emits structured JSON. Multi-file composition with systemd-drop-in semantics (last-wins scalars, key-merge lists, append+dedup positional, CLEAR directive). ARG overrides via CLI flag or env var.

- **`orchd`** — execution engine. Consumes orch's parsed JSON and produces native service-manager artifacts. Runtime layer (`bare` shipping; `containerd`, `podman`, `apple` planned) + Platform layer (`systemd` shipping; `launchd` planned). `orchd up`, `down`, `restart`, `status`, `logs --follow`, `health --timeout`, `list`, `clean`. Configurable via `.orchrc` (project or `$HOME`) or env or CLI flags with documented merge order.

Pipeline: **Orchfile → orch parse (JSON) → orchd Runtime (ExecSet) → Platform (native artifacts).**

## Why they matter to the portfolio

orch/orchd is the **most cleanly-scoped, classically-shaped technical project** in the portfolio. It is also the project most legible to mainstream OSS audiences — it competes in a familiar landscape (docker-compose, systemd-by-hand, nomad, pm2 for services), and its pitch is comprehensible to anyone who has ever written a `docker-compose.yml` and wished it did less or did more.

Distribution-wise, it is also the project most likely to land in **Rust + self-hosting communities** as opposed to the agent-systems audiences the rest of the portfolio targets. That diversification is itself valuable.

## Audience

- **Self-hosters** running services on a personal server / homelab who find `docker-compose` over-engineered and `systemd` units verbose.
- **Rust users** looking for tasteful tools written in Rust.
- **Developers who want service orchestration without containers** — orch's `bare` runtime + systemd platform is the killer combo here.
- Adjacent: macOS users wanting equivalent service-management on launchd (when the launchd platform ships).

## Distribution state — what's done

- ✅ Spec (`SPEC.md` referenced) and engine are in two cleanly separated repos.
- ✅ Composition semantics (drop-in overlay model) are well-explained with merge rules.
- ✅ ARG override priority is documented and unambiguous.
- ✅ `orchd` config-loading order documented (CLI > env > `.orchrc` > defaults).
- ✅ Both READMEs scan well — clear, technical, no unnecessary prose.

## Distribution state — gaps

- ❌ **License not declared in either README.** Mandatory before launch. Recommended: **Apache-2.0** (system-orchestration tooling benefits from explicit patent grants for downstream embedders).
- ❌ Not on crates.io. Hard requirement for a Rust tool's credibility — `cargo install orch` and `cargo install orchd` are the expected install paths.
- ❌ No comparison framing in the README. The audience needs *immediate* answers to "how is this different from docker-compose? from raw systemd? from nomad? from pm2?" A short comparison table near the top of `orchd`'s README would be high-leverage.
- ❌ No example Orchfile in either README beyond a partial fragment. A full, runnable example showing a 2–3 service stack (postgres + redis + app) end-to-end would let readers internalize the spec quickly.
- ❌ launchd platform not yet shipping. Documented as planned; until launchd lands, the project is Linux-only in practice. Be explicit about this in launch copy.
- ❌ No demo content (asciinema of `orchd up && orchd status && orchd logs`).
- ❌ Not on `awesome-rust`, `awesome-self-hosted`, or `alternativeto.net`.
- ❌ No `adils.me` essay framing the project against existing alternatives.
- ❌ The two-repo split is a discoverability cost. A visitor finds `orchd` first, then has to leave to understand `orch`. Consider whether to (a) link them very visibly in both READMEs or (b) merge into a workspace later.

## Plan — pre-launch

**Phase 1 — Polish (weeks 1–2):**

1. **License both repos** (Apache-2.0). Add `LICENSE`. Mention in READMEs.
2. **Add a comparison block** to `orchd`'s README, near the top: `orch/orchd` vs `docker-compose` vs raw `systemd` vs `nomad`. Be honest about trade-offs.
3. **Full runnable example** in `orchd` README: a 2–3 service stack (postgres + a small app + a healthcheck), with the full Orchfile, the generated systemd unit (snippet), and the `orchd up` → `orchd health` flow.
4. **Cross-linking:** `orch`'s README links to `orchd` as "execution engine"; `orchd`'s README links to `orch` as "parser/spec." (Already partially done — verify and reinforce.)

**Phase 2 — Publish (week 3):**

5. **`cargo publish`** both `orch` and `orchd` to crates.io. Pin version `0.1.0`.
6. **Tagged GitHub Releases** with handwritten release notes for both repos.
7. **Homebrew tap entry** in `adiled/tap` (currently Linux-only, but the brew formula can declare that).

**Phase 3 — Distill (weeks 4–6):**

8. **`adils.me` essay:** *"Orch — declarative service orchestration that targets your platform, not Docker."* The essay positions the project against the alternatives, shows the postgres+app example end-to-end, and explains the Runtime/Platform split as a deliberate choice (not a TODO).
9. **Demo recording.** ~60s asciinema: write Orchfile → `orchd generate` → `orchd up` → `orchd status` → `orchd logs --follow`.

**Phase 4 — Launch (week 7+):**

10. **Submit to `awesome-rust`** under tools / system / orchestration.
11. **Submit to `awesome-self-hosted`** under "monitoring" or "service managers."
12. **Submit to `alternativeto.net`** as alternative to docker-compose.
13. **Show HN candidate** with the `adils.me` essay as the URL — but only if the comparison framing is genuinely sharp. orch/orchd's argument needs to be defensible against the inevitable "why not just use X" questions; if the essay isn't airtight, defer HN.
14. **`/r/selfhosted`** post.
15. **`/r/rust`** post.

**Phase 5 — Promote to T2:**

When (a) both crates published, (b) essay published, (c) demo recorded, (d) at least one launchd platform commit landing — the project graduates to T2 (which at this point will be largely retrospective).

## What we are *not* doing

- **Not splitting attention between orch and orchd in launch copy.** The user-facing story is `orchd` ("the thing you run"). `orch` is the spec library — mentioned, linked, but not the front-of-pitch.
- **Not pretending launchd already works.** Linux-only is honest; macOS coming is a roadmap statement.
- **Not adding more runtimes (containerd, podman, apple) before the bare runtime is well-tested.** Premature breadth dilutes the argument that bare-runtime + systemd is a deliberate position.

## Metrics

T3 readiness gates. Project-specific signal: **inbound from self-hosted communities.** /r/selfhosted feedback is the highest-fidelity indicator of whether the docker-compose-alternative framing is landing — that subreddit is brutally honest about tools they don't see the point of.

## Launches

*(none yet — blocked on license + crates publish + comparison framing)*

## Notes

orch/orchd is the project where **clear technical framing beats novelty.** The Runtime/Platform split is the deliberate technical contribution; the cleanest way to communicate it is *one good example program that does something familiar* (run a postgres + app stack) and *one direct comparison block* explaining what's different. Resist the temptation to oversell — the audience for service orchestration is technical and skeptical, and oversold copy reads as red flag.
