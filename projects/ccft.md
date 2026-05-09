# ccft

**Tier:** T2 — Live • **Repo:** https://github.com/adiled/ccft • **License:** *not declared in README* (verify) • **Stars:** 0

## What it is

A streaming MITM proxy that sits between Claude Code and `api.anthropic.com`. Mutates the request system prompt to user preferences, writes a per-response token ledger, preserves Claude's token-by-token streaming UX byte-for-byte. Single Rust binary (~6 MB on disk), self-installs to `~/.local/bin/ccft`, registers a launchd agent, generates its own CA. ~4 MB resident at idle.

The pitch: **see and shape what your Claude Code session is doing — without breaking the streaming.**

## Why it matters to the portfolio

ccft is a **high-craft, observability-shaped tool** that addresses a real pain — "what is my Claude Code session actually sending and consuming?" — with a notably tasteful technical approach (custom Body wrapper for SSE tap, single-artifact install, idempotent installer, dev/prod isolation built in).

It also has a **clear thesis hook**: in an era where everyone runs LLM agents but few can answer "what does mine cost / what is it sending," ccft is the answer. That makes it interesting to two distinct audiences: Claude Code power users (immediate) and the "agent observability" trend (broader).

## Audience

- Heavy Claude Code users wanting cost visibility per response.
- Developers who want to inject system-prompt preferences without forking the client.
- The "agent observability" / "agent telemetry" intellectual neighborhood — a small but engaged audience that overlaps with HN and Latent Space readership.

## Distribution state — what's done

- ✅ Excellent README — opens with three concrete design properties; that framing is itself a distribution asset.
- ✅ Working installer (`make install` + `ccft install` is genuinely idempotent).
- ✅ `ccft trust --apply` for `~/.claude.json` modification with backup discipline.
- ✅ Dev mode with isolated state (separate port, separate config, separate ledger).
- ✅ Real engineering choices documented (preserved streaming cadence, tiny resident footprint, single-artifact install).
- ✅ launchd integration (so the proxy survives reboots).
- ✅ Personal validation: the maintainer is using it (`HTTPS_PROXY=http://127.0.0.1:7178` + the cc-flytrap CA path are in this user's own env). Dogfooded.

## Distribution state — gaps

- ❌ **No license file in README.** Mandatory before launch (`policy.md` §4). Apache-2.0 is appropriate here (touches cryptographic concerns: self-signed CA generation).
- ❌ Not on crates.io. The Rust audience expects `cargo install ccft` as a credible install path.
- ❌ Not on Homebrew. macOS-only project (launchd!) — Homebrew is the natural channel.
- ❌ No demo content. The "see what your Claude Code session is doing" pitch is *visual* — a screenshot of the ledger output (`ledger.jsonl` rows) plus a one-frame "before/after token count" comparison would be the README hero.
- ❌ Linux not yet supported (launchd-only). Document this clearly in the README — managing user expectations early prevents bug reports.
- ❌ No tagged GitHub Releases. Pre-built binaries (universal Mac binary, signed) would lower the install bar dramatically.
- ❌ Not on any awesome list.
- ❌ Author has not yet written about it externally.

## Plan — coordinated launch (T2)

**Pre-launch (must all be green):**

1. **License.** Add `LICENSE` (Apache-2.0). Mention in README.
2. **README front-matter:**
   - Hero screenshot of ledger output above the install snippet.
   - One sentence above the existing three design properties: *"ccft tells you what Claude Code is doing — at the byte level."*
   - macOS-only callout (early, honestly framed).
3. **crates.io publish.** `cargo publish` — a stable name reservation alone is worth this.
4. **Homebrew tap entry** in `adiled/tap` (shared with `clwnd`).
5. **Tagged release** with prebuilt universal Mac binary attached (saves the `cargo install` time for non-Rust users).
6. **`adils.me` thesis post.** Title direction: *"What is your Claude Code session actually doing? A 4MB proxy with answers."* Show real ledger output. Show the streaming-preserved cadence claim with a measurement.

**Launch beats:**

1. Show HN with the `adils.me` post URL. The angle is the post, not the binary.
2. PR to `awesome-claude-code`.
3. PR to `awesome-rust` under "tools" or "system" subsection.
4. PR to `awesome-self-hosted` under "monitoring" or similar.
5. X thread: ledger screenshot → measurement evidence → repo link.
6. Latent Space tip-line — this is exactly the kind of "small tasteful infra" they cover.

**Post-launch:**

- Linux support is the #1 expected request. Have a one-paragraph "Linux roadmap" answer ready.
- The MITM-proxy framing will draw security questions. Have a clear, non-defensive answer about the self-signed CA, scope, and what it does *not* do.

## What we are *not* doing

- **Not pitching it as a "monitoring tool" for organizations.** ccft is a personal-developer tool. SaaS / org-tier framing dilutes the punch and invites "what's the enterprise plan" questions. Stay personal.
- **Not building a UI on top yet.** The ledger is the artifact. A web UI on top would be a different project.
- **Not extending to non-Anthropic providers preemptively.** Single-provider focus is part of the pitch.

## Metrics

T2 per-launch from `metrics.md` apply.

Project-specific: track the **ratio of stars to closed issues** as a quality signal — ccft is a deeply technical project where every issue is engagement, not noise. A project where stars race ahead of issue engagement tends to be vanity-driven; one where issues track stars closely is being used for real.

## Launches

*(none yet)*

## Notes

ccft is the **most "show, not tell" project** in the portfolio. The README is already strong on telling; the launch needs to lean on showing — measured cadence, real ledger output, the actual `~/.cc-flytrap/ca.pem` file living in a real install. Resist marketing copy and lean into engineering specifics; the audience this targets is allergic to the former and rewards the latter.
