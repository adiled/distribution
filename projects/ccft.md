# ccft

**Tier:** T2 (Live) • **Repo:** https://github.com/adiled/ccft • **License:** MIT • **Stars:** 0 • **Latest release:** v1.5.0 (2026-05-09)

## What it is

ccft is a Claude Code companion that watches your `/v1/messages` traffic and tells you, in cinematic terminal chrome, whether the bot or the driver (you) is rotting. The maintainer's own one-liner is "an agentic self improvement tool" and that is the right frame. Mechanically it is a small Rust daemon that sits at `127.0.0.1:7178`, decrypts only `api.anthropic.com` (every other host passes through untouched), mutates the request system prompt to inject a personal preamble or trim Claude Code's three large bloat blocks, and writes a per-response JSONL ledger. The TUI then reads that ledger and renders two scores from 0 to 100, `bot` and `driver`, against time, with a diagnosis line underneath that says things like "co-rotting, driver and bot are in a feedback loop" or "bot is cooked, swap models or clear context."

The scores are not token counts. They come from a four-signal model on each side. Driver-side combines prompt bloat, rapid-fire cadence, burst irregularity, and session sprawl. Bot-side combines latency-vs-input slope, p99/p50 spike ratio, output collapse, and an output-to-input hedge ratio. The math runs over the ledger only. Nothing in the request body is inspected for content, only behaviour.

The artifact does not call itself a proxy. The maintainer explicitly removed that word from every self-description on 2026-05-09 and standardized on "flytrap" for the daemon and "agentic self improvement tool" for the project. The name `ccft` was originally Claude Code Fly Trap. The maintainer no longer expands it. Treat the four letters as the name.

## Why it matters to the portfolio

ccft is the portfolio's first artifact that takes the agentic-systems thesis past observation and into self-confrontation. `clwnd` governs a fleet of agents. `opencode-dir` makes a workflow tractable. `ccft` asks the operator a harder question: are you driving or is the loop carrying you. The answer arrives as a vibe label and a sparkline, not a dashboard widget, because the register is meant to land on someone who knows what "fried" means and does not need a quarterly report.

The TUI is also where the portfolio's aesthetic conviction is loudest. The center column is intentionally biased 4% left of screen mid for a "broadcast/surveillance" read rather than enterprise grid. The substrate is `#02050E` with per-cell phosphor noise. Border hue cycles cyan to magenta with a dead tier. None of this is decoration. It carries the project's claim that watching yourself agent should feel like watching yourself, not auditing yourself.

## Audience

The audience is narrow and specific. Three kinds of people will pick this up and feel it fits them.

The first is the heavy Claude Code user who has already noticed they are paying for tool loops they did not need, who recognizes their own "fried" days, and who would rather see a vibe label than an OpenTelemetry dashboard. They will install ccft because the diagnosis line names something they already feel.

The second is the developer who wants to inject a personal system prompt into every Claude Code session without forking the client, who finds the existing CLAUDE.md / global-prompt mechanisms either too coarse or insufficient, and who treats prompt design as an iterative practice. The `system_override` knob and the `pain` knob (which trims Claude Code's three boilerplate blocks from the system array) are for this person.

The third is the terminal-aesthetic crowd who installed `bottom`, `gitui`, `atuin`, `bat` because the TUI was good, not because the underlying utility was novel. ccft's TUI sits in that lineage. The screenshot ships before the pitch does.

This audience does not overlap cleanly with the token-cost-observability cohort that codeburn, claude-usage-monitor, token-dashboard, and tokscale serve. Those tools answer "where are my tokens going". ccft answers "how is the session going". The mutation arm and the vibe scoring are why a token dashboard is not a substitute.

## Distinguishing claim

Two claims, both defensible from the code.

The first claim is the V·L·P·V signal model in `src/brainrot/aggregate.rs`. Eight scalar signals, no content inspection, two scores per time bucket, a diagnosis function that names eight specific failure modes including "driver-heavy, lots of typing, agent doing little tool work" and "bot-heavy, agent grinding through tool loops". No comparable Claude-Code-adjacent tool computes a behavioural score from inter-arrival gaps and latency derivatives. Codeburn classifies tool-call categories. ccft classifies the dynamic between the two parties.

The second claim is that ccft mutates running Claude Code requests where every other tool in the neighbourhood reads logs after the fact. The handler in `src/handler.rs` decodes the request body, walks the `system` array, appends an override block when one is configured, and replaces three specific blocks of Claude Code's stock system prompt with shorter equivalents when `pain=false`. That mutation arm is structural to the project, not a bonus feature.

## Distribution state, current

The repository is now release-ready on every dimension the existing dossier flagged in the prior pass.

License is MIT, declared in `Cargo.toml` and present as a `LICENSE` file. Issue #3 ("Add LICENSE") closed 2026-05-09. README was cut from encyclopedia length to 56 lines on 2026-05-09 (commit `2d4ebea`, closing issue #1), with reference content moved to `docs/install.md`, `docs/reference.md`, and `docs/architecture.md`. Tagged release v1.5.0 ships a prebuilt universal Mac binary attached as a release asset, built by `.github/workflows/release.yml` (issue #2 closed). The README hero image is a TUI screenshot. The repo description on GitHub matches the project's own line: "ccft - an agentic self improvement tool". Topics on the repo: `ai-alignment`, `brainrot`, `claude-code`, `self-improvement`, `system-prompt`.

The TUI runs as the default `ccft` invocation at a tty. Brainrot panel renders bot and driver as two-line braille sparklines (pink and cyan) with range chips above and proportional x-axis labels below. Diagnosis panel sits in the center column lower half, deliberately clustered upper-left with the rest as negative space. Range presets `today`, `yesterday`, `24h`, `7d`, `this-week`, `all` reachable via single keypress (`t y h w W a`).

The flytrap is host-gated to `api.anthropic.com` only via `should_intercept` in `src/handler.rs`. Every other CONNECT, including `gh`, `git`, `npm`, `pip`, `pypi`, passes straight through without being decrypted, so subprocesses spawned from a Claude session that do not trust ccft's CA never fail TLS. This is a safety property the existing dossier did not name.

Personal validation: the maintainer's own environment routes Claude through ccft (`HTTPS_PROXY=http://127.0.0.1:7178`, CA at `~/.cc-flytrap/ca.pem`). Dogfooded in production for the maintainer for at least seven days as of this dossier.

## Distribution state, gaps

Not on crates.io. The Rust audience expects `cargo install ccft` as a credible install path. `cargo publish` is a one-time setup task. Note: the package name `ccft` may already be reserved on crates.io. Verify availability before scheduling the publish step.

Not on Homebrew. macOS is the only supported platform (launchd-managed daemon). Homebrew is the natural channel for that audience. A formula entry in `adiled/tap` (the tap created for `clwnd`) takes about an hour.

Not on `awesome-claude-code` (the canonical hesreallyhim list at 36.8k stars), `awesome-claude-plugins`, or `awesome-rust`. These are slow-burn discovery channels. The PRs are short and do not need launch-day coordination.

No `adils.me` thesis post yet. The post is the launch's primary artifact under the policy in `policy.md` §6. Show HN and X both link to the post, not to the repo. The post is missing.

Linux not supported. The daemon is `launchd`-managed. Document the macOS-only constraint in the README front matter (it is currently in a callout block under the screenshot, which is fine) and have a one-paragraph "Linux roadmap" answer ready for the launch threads where this question will arrive within the first hour.

The token-dashboard category collision is the launch risk this dossier flags loudest. Codeburn, Claude-Code-Usage-Monitor, claude-usage, token-dashboard, ccusage, ccflare, and tokscale all live in the audience's nearby attention. A first-time visitor scrolling the README must understand within ten seconds that ccft is in a different lane (request mutation plus behavioural scoring, not log-reading cost analytics). The current README opening leans on the streaming-preserved property and the three design properties. It does not yet say "this is not a token tracker" out loud. Saying it once, near the top, would inoculate the launch.

## Distribution state, aspirational

A short loop video of the TUI showing the bot and driver lines diverging across a real day would do more for the launch than any prose. asciinema or vhs both work. This does not exist yet.

A second screenshot of the diagnosis line catching a co-rotting moment ("co-rotting, driver and bot are in a feedback loop") would seal the pitch for the audience that will not click through. The visual is the argument.

A `ccft brainrot score` integration with statusline tools (`tmux`, `starship`, `ccstatusline`) would give the project a reason to live in the audience's terminal even when the TUI is closed. The CLI subcommand already prints a one-liner suitable for status bars. A pull request to `ccstatusline` adding ccft as a source would carry the project into a much wider Claude Code audience without ccft itself needing to grow.

## Plan

Pre-launch, in order:

1. Verify `ccft` availability on crates.io. If taken, decide between a renamed publish (`ccft-cli`, `claude-flytrap`) or skipping the registry entirely and leaning on the prebuilt binary plus Homebrew. Document the decision in `notes` below.
2. Publish to crates.io if the name is available. Stable name reservation alone is worth the run.
3. Write the Homebrew formula in `adiled/tap` pointing at the universal Mac binary on the v1.5.0 release. Test `brew install adiled/tap/ccft` from a fresh shell.
4. Write the `adils.me` thesis post. Working title direction: "ccft: how is your Claude Code session going". Open with the TUI screenshot. Show one full ledger record. Show a co-rotting diagnosis caught in the wild. Close with the install one-liner. Do not include the legal-name byline; this is informal register per `policy.md` §2.
5. Cut a v1.5.1 patch only if the README needs a "what this is not" line near the top to inoculate against token-dashboard category collision. Not strictly required.

Launch beats, in order, on a Tuesday or Wednesday morning US Pacific:

1. Publish the `adils.me` post.
2. Show HN with the post URL. Title direction: "ccft: a flytrap for your Claude Code session". Not a "Show HN: I made a tool" framing. The post is the artifact.
3. PR to `hesreallyhim/awesome-claude-code` under the relevant category. The list is curated; the PR needs a one-line entry that names what ccft is in 30 words or fewer.
4. PR to `ComposioHQ/awesome-claude-plugins` if ccft surfaces as a plugin or skill candidate. It currently does not, but the list also covers companion apps.
5. PR to `awesome-rust` under "command-line utilities" or "applications" subsection.
6. X thread of four to five posts: TUI screenshot lead, then a second screenshot of the diagnosis line, then a ledger record close-up, then the streaming-preserved property with a measurement, then the install one-liner. The TUI is doing the heavy lifting. The thread does not need to be longer than that.
7. Drop the post URL into `r/ClaudeAI` and `r/ClaudeCode` (4,200+ weekly contributors on the latter). Frame around the diagnosis line, not the proxy machinery.
8. Tip line submissions to TLDR AI and Latent Space the day after the Show HN beat, so the editorial reach lands while the post is still warm.

Post-launch, first 30 days:

1. Linux support is the most-requested feature that will arrive. Have a one-paragraph "macOS-only because launchd; Linux roadmap depends on systemd parity" answer pinned to the response. Do not commit to a date.
2. The MITM-flytrap framing will draw security questions. The repo's own answer is already in `docs/architecture.md`: self-signed CA at `~/.cc-flytrap/`, scope gated to `api.anthropic.com`, no upload, no telemetry, local-only. Have that paragraph copy-pasted in a saved buffer.
3. The TUI feature requests will land. Triage aggressively. A minimal opinionated TUI that does one thing well is the asset; one that absorbs every reasonable request becomes nothing.
4. Watch the `ccft brainrot score` adoption. If it lands in `ccstatusline` or `tmux` setups in the wild, that is the network effect. Encourage it. Document it.

## What we are not doing

Not pitching ccft as a token-cost dashboard. The category is crowded (codeburn, ccusage, claude-usage-monitor, token-dashboard, tokscale, ccflare, claude-doctor) and the audience overlap is small. ccft's claim is behavioural, not financial. Importing the cost-observability frame would land us in someone else's lane with a worse version of their product.

Not pitching ccft as an "agent observability platform" for organizations. ccft is a personal tool with a single binary, a single user, a launchd unit, and a self-signed CA on disk. Org or SaaS framing dilutes the punch and invites enterprise-plan questions the project has no answer to. Stay personal.

Not building a web UI. The TUI is the visual surface. A web UI is a different project with a different audience and a different aesthetic.

Not extending to non-Anthropic providers preemptively. The handler is hardcoded to `api.anthropic.com` and the system-prompt mutation is keyed to Claude Code's three specific bloat blocks. Adding OpenAI or Gemini support is a different project's worth of work and dilutes the single-provider focus that is part of the pitch.

Not flattening the cinematic register. The TUI's broadcast/surveillance composition, the cyberpunk color palette, the phosphor noise, the magenta caret section markers: these are the project's voice. Distribution copy explains them. It does not erase them.

## Metrics

Per `metrics.md` T2 per-launch floor / target / stretch.

| Metric | Floor | Target | Stretch |
|---|---|---|---|
| Stars added (30d) | 25 | 100 | 500 |
| Unique installers (30d) | 50 | 250 | 1k |
| External issues (30d) | 1 | 5 | 15 |

Project-specific signals to watch.

Diagnosis-line virality. The single most quotable artifact ccft produces is the diagnosis function output. If posts that screenshot a diagnosis line ("co-rotting", "driver is rotting; bot is keeping up") drive more inbound traffic than posts that screenshot the chart, the post should lead with diagnosis lines. Track inbound by referrer per post.

Statusline integration adoption. The `ccft brainrot score` one-liner is the cheapest hook into a Claude Code user's terminal once the TUI session ends. Mentions, forks, or pull requests that surface ccft inside `ccstatusline`, `tmux-statusline`, or `starship` configs are the leading indicator that the project is being lived in, not just installed.

Issue-to-star ratio. ccft is technical enough that any external issue is engagement, not noise. A project where stars race ahead of issue activity is being collected; one where issues track stars closely is being used.

## Launches

None executed yet. v1.5.0 is the first release-ready cut.

## Notes

The existing dossier was written under three imported frames that the code does not support. It called ccft a "streaming MITM proxy" (the maintainer removed "proxy" from every self-description on 2026-05-09). It pattern-matched the project to "agent observability" and "agent telemetry" market neighbourhoods (the actual neighbours, codeburn and ccusage, do something different and the ccft claim is behavioural rather than observational). It treated the TUI as a launch asset on top of a backend story (the TUI is the product; the daemon is the substrate). This dossier replaces those frames.

The brainrot module name is load-bearing. It is the GitHub topic, the CLI subcommand, the panel title, and the chart subtitle ("vibes over time"). Distribution copy in English explains what brainrot computes. It does not rename it.

The package name on crates.io needs a verification pass before the publish step is committed. If `ccft` is taken, the launch plan can survive without crates.io (`brew install adiled/tap/ccft` plus the prebuilt binary download path are sufficient install routes for a macOS-only tool). Renaming the published crate to `ccft-cli` or similar is a less interesting fallback than skipping crates.io altogether.

Naming hygiene: `cc-flytrap` appears on disk in the maintainer's own environment (`~/cc-flytrap` checkout, `~/.cc-flytrap/ca.pem`, the v0.0.0 commit message). Public artifacts have already been migrated to `ccft`. The CA path is the one user-visible leftover and is documented as such; renaming it would orphan existing installs. Leave it alone.

## Research log

Sources consulted, in order:

- Local clone at `~/cc-flytrap`. Cargo.toml manifest, full source tree, README, AGENTS.md, docs/architecture.md, docs/reference.md, docs/install.md.
- Source code: `src/main.rs`, `src/handler.rs`, `src/flytrap.rs`, `src/ledger.rs`, `src/brainrot/mod.rs`, `src/brainrot/aggregate.rs`, `src/brainrot/today.rs`, `src/brainrot/score.rs`, `src/tui/mod.rs`, `src/tui/panels/brainrot.rs`, `src/tui/panels/diagnosis.rs`.
- Git history: last 50 commits, with attention paid to commit `72db49f` ("Drop 'proxy' from ccft's self-description; standardize on 'flytrap'", 2026-05-09), commit `92c9a02` (initial v0.0.0 named "Claude Code system prompt stripper"), commit `2d4ebea` (README cut to under 80 lines, closed issue #1), commit `b8a02de` (release.yml workflow, closed issue #2).
- GitHub issues: #1 (README cut), #2 (prebuilt binary), #3 (LICENSE) all closed on 2026-05-09.
- Existing dossier at `projects/ccft.md`.
- `policy.md` (T2 launch checklist, license defaults, voice rules), `channels.md` (Claude Code adjacent channels), `metrics.md`, `projects/_index.md`, `projects/clwnd.md` (dossier shape reference).
- `gh repo view adiled/ccft` for description, license, topics, latest release.

Sources rejected as imported-category framings:

- "MITM proxy" market category. The maintainer removed "proxy" from every self-description on 2026-05-09. The artifact's own register is "flytrap" for the daemon and "agentic self improvement tool" for the project. Forcing ccft into the MITM-proxy lane (where mitmproxy, Caido, Burp Suite live) would land on a security-researcher audience that has no use for the bot/driver scoring.
- "Agent observability" or "agent telemetry" category. The OpenTelemetry-vibe-coding-dashboards lane (VictoriaMetrics, opencode-plugin-otel) is observational and integrates into existing monitoring stacks. ccft is single-user, behavioural, and does not export OTLP. Importing this frame would route the launch to platform-engineering audiences that are not the project's audience.
- "Token cost observability" category. Codeburn (`getagentseal/codeburn`), Claude-Code-Usage-Monitor (`Maciek-roboblog`), claude-usage (`phuryn`), token-dashboard (`nateherkai`), tokscale (`junhoyeo`), ccusage, ccflare, claude-doctor, ccstatusline. All read local Claude Code session JSONLs, none mutate requests, none compute a vibe score. The audience overlap exists but the lane is wrong; the README must inoculate against this collision in the first paragraph.
- "MCP gateway" category. ccft is not an MCP server, does not surface tools, and does not interoperate with the MCP registry. The category fit was never plausible from the code.

What surprised me in step 4, when writing the project's-own-voice description.

The maintainer's framing is more developed than the existing dossier credited. "Vibes over time" is the chart subtitle. "Co-rotting", "fried", "crisp", "cooked" are the labels. "Driver" and "bot" are first-class entities with separate four-signal models. "Agentic self improvement tool" is the Cargo.toml description and the CLI about-string. The project does not present itself as infrastructure; it presents itself as a feedback instrument on the operator's relationship with the agent. The cyberpunk aesthetic is intentional ("broadcast/surveillance rather than enterprise grid", "intentional pressure imbalance", "phosphor noise"). The previous dossier flattened all of that into "TUI dashboard" and "ledger" and "MITM proxy", which sat the project next to mitmproxy and Grafana when its actual neighbours are closer to a Strava activity feed for your coding sessions.

The other surprise was how cleanly the safety story already exists in the code. `should_intercept` is gated to `api.anthropic.com` only, by name. Every other CONNECT passes through without decryption. `gh`, `git`, `npm`, `pip` continue to work from any subprocess spawned by Claude. This is a real differentiator against the generic "MITM proxy" objection that will arrive in launch threads, and it is not yet surfaced in the README or in any prior dossier.
