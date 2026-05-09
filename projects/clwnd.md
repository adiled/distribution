# clwnd

**Tier:** T2 (Live) • **Repo:** https://github.com/adiled/clwnd • **License:** MIT • **Stars:** 9 • **Latest release:** v0.24.19 (2026-05-05)

## What it is

clwnd is an OpenCode sidecar that lets an OpenCode session use a locally-installed Claude Code CLI as its model provider, and substitutes OpenCode's standard filesystem and code-editing tools with an AST-grounded toolkit the daemon owns. It is a long-lived process (`launchd` on macOS, `systemd --user` on Linux) that the OpenCode plugin `@clwnd/opencode` talks to over a unix socket. The daemon spawns and pools Claude CLI processes (one per session, capped by `maxProcs`), grafts OpenCode's session history into Claude's JSONL when models switch, brokers tool calls, watches the stream for context loss, and writes a per-turn latency record the operator can read with `clwnd drift`.

The maintainer's own register names every part of this in botanical and organic terms. A turn is a `bloom` (open to wilt). The Claude CLI process pool is a `nest`, each process is a `roost`. The bidirectional NDJSON socket is the `hum`. Each message on it is a `tone`, every state mutation ticks a `wane` counter, every tone has a `dusk`. The optional context-loss watcher is the `drone`, which buffers the first eighty bytes of a turn in a `cup` and `withers` (kills and respawns) the roost if a heuristic and an LLM judge agree the model has lost context. Tools are `tendrils`, savings are `pennies`, and the cost-and-corruption ledger is `clwnd savings`. Per the `HUMAN2HUMAN.md` foreword in the repo: "ML / LLM / AI terminologies get very tiring for me. turn? turn where? to what? with petal i know where it falls, how it feels, smells, where it can fly to in the system." The vocabulary is structural. Distribution copy in English explains the system. It does not erase it.

The package description is `clwnd - take back what is yours`. The README opens with `\_ clwnd _/` and an IPA pronunciation `/klwʊnd/`. Treat the four lowercase letters as the name; there is no expansion to recover.

## Why it matters to the portfolio

clwnd is the portfolio's most ambitious agentic-systems artifact and also the most exposed one. It sits where two opaque tools meet (OpenCode is open-source, modular, written in Go and TypeScript; Claude Code CLI is proprietary, closed, and governed by Anthropic's consumer terms) and produces a working composite. The thesis the portfolio is making, that the substrates around agentic loops matter at least as much as the loops themselves, is more visible here than anywhere else. The penny-pincher counters (`readDedupHits`, `bashTruncated`, `curateBytesSaved`, `humDedup`, `priorPetalsElided`, `droneWithers`, `totalCost`) make the substrate's value legible in a number the operator already cares about.

clwnd is also the project where the portfolio's voice is most articulate as code, not just as README. The `hum` protocol's six primitives (`sigil`, `tone`, `echo`, `breath`, `pulse`, `reach`), the drone's four assessment levels (`serene`, `alert`, `tense`, `critical`), the cup's verdicts (`passthrough`, `buffered`, `withered`), the drift module's mark/span/hum/tendril taxonomy: this is the same maintainer who writes `iksir` in alchemical register and `rishta-lang` in kinship-typed lineage register, expressing the agentic-substrate thesis in its native vocabulary.

## Audience

The audience is narrower than the existing dossier credited and sharper.

The first kind is the heavy OpenCode user who already prefers OpenCode's TUI, modular plugin surface, and provider-agnostic model selection but wants Claude Sonnet/Opus quality through their Claude Code CLI install rather than through pay-as-you-go API spend. After Anthropic's April 2026 policy change pushed third-party harnesses off subscription-quota OAuth, this user is unusually mobilized and reading every plugin they can find. They live on the awesome-opencode list, the OpenCode Discord, and `r/LocalLLaMA` threads about local-and-claude hybrid setups.

The second kind is the OpenCode user who has already noticed that the default `read`/`grep`/`edit` tool surface produces churn (re-reading files that have not changed, hand-rolled `sed` for symbol replacement, drift between OpenCode's and Claude's view of state) and wants editing tools that understand symbols, not strings. clwnd's `do_code` operates by AST byte-range splice across fifteen tree-sitter grammars and rejects edits that produce invalid syntax. `do_noncode` operates by linguistic anchor (word, phrase, sentence, paragraph) on Markdown, JSON, YAML, env files, and Dockerfiles. `read` is the single filesystem-analysis tool that replaces glob, grep, and line-based read in one surface. This user is more rare than the first one, but cares more about what clwnd is doing.

The third kind is the operator who runs three or four agentic sessions in parallel and has had at least one go silently sideways with a "fresh session, how can I help" greeting mid-conversation. The drone is for them. It is opt-in (`droned: true`), runs a fast heuristic over the first eighty bytes of every turn, escalates to an LLM judge on suspicion, and respawns the Claude CLI process with the full JSONL re-grafted before the user sees the broken turn.

The audience that does not overlap cleanly: anyone wanting an Anthropic-API proxy, a cost dashboard alone, a process supervisor for arbitrary subprocesses, or a cross-vendor model router. clwnd is OpenCode-shaped and Claude-CLI-shaped on both ends. Generalizing dilutes it.

## Distinguishing claim

Three claims, each defensible from the code.

The first claim is that clwnd is the only OpenCode plugin that delegates the entire filesystem and editing surface to a daemon-owned, AST-grounded toolkit. The opencode-with-claude / Meridian / oc-go-cc neighbors all route credentials and stop there. clwnd routes credentials *and* swaps in `read`, `do_code`, `do_noncode`, `bash`, and `task`, with post-edit re-parse validation, staleness guards, and bytes-not-strings splicing. The plugin's `tool: { do_code, do_noncode, clwnd_permission }` block in `plugins/opencode/index.ts` is structural to the project, not a bonus feature.

The second claim is the JSONL graft. When OpenCode hands a session to a different model (or recovers from a `roost-died` pulse), `lib/session.ts` reconstructs Claude CLI's persistent JSONL on disk so the new process boots with the full lineage of petals, tool uses, and tool results in their native shape. This is what allows model switching mid-session without losing context, and it is the substrate that the drone's wither-and-respawn flow depends on. No comparable plugin in the OpenCode ecosystem owns Claude's JSONL with this much intent.

The third claim is the penny-pincher. clwnd is the only Claude-Code-adjacent tool the maintainer is aware of that quantifies its own contribution to the session by counting what it kept the loop from doing: re-reads it short-circuited (`readDedupHits`), bash output bytes it trimmed (`bashBytesTrimmed`), JSONL bytes pruned by curation (`curateBytesSaved`), reminder blocks stripped from system prompts (`reminderStripped`), `priorPetals` it elided from prompts the plugin already sent, context-corruption events the drone caught (`droneWithers`), and an estimated dollars-saved figure surfaced in the OpenCode TUI sidebar.

## Distribution state, current

The repository is more launch-ready than the prior dossier credited.

License is MIT, declared in `package.json` and present as `LICENSE` at the repo root. (Open issue #32 still flags this as a blocker; the issue is stale and should be closed.) The repo description on GitHub is "clwnd - your sentinel for multiplexed cross-harness AI bots" and topics are `claude-code`, `claudecode`, `opencode`, `opencode-plugins`, `agentic-coding`, `llm`, `swarm`. The latest release is v0.24.19 (2026-05-05); twenty releases in three weeks, cadence is high. (Open issue #34 asks for a v0.1.0 tag; the project shipped past that two months ago and the issue is stale.) NOTICE file at the repo root explicitly positions clwnd as a local execution utility that does not handle credentials, does not act as a proxy, and is not affiliated with Anthropic.

The install path is `curl -fsSL .../install | bash`, which clones the source under `$XDG_DATA_HOME/clwnd/src`, downloads a pinned Node.js into `$XDG_DATA_HOME/clwnd/node/v$PINNED` (so the daemon never breaks when the user's system Node changes), provisions pnpm via corepack, runs the build, and installs a `launchd` plist (macOS) or `systemd --user` unit (Linux). `clwnd doctor` is a real diagnostic that probes process, port `29147`, the launchctl/systemd unit, the unix socket triplet (`.sock`, `.sock.hum`, `.sock.http`), and the log files. `clwnd update` self-heals when the user's Claude CLI is below `MIN_CLAUDE` (currently `2.1.86`).

The OpenCode plugin ships from npm as `@clwnd/opencode`. Plugin and daemon are versioned together by `scripts/version.sh`. The TUI sidebar slot renders a `clwnd` badge with proc count, dollars saved, and session count. `clwnd savings` and `clwnd drift` are real readouts that work today.

The codebase tells a story of recent refactor: bun was migrated out wholesale on 2026-04-13 (issues #16, #23, multiple commits ending `feat: complete unbun`), the drone was extracted to `lib/drone/` on 2026-05-02, the drift module was added the same day with full coverage, and a small rename pass landed the same evening (`turn` → `bloom` in `drift` and `penny`). Three weeks of intense iteration with twenty tagged releases. The cadence and the rename pass tell us the project knows itself well enough to refactor its own vocabulary in flight.

## Distribution state, gaps

The README is short by design but assumes the reader already knows OpenCode and Claude Code CLI. A first-time visitor scrolling the README has the demo gif, the install one-liner, a config table, and a brief "core workflow is operational" callout. There is no paragraph that says, in plain terms, "you run OpenCode, you have a Claude Code CLI install, and clwnd lets the first one drive the second with custom edit tools and a context-loss watcher." That paragraph belongs above the install snippet. (Open issue #33 names this; it remains correct.)

No tagged release notes that read like release notes. Twenty releases on the GitHub Releases page each titled `v0.24.X` and nothing else. The first launch-grade release (call it v0.25.0 or v1.0.0, the maintainer's call) needs a hand-written body that names the surface area: OpenCode plugin, Claude CLI delegation, AST tools, JSONL graft, drone, drift, penny-pincher, OS service.

Not on the awesome-opencode list. clwnd is not currently listed among the 100+ plugins on `awesome-opencode/awesome-opencode`. The list's PLUGINS section is the right slot. There is no current entry for any Claude Code routing plugin (Meridian, opencode-with-claude, oc-go-cc all also missing); a clean opening to land first.

Not on the awesome-claude-code list either (`hesreallyhim/awesome-claude-code`). The right category is companion apps. clwnd's pitch there is different: not "Claude Code skill" or "subagent" but "OpenCode-side runtime that uses Claude Code as a provider and replaces the editing surface."

The thesis post on `adils.me` is missing. Per `policy.md` §6, the post is the launch's primary artifact. Show HN, Discord drops, and the awesome-list PRs all link back to the post, not to the repo. The right title direction is the maintainer's own substrate language: "clwnd: an OpenCode sidecar that takes Claude Code's loop seriously" or, plainer, "what's between OpenCode and Claude Code (and why it's worth a daemon)." The post should open with the demo gif, show one bloom record from `clwnd drift`, show one savings readout from `clwnd savings`, and close with the install one-liner.

The naming-collision risk at launch is the policy moment around third-party harnesses. clwnd is not Meridian-shaped (no SDK proxy, no OAuth interception) and the NOTICE file is careful, but a launch thread will surface "is this affected by the April 2026 ToS change" within the first hour. The README needs a one-paragraph "this uses your local Claude Code CLI install, not the Agent SDK or OAuth tokens" line near the top, in plain language, before the launch goes live. It is currently in legal register inside `NOTICE` and does not surface in the README.

There are stale distribution-issue artifacts open on the repo (#32 add LICENSE, #33 README front-matter, #34 v0.1.0 tag, #35 npm publish package.json setup) that were filed against a much earlier state of the project. Either close them with a short comment or rewrite their bodies to track the actual remaining work. They currently misrepresent the project's readiness to anyone scanning issues.

## Distribution state, aspirational

A longer demo video showing model-switching mid-session with the JSONL graft taking over, then a forced wither + respawn from the drone, would seal the pitch in 45 seconds. The current `clwnd.gif` shows the install and basic operation; it does not yet show the parts of the project that nothing else does.

A `clwnd savings` and `clwnd drift` screenshot pair would do most of the social-thread work. The `savings` output is already laid out in two-column form with daemon-side and plugin-side groups; it photographs well. The `drift` output is dense but readable, and the column it adds (`tendrils` per bloom) is the kind of visual that lands a substrate claim faster than prose.

A short `adils.me` companion post specifically on the JSONL graft would do unusual long-tail work. The graft is the hardest part of clwnd to explain in passing, and it is also the part most likely to come up in cross-tool comparison conversations as model-switching becomes more common in the OpenCode ecosystem. Three to five hundred words with one diagram.

OpenCode workspace adaptor support is documented as a future architectural fit in `OC_WORKSPACE_DISCOVERY.md`. If OpenCode's experimental `installAdaptor()` hook stabilizes, clwnd becomes a workspace type rather than a provider plugin. This is post-launch territory but worth marking; it would be a single substantive blog post worth of substance for a relaunch beat.

## Plan

Pre-launch, in order:

1. Close or rewrite stale distribution issues #32, #33, #34, #35 to match the project's actual state. Keep #33 open with the corrected scope (not "add a license", "add a paragraph above the install snippet plus a four-line ToS-posture inset").
2. Add the four-line ToS-posture inset to the README, near the top, in plain language. The substance already exists in NOTICE; the README needs its plain-English summary so it lands during a launch-thread skim.
3. Cut a tagged release with hand-written notes. Suggested name v1.0.0 to mark the launch readiness threshold; the maintainer can choose v0.25.0 if they prefer to keep the implicit "still moving fast" register. Notes should name the surface area in the project's own vocabulary (bloom, hum, drone, drift, penny) and resolve to plain English at the bottom.
4. Update `clwnd.gif` or add a second gif: 25 to 45 seconds, showing one bloom from prompt to wilt with the savings counter ticking and (optionally) one withered turn caught by the drone. The current gif is 2.5MB and predates these features.
5. Write the `adils.me` thesis post. Open with the gif. One bloom from `clwnd drift`. One savings readout. One paragraph each on the AST tools, the JSONL graft, the drone, and the penny-pincher. Close with the install one-liner.
6. PR draft to `awesome-opencode/awesome-opencode` PLUGINS section. Do not send before launch day. The entry needs a one-line description that names what clwnd does in 30 words or fewer, written in plain technical register (not the project's own register, per `policy.md` §3 plain-technical setting). Working draft direction: "OpenCode sidecar that delegates model calls to Claude Code CLI and replaces the file/edit toolset with AST-grounded tools, JSONL grafting, and a context-loss watcher."
7. Verify install on a fresh macOS account and a fresh Linux user. The install script is elaborate (pinned Node, corepack, launchd/systemd, MCP config rewrite); the failure modes are known, but a launch is not the time to discover a new one.

Launch beats, in order, on a Tuesday or Wednesday morning US Pacific:

1. Publish the `adils.me` post.
2. Show HN with the post URL. Title direction: "clwnd: an OpenCode sidecar for the Claude Code loop." Not a "Show HN: I built X" framing. The post is the artifact, the demo is the proof.
3. PR to `awesome-opencode` PLUGINS section.
4. PR to `hesreallyhim/awesome-claude-code` companion-apps section. The pitch is different there: clwnd as an OpenCode-side runtime that uses Claude Code as a provider, with structural value-add beyond credential routing.
5. Drop the post URL into the OpenCode Discord (the community is active, post-April-2026 migration is still echoing). The Discord audience is the most concentrated form of the first-kind audience named above.
6. Drop into `r/LocalLLaMA` framed around the substrate angle, not as another routing plugin. The post and the savings/drift readouts do the differentiating; do not lead with subscription routing in this venue.
7. X thread of four to five posts: gif lead, then a savings screenshot, then a drift readout, then the project's own one-line vocabulary tour ("bloom, hum, drone, drift, penny"), then the install one-liner. Keep the thread short.
8. Tip-line submissions to TLDR AI and Console.dev the day after the Show HN beat.

Post-launch, first 30 days:

1. Anthropic-ToS questions will arrive within the first hour. The NOTICE file already contains the substantive answer; have the four-line plain-English summary saved for thread responses.
2. "Why not Meridian / opencode-with-claude / oc-go-cc" will arrive next. The honest answer is that clwnd does something orthogonal: it does not bridge OAuth or wrap the Agent SDK, it sits in the OpenCode <-> Claude-CLI seam and replaces the editing surface. Have a paragraph saved.
3. The drone will be the most-asked-about feature, including from people who do not use OpenCode. Consider one short companion post specifically about heuristic-then-LLM context-loss detection on streaming output. That post is portable to other audiences.
4. The penny-pincher counters are the cheapest hook into recurring-attention. If users start posting their own savings screenshots, encourage it explicitly. Add a "Who uses this" section to the README as soon as one or more external users surface.

## What we are not doing

Not pitching clwnd as a "fleet manager" or "process supervisor." Both were imported categories in the prior dossier and neither fits the code. clwnd manages exactly one process pool (`ClaudeNest`) for exactly one tool (Claude Code CLI) on behalf of exactly one harness (OpenCode). Calling that fleet management imports an enterprise frame that does not match the artifact and lands the launch in the wrong audience.

Not pitching clwnd as a credential-routing plugin. That is what Meridian / opencode-with-claude / oc-go-cc do, and the existing dossier framing pulled clwnd into their lane. clwnd's NOTICE file is explicit that credential handling is the user's responsibility and clwnd does not access, collect, store, transmit, or manage credentials. Reusing the routing-plugin pitch would both misdescribe the project and invite ToS questions the project has no need to invite.

Not pitching clwnd as a generic agent observability tool. It is not OpenTelemetry-shaped, does not export OTLP, does not integrate into existing monitoring stacks, and is not designed for org-scale observation. The drift module is for the operator at the keyboard, not for a platform-engineering team.

Not extending preemptively to non-OpenCode harnesses. The dependency on `@opencode-ai/sdk` and the plugin shape are structural. Generalizing to crush, aider, or cline doubles the surface area for a smaller marginal audience. If demand surfaces post-launch, treat it as a separate project.

Not flattening the botanical register. The vocabulary in `daemon.ts`, `hum.ts`, `drone.ts`, `drift.ts`, `penny.ts` is the project's voice, named deliberately by the maintainer in `HUMAN2HUMAN.md`. Distribution copy in English explains it. It does not rename it.

## Metrics

Per `metrics.md` T2 per-launch floor / target / stretch.

| Metric | Floor | Target | Stretch |
|---|---|---|---|
| Stars added (30d) | 25 | 100 | 500 |
| Unique installers (30d) | 50 | 250 | 1k |
| External issues (30d) | 1 | 5 | 15 |

Project-specific signals to watch.

Savings-screenshot virality. The `clwnd savings` readout is a more quotable artifact than any prose pitch. If posts that screenshot a savings line ("$3.40 saved, 47 read-dedup hits, 2 corruptions caught") drive more inbound traffic than posts that screenshot the gif, the next thread should lead with savings. Track inbound by referrer.

Drift adoption among OpenCode plugin authors. `clwnd drift` produces a structured per-bloom latency record that other OpenCode plugins could in principle write to. If anyone in the ecosystem starts emitting `perf-mark` tones into the hum, that is a signal clwnd's data shape is becoming a small standard. Watch the awesome-opencode entries for cross-references.

External issue shape. clwnd is technical enough that any issue is engagement, not noise. The shape of the issues matters: questions about install paths and OS support are healthy, questions about routing vs Meridian are launch-thread fallout, contributions touching the drone or the AST tools indicate the substrate value is landing.

## Launches

None executed yet. The pre-launch checklist above is the gate.

## Notes

The previous dossier was written under three frames the code does not support. It described clwnd as a "sentinel for multiplexed cross-harness AI bots" and a "fleet of agentic coding sessions" governance tool, language that imports enterprise process-supervisor framing. The code is one harness (OpenCode), one provider (Claude Code CLI), and one process pool. It described the differentiator as permission gating, idle-process killing, and observer-style auto-recovery, which are real but secondary to the AST/linguistic editing toolkit and the JSONL graft. It claimed no LICENSE file existed; one has been at the repo root since v0.0.0. It claimed launches were blocked on a missing tagged release; twenty releases have shipped on the GitHub Releases page in the three weeks before this dossier. This revision replaces that frame with the project's own.

clwnd's own register is not "drone-style observer" or "process governance." It is the botanical vocabulary the maintainer chose deliberately and named in `HUMAN2HUMAN.md`. Bloom, petal, hum, sigil, nest, roost, drone, cup, tendril, wilt, withered, wane, dusk, drift, penny. Distribution copy in English explains what each one means in plain terms when the audience needs it. The vocabulary itself stays.

The Anthropic ToS posture is real and matters at launch. clwnd routes through the locally-installed Claude Code CLI binary, which is Anthropic's own product and is exempt from the consumer-ToS automation prohibitions. clwnd does not handle OAuth tokens, does not wrap the Agent SDK, does not proxy `api.anthropic.com`, and the NOTICE file is precise about each of these. The April 2026 ToS change targeted third-party harnesses using subscription OAuth (the OpenClaw cohort and the SDK-bridging proxies). clwnd is in a different category from those tools, and the README needs to make that legible to a thread skimmer in four lines.

The naming hygiene story: clwnd is `/klwʊnd/`, lowercase, four letters, no expansion. The repo description and the install one-liner both use the lowercase form. The `clwnd-playground` companion repo (T4) is for demonstration only and is not promoted standalone.

## Research log

Sources consulted, in order:

- Local clone at `~/clwnd`. `package.json`, `README.md`, `HUMAN2HUMAN.md`, `NOTICE`, `LICENSE`, `OC_WORKSPACE_DISCOVERY.md`, `clwnd.schema.json`, `bin/clwnd`, `install`.
- Source code: `daemon/daemon.ts`, `lib/hum.ts`, `lib/drone/drone.ts`, `lib/drone/cup.ts`, `lib/drone/classify.ts`, `lib/drone/index.ts`, `lib/drift.ts`, `lib/session.ts`, `lib/penny.ts`, `lib/config.ts`, `lib/ast.ts`, `lib/linguistic.ts`, `mcp/tools.ts`, `plugins/opencode/index.ts`, `plugins/opencode/provider.ts`, `plugins/opencode/tui.tsx`, `tests/e2e-serve.test.ts`, `reports/compat-v0.12.0.md`.
- Git history: full log (215 commits across `2026-03-20` to `2026-05-02`, twenty tagged releases). Attention to commits `226d547` (turn → bloom rename), `a91efa0` (drift module landing), `a24e682` (drone extraction to `lib/drone/`), `2314436` (unbun), `90c26da` (systemPrompt dedup eating AGENTS.md), `f04a912` (remote MCP bearer-from-mcp-auth.json), `38e9af4` (compaction hiccup kill), `6f082cd` (strip Anthropic harness fingerprints).
- GitHub issues, all 35: #32-#35 are stale distribution issues from a prior dossier pass. #27, #28, #30, #31 are open functional issues against `do_code` and edit tools. #1-#26 closed, mostly P1, mostly correctness or compat, including the `bun → node` migration arc (#16, #21, #22, #23, #24, #25), the hum protocol overhaul (#18), and `oc compat completeness` (#8).
- `gh repo view adiled/clwnd` for description, license, topics, latest release.
- `gh release list` for release cadence (twenty releases, 2026-04-12 to 2026-05-05).
- Existing dossier at `projects/clwnd.md`.
- `policy.md` (T2 launch checklist, license defaults, voice rules), `channels.md` (claude-code-adjacent and opencode-adjacent channels), `metrics.md`, `projects/_index.md`, `projects/ccft.md` (dossier shape and rigor reference).
- Web research scoped to the project's actual claim. `awesome-opencode/awesome-opencode` plugin list contents and contribution flow. Anthropic April 2026 third-party-tool policy reporting (theregister, venturebeat, mlq.ai). OpenCode Discord and ecosystem activity. Adjacent plugins (Meridian, opencode-with-claude, oc-go-cc) and how their pitch differs from clwnd's.

Sources rejected as imported-category framings:

- "Fleet manager" / "agent fleet governance" frame from the prior dossier. The code is one harness, one provider, one process pool. The fleet-management category leader audience (kubernetes-shaped operator-tool readers) is not clwnd's audience and forcing the frame would mis-route the launch.
- "Process supervisor" frame. clwnd is not a generic process supervisor; it spawns Claude CLI specifically and the JSONL graft is structural. Generic process-supervisor language would land it in the systemd / supervisord / pm2 lane where it has no business.
- "Credential routing plugin" frame from the awesome-opencode neighbour analysis. Meridian and opencode-with-claude are in that lane; clwnd is explicitly not, per the NOTICE file. Adopting the lane would import the OAuth-third-party-tool risk profile that does not apply to clwnd.
- "Agent observability platform" frame. clwnd is not OpenTelemetry-exporting, does not target organizations, does not have a multi-tenant story. Drift is per-operator-at-keyboard.
- "MCP gateway" frame. clwnd does expose an MCP endpoint for its own tools (`/s/<sid>` over port `29147`), but it is not a gateway between MCP clients and external servers in the registry sense. The MCP support is internal plumbing for tool dispatch.

What surprised me in step 4, when writing the project's-own-voice description.

The botanical/organic register is not flavor. It is the design vocabulary, and it shapes the code's structure. `bloom` is one assistant turn, with `marks` along it (graft_synced, nest_spawned, murmur, first_petal, first_reasoning_start, first_text_start, first_tool_input_start, first_uncup, first_bloom, oc_bus_publish, oc_sse_write, wilt) that the drift module uses as a per-turn instrument. `hum` is the wire protocol with six primitives, each one named in the same vocabulary (`tone`, `echo`, `breath`, `pulse`, `sigil`, `wane`, `dusk`, `reach`). The drone is a four-state finite state machine (`serene`, `alert`, `tense`, `critical`) with a rhythm that adapts. The cup is a buffer with three verdicts (`passthrough`, `buffered`, `withered`). The penny-pincher is a cost-and-corruption ledger with twenty named counters. None of this is decoration; it is the mental model the maintainer is using to keep the code coherent across a 86KB single-file daemon, a 67KB provider, and a 100KB MCP tools surface. Distribution copy that flattens any of it into "ledger" or "observer" loses what is interesting about the artifact.

The other surprise was how legible the Anthropic-ToS posture is in the code. The NOTICE file is a serious legal document, not a disclaimer afterthought. The README's "headless (compliant) mode" parenthetical is specific. The `should_intercept`-style host gating that protects ccft does not have a direct analogue here, but the daemon never touches `api.anthropic.com` directly and never sees an OAuth token. The April 2026 third-party-tool policy targets a specific class of tool (subscription OAuth in third-party harnesses, SDK-bridging proxies), and clwnd is sitting deliberately outside that class. The launch can lean on this posture if the README surfaces it in plain English near the top.

The prior dossier flagged "no license file" as a launch blocker. The license has been MIT and present at the repo root since v0.0.0. The four open distribution issues (#32-#35) read as artifacts of a much earlier pass on this project, before the project actually shipped, and they currently misrepresent the project's state to anyone scanning issues. Closing or rewriting them is itself a small distribution task.
