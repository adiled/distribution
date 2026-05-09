# ccft

**Tier:** T2 (Live) • **Repo:** https://github.com/adiled/ccft • **License:** *not declared in README* (verify) • **Stars:** 0

## What it is

A streaming MITM proxy that sits between Claude Code and `api.anthropic.com`. Mutates the request system prompt to user preferences, writes a per-response token ledger, and preserves Claude's token-by-token streaming UX byte-for-byte. Single Rust binary (about 6 MB on disk), self-installs to `~/.local/bin/ccft`, registers a launchd agent, generates its own CA. About 4 MB resident at idle.

Now also ships a **full-screen TUI dashboard** (`ccft` at a tty, no subcommand) with time-dimension dials, range presets, overlays for split / sessions / perf / live, and a permanent header status block. The dashboard reads `ledger.jsonl` directly. There's a `pain` config knob that trims Claude Code's three large bloat blocks from the request payload.

The pitch: **see and shape what your Claude Code session is doing, without breaking the streaming.**

## Why it matters to the portfolio

ccft is a **high-craft, observability-shaped tool** that addresses a real pain ("what is my Claude Code session actually sending and consuming?") with a tasteful technical approach: custom Body wrapper for SSE tap, single-artifact install, idempotent installer, dev/prod isolation built in, h1.1 forced to sidestep the open h2 issues across the Go/Rust MITM ecosystem.

The TUI is the new center of gravity. A proxy is a backend story. A TUI is a *screenshot story*. The screenshot ships. The screenshot is the launch.

## Audience

- Heavy Claude Code users wanting cost visibility per response.
- Developers who want to inject system-prompt preferences without forking the client.
- The "agent observability" or "agent telemetry" intellectual neighborhood. A small but engaged audience that overlaps with HN and Latent Space readership.

## Distribution state: what's done

- ✅ Screenshot is now in the README, top of file. The single most important README addition for this project, done.
- ✅ Working installer (`make install` plus `ccft install` is genuinely idempotent).
- ✅ `ccft trust --apply` for `~/.claude.json` modification with backup discipline.
- ✅ Dev mode with isolated state (separate port, separate config, separate ledger).
- ✅ launchd integration. The proxy survives reboots.
- ✅ TUI dashboard with keyboard-driven time-range dials, overlays, permanent status chrome.
- ✅ Ledger schema published in the README (per-response JSONL with timing, token counts, cache stats, model, session id).
- ✅ Honest engineering footnotes: hudsucker as the substrate, h1.1 forced for ALPN reasons, Lua pilot history kept as a `lua/` directory and explained.
- ✅ Personal validation: the maintainer is using it (`HTTPS_PROXY=http://127.0.0.1:7178` and the cc-flytrap CA path are in this user's own env). Dogfooded.

## Distribution state: gaps

- ❌ **README bloat.** The README has grown to encyclopedia length: install, uninstall, lifecycle, dev mode, config, ledger schema, how-it-works diagram, TUI keyboard table, file layout, full subcommand reference, sources, lua/ history. A first-time visitor scrolling past the screenshot has to wade through 200+ lines of reference material before they decide whether to install. Most of this should move to `docs/` (or `ccft <cmd> --help`, or `man ccft`), with the README left as: screenshot, pitch, three properties, install one-liner, "what's inside" (TUI, ledger, config), "see docs/ for the rest." The reference content is good, it's just on the wrong surface.
- ❌ **No license file in README.** Mandatory before launch (`policy.md` §4). Recommendation: **MIT.** ccft is an end-user binary with no library exports. The crypto surface (self-signed CA, TLS) is entirely inherited from `rcgen` and `rustls`, not invented here. The Apache patent grant has nothing to attach to. MIT is shorter, clearer, and matches the convention for Rust end-user binaries (bat, exa, fd, gitui, atuin, bottom).
- ❌ Not on crates.io. The Rust audience expects `cargo install ccft` as a credible install path.
- ❌ Not on Homebrew. macOS-only project (launchd). Homebrew is the natural channel.
- ❌ Linux not yet supported (launchd-only). Document this clearly in the README front-matter, not buried. Managing user expectations early prevents bug reports.
- ❌ No tagged GitHub Releases. Pre-built universal Mac binary attached to a release saves the `cargo install` time for non-Rust users.
- ❌ Not on any awesome list.
- ❌ Author has not yet written about it externally.
- ❌ The TUI is a launch asset and the README mentions it once in passing. It deserves its own framing in the launch copy: "ccft ships a TUI. The proxy is the backend. The TUI is the product."

## Plan: coordinated launch (T2)

**Pre-launch (must all be green):**

1. **License.** Add `LICENSE` (MIT). Mention in README.
2. **README cut.** Move config table, ledger schema, file layout, full subcommand reference, sources, and lua/ history to `docs/`. Keep in README: hero screenshot, pitch, three design properties, install snippet, macOS-only callout, one-line note about each feature surface (TUI, ledger, dev mode), link to `docs/`. Target: under 80 lines.
3. **Linux callout** at the top of the README, near the screenshot. One sentence, honestly framed.
4. **crates.io publish.** `cargo publish`. Stable name reservation alone is worth it.
5. **Homebrew tap entry** in `adiled/tap` (shared with `clwnd`).
6. **Tagged release** with prebuilt universal Mac binary attached.
7. **`adils.me` thesis post.** Title direction: *"What is your Claude Code session actually doing? A 4MB proxy with answers."* Open with the TUI screenshot. Show one full ledger row. Show the streaming-preserved cadence claim with a measurement. Close with the install one-liner.

**Launch beats:**

1. Show HN with the `adils.me` post URL. The angle is the post, not the binary.
2. PR to `awesome-claude-code`.
3. PR to `awesome-rust` under "tools" or "system" subsection.
4. PR to `awesome-self-hosted` under "monitoring" or similar.
5. X thread: TUI screenshot first (it's the visual hook), then ledger row close-up, then measurement evidence, then repo link. The TUI is doing the heavy lifting.
6. Latent Space tip-line. This is exactly the kind of "small tasteful infra" they cover.

**Post-launch:**

- Linux support is the #1 expected request. Have a one-paragraph "Linux roadmap" answer ready.
- The MITM-proxy framing will draw security questions. Have a clear, non-defensive answer about the self-signed CA, scope, and what it does *not* do (no upload, no telemetry, local-only).
- TUI feature requests will land. Triage aggressively. A minimal, opinionated TUI that does one thing well beats a TUI that absorbs every reasonable feature.

## What we are *not* doing

- **Not pitching it as a "monitoring tool" for organizations.** ccft is a personal-developer tool. SaaS or org-tier framing dilutes the punch and invites "what's the enterprise plan" questions. Stay personal.
- **Not building a web UI on top.** The TUI is the visual surface. A web UI would be a different project.
- **Not extending to non-Anthropic providers preemptively.** Single-provider focus is part of the pitch.
- **Not bloating the README to "document everything in one file."** Reference docs belong in `docs/`. The README is the front door, not the manual.

## Metrics

T2 per-launch from `metrics.md` apply.

Project-specific signals to watch:
- **Ratio of stars to closed issues.** ccft is a deeply technical project where every issue is engagement, not noise. A project where stars race ahead of issue engagement tends to be vanity-driven. One where issues track stars closely is being used for real.
- **TUI screenshot virality.** Track inbound link traffic specifically from posts that embedded the TUI screenshot vs posts that didn't. If the screenshot drives most of the traffic (it will), invest in more visual material: short loop video of the TUI, second screenshot of the live overlay, asciinema of `ccft trust --apply` and first ledger row appearing.

## Launches

*(None yet.)*

## Notes

The README's current state is the classic problem of a project that grew faster than its surface. Every feature added a section. Each section is well-written. Together they overwhelm. The fix is editorial, not technical. Cut hard, link to `docs/`, trust the reader to follow the link when they need the detail.

The screenshot landing changes the launch math. Before the screenshot, this was a "small tasteful Rust binary" launch that needed to argue for its own attention. With the screenshot, it's a "look at this dashboard" launch where the visual does the arguing. Adjust the order of operations: the README cut and the screenshot framing are now upstream of crates.io publish in priority.
