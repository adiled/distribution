# iksir

**Tier:** T3 (Ambitious / pre-launch) • **Repo:** https://github.com/adiled/iksir • **License:** MIT • **Stars:** 0 • **Latest release:** none tagged yet (deno.json declares `0.1.0`)

## What it is

iksir is a Deno daemon that runs on a Linux box and turns Linear tickets into git branches with OpenCode sessions working on them, one at a time, with the human operator routed in over Telegram for the questions that actually matter. The Cargo-style description in `deno.json` is `@iksir/core` v0.1.0; the source's own one-line self-description in `src/main.ts` is "Iksir Daemon - Autonomous Agent Tansiq". The README opens with the same alchemical poem the maintainer has been refining across forty-eight commits, and that register is structural: every module, every type, every database table, every MCP tool name is in Arabic alchemical vocabulary. Distribution copy in English explains the system. It does not flatten it.

Mechanically, three systemd services compose the runtime. `iksir-mcp` is the MCP server on port 3100 that exposes alchemical tools (`mun_istikhlas`, `mun_talaum`, `mun_istihal`, `mun_istihal_mutabaqq`, `mun_fasl`, `mun_naqsh`, plus seventeen others) for an OpenCode model to call. `iksir-agent` is OpenCode itself, bound to port 4096, hosting the actual coding sessions. `iksir` is the long-lived daemon that holds the control loop. State lives in one SQLite file at `~/.local/share/iksir/iksir.sqlite` with WAL mode on and seven tables (`jalasat`, `qanawat`, `ahdath`, `asila`, `qararat`, `ahwal_tanfidh`, `matalib_muallaq`). The daemon and the MCP server talk to each other through that database, not over a socket.

The daemon is composed of named servants (Khuddām). Each one is a class with a sacred docblock at the top of its file that describes what it does in alchemical register before a single line of code runs. **Munadi** receives utterances from the operator, asks **Arraf** to divine intent (URL detection first, then an LLM oracle if the marks are faint, then a sijill search), and routes resolved work to a murshid. **Katib** inscribes every vessel that gets lit and names the branches by convention (`epic/{huwiyya}-{kunya}`, `{ismMustakhdim}/{huwiyya}`, `sandbox/{kunya}`). **Saail** receives questions a murshid raises through OpenCode's question tool, runs each one through **Mumayyiz**'s assay (an LLM judge that classifies each sual as `DHAHAB` or `KHABATH` per the prompt at `prompts/mayyaza-sual.md`), and either auto-answers the dross or forwards the gold to Telegram with an inline keyboard. **Munaffidh** watches the `ahdath` table for unprocessed tool calls and executes them against Linear, GitHub, and git. **Raqib** ticks once a minute checking for stuck sessions (no token flow for five minutes), bloated sessions (over fifty messages), and retry loops, then alerts and aborts. **Hayat** watches every PR a murshid has opened, fires callbacks for merge and close and conflict and CI failure and review comments, classifies operator comments as commands versus suggestions versus questions, and during quiet hours merges main into every vessel branch and reports conflicts.

The control protocol is the project's most distinctive part. Only one murshid is in the `fail` state at a time. The other states are `sakin` (idle), `masdud` (blocked) and `muntazir` (waiting). The murshid yields voluntarily through the `mun_tanazal` tool when it is blocked or waiting, and demands control back through `mun_talab_tahakkum` when it has actionable work. The operator never has to click "pause"; the murshid surrenders the flame on its own when it cannot proceed. Munadi guards the law. Switching from one murshid to another is a sacred intaqala that does a WIP commit on the previous vessel's branch, checks out the new vessel's branch, and routes notifications to the new vessel's Telegram topic.

The transmutation pipeline is where the alchemy reads as engineering. A murshid working in a `buwtaqa` branch (the messy crucible) calls `mun_istikhlas` with a list of files to extract, then `mun_talaum` to test that those files type-check and stand alone without the rest of the buwtaqa, then `mun_istihal` to put them on a clean `jawhar/{huwiyya}` branch, then `mun_fasl` to open a draft PR (a `risala`), then eventually `mun_naqsh` to merge. Layered transmutations (`mun_istihal_mutabaqq`) build a child jawhar on top of a parent jawhar. The pipeline is implemented in `src/kimiya/istihal.ts` and surfaced as a typed sequence the murshid follows under instruction from the prompt at `prompts/iksir-murshid.md`.

The README is misleading on two specific points and the code is the truth. The README lists CLI commands `iksir divine`, `iksir transmute`, `iksir rekindle`, `iksir verify`. The actual CLI in `src/cli.ts` exposes `init`, `start`, `stop`, `restart`, `status`, `update`, `check`, `sync`, `config`, `help`, `version`. The README also says "Edit `~/.local/share/iksir/src/.env` to bind your keys, then `iksir start`" while telling the user the prerequisites are Deno and OpenCode. The prerequisites in the install script also include Linux with systemd: `register_services` calls `die "unsupported OS: $OS (only Linux is supported)"` for everything that is not Linux. macOS users who try to install will fail at the service registration step.

## Why it matters to the portfolio

iksir is the portfolio's first artifact that takes the agentic-systems thesis past the local-coding-tool layer and into autonomous ticket-driven work with a human in the messaging loop. `clwnd` sits between OpenCode and Claude Code CLI for one operator at the keyboard. `ccft` watches one operator's Claude Code traffic for vibe drift. iksir runs on its own and waits to be summoned. The pitch is not "make your coding session more legible" but "send your tickets in, and only get pulled in for the questions that pass the assay".

iksir is also where the portfolio's voice register is most committed. The last forty-eight commits are almost entirely vocabulary work. Sixty symbol renames in `munadi.ts`, a full schema rewrite of the sijill, arabicization of every `Nida*` tool call interface, then a pass to fix English-Arabic hybrid identifiers (`englarabic`), then a coherence pass on the README. The maintainer is treating the alchemical register as a load-bearing constraint, not as decoration. Distribution that flattens it loses the audience the register is meant to attract.

## Audience

The audience is small, sharp, and not the audience the previous dossier named.

The first kind is the operator who already runs Linear or a Linear-shaped issue tracker for their own work, has agentic coding tools in their stack (OpenCode plus Claude through OpenCode), and is willing to keep a Linux box on for long-running work. They want their tickets to advance while they are not at the keyboard, they want to be pulled in only for actual decisions, and they accept that the answer will sometimes be "the murshid is blocked, please answer this multiple-choice question on Telegram." This is a workflow shape that did not exist before the current generation of agentic dev tools made it plausible. iksir is one of the first concrete attempts at it.

The second kind is the operator who has tried to wire OpenCode or Claude Code into Linear or GitHub Issues themselves, hit the integration-glue tax, and would rather inherit an opinionated daemon than build their own. The MCP tool surface for issue tracker mutation (`mun_khalaq_wasfa`, `mun_jaddid_wasfa`, `mun_wadaa_alaqat`) plus the tool surface for GitHub mutation (`mun_khalaq_risala`, `mun_iltazim`, `mun_idfa`, `mun_naqsh`) plus the tool surface for the transmutation pipeline (`mun_istikhlas`, `mun_talaum`, `mun_istihal`, `mun_fasl`) together amount to a serious-sized integration. A single user who decides they want this is inheriting maybe two thousand lines of glue they would otherwise write themselves.

The third kind is the practitioner who reads README's like the iksir README and wants the system in their tooling. Sufi-adjacent, alchemy-aware, drawn to vocabulary that takes itself seriously. This is a small cohort across the portfolio's potential audience, but they are loyal once they install something. The maintainer's `rishta-lang` (kinship-typed lineage), `clwnd` (botanical), and iksir (alchemical) all attract from the same shelf.

The audience that does not overlap cleanly: anyone wanting a parallel-agent fan-out (the Manus/Devin shape), anyone wanting a CLI tool for a single coding session (that is `clwnd`'s lane and `opencode-dir`'s lane), anyone running on macOS who expects the install to work today, and anyone who wants to operate without Linear and Telegram. The second of those is the largest gap.

## Distinguishing claim

Two claims, both defensible from the code.

The first claim is the yield/demand control protocol. The murshid surrenders the flame on its own when blocked, through `mun_tanazal`, with reasons (`masdud` or `muntazir`), details, and an optional next-step suggestion. It demands the flame back through `mun_talab_tahakkum` with priority levels. Munadi guards the law that only one murshid is `fail` at a time, performs an intaqala when the operator switches focus, and does a WIP commit on the previous vessel's branch before moving on. No comparable agentic dev tool the maintainer is aware of frames concurrency this way. Most of the neighbourhood is parallel-by-default with a queue at the front; iksir is sequential-by-default with voluntary yield. That design choice is the substance of the project's name and it shows up in eight specific places in the code (`HalatMurshid` type, `huwiyyaFaaila` field, `intaqalaIla` flow, `mun_tanazal` tool, `mun_talab_tahakkum` tool, the `talabKhitam` queue, the resume-on-startup logic in `istarjaaIndaNashaat`, the `/switch` and `/queue` slash commands).

The second claim is the question-assay pipeline. When a murshid pauses and calls OpenCode's question tool, Saail receives the question, hands it to Mumayyiz, which loads the prompt at `prompts/mayyaza-sual.md` along with `~/.config/iksir/AGENTS.md` and asks an LLM whether this question is `DHAHAB` (worth the operator) or `KHABATH` (the murshid should answer itself per the prompt's guidance). Khabath gets auto-answered with a terse rejection and a guidance string. Dhahab gets sent to Telegram with an inline keyboard built from the question's options. The operator clicks a button or types a custom answer in the murshid's topic, and Saail routes it back. This is a real differentiator against tools where every model question lands on the human regardless of whether the answer was already inferable from the agent's own context.

There is a third claim that is design-time but not yet runtime: the transmutation pipeline (`buwtaqa` → `istikhlas` → `talaum` → `istihal` → `fasl` → `naqsh`) is an explicit attempt to model the messy-to-clean refactor as a sequence of named tools rather than as a single "create PR" action. This deserves a launch artifact of its own. It is currently buried in the prompts and the MCP tool surface and not surfaced anywhere in the README.

## Distribution state, current

The repository is closer to launchable than the previous dossier credited, on some axes, and further on others.

License is MIT with the copyright holder declared as `al-Khīmiyāʾī`, in `LICENSE` at the repo root, present since commit `4856527`. The file is real, the license header is correct. The project is publishable.

The codebase is twenty thousand lines of TypeScript across sixty-three files, with tests for the non-trivial modules (`db.test.ts`, `katib.test.ts`, `mumayyiz.test.ts`, `saail.test.ts`, `messenger.test.ts`, `config.test.ts`, `strings.test.ts`, `time.test.ts`, plus a smoke test). The Deno tasks include `typecheck`, `fmt`, `lint`, and `test`. The `iksir check` CLI command runs config validation, typecheck, and tests in one pass.

The install path works on Linux. `curl -fsSL https://raw.githubusercontent.com/adiled/iksir/main/install | bash` clones the source into `~/.local/share/iksir/src`, copies `iksir.json.example` to `~/.config/iksir/iksir.json` and `agents.md.example` to `~/.config/iksir/AGENTS.md` and `.env.example` to `~/.local/share/iksir/src/.env`, symlinks `prompts/iksir-*.md` into `~/.config/opencode/agent/` and `plugins/iksir.ts` into `~/.config/opencode/plugins/`, writes three systemd unit files (`iksir-mcp.service`, `iksir-agent.service`, `iksir.service`) into `~/.config/systemd/user/`, and starts them. `iksir doctor` is not implemented; the closest equivalent is `iksir check` (config plus typecheck plus tests) and `iksir status` (systemctl is-active for each service plus a session listing from the sijill).

A release script (`./release patch|minor|major`) and a release workflow (`.github/workflows/release.yml`) are both present and ready to fire on a `v*` tag. `gh release list` is empty: no tagged releases yet. `git tag -l` is empty. `deno.json` declares version `0.1.0` but no tag has been cut. This is the cleanest pre-launch readiness gap to close.

The repo's GitHub description is `Taʿāl ilā hunā yā khīmiyāʾī.` ("Come here, alchemist."). Topics on the repo are not set. There is no website or thesis post.

There are two open distribution issues, both filed against the previous dossier's framing. They remain accurate in spirit but their bodies misdescribe what iksir is.

Issue #1, "Record demo: submit, block, answer, complete." The substance of the ask is correct: a sixty-to-ninety second demo showing a Linear URL going in, a murshid waking up, a question coming back to Telegram, the operator answering, and the murshid completing. The body's framing of "submit a recipe, see worker pick it up" understates what iksir does (the worker reads from Linear; the operator submits a URL or types in Telegram, not "a recipe"). Keep the issue open, rewrite the body to match the actual flow.

Issue #2, "Add 'Operationally' section to README with concrete mechanics." The substance of the ask is correct and it is now the largest distribution gap on the project. The body's suggested operational paragraph ("iksir runs a serialized work-queue for opencode sessions. Submit a recipe (formula). Workers execute one at a time on isolated branches.") is technically wrong on two points: it is not a queue in the FIFO sense, and the workers do not pick from a recipe submission, they are spawned on Linear ticket URLs. Keep the issue open, rewrite the body. Suggested replacement text appears under "Distribution state, gaps" below.

## Distribution state, gaps

The README is the largest gap. A first-time visitor cannot answer "what does this actually do, and what do I need to run it" within sixty seconds. The CLI commands listed in the README do not match the CLI as implemented. The platform constraint (Linux with systemd, no macOS, no Windows) is not stated. The dependency on Linear and OpenCode and a Telegram bot are listed in passing. Anyone evaluating this in the time it takes to scroll the README will leave thinking it is a poetic CLI tool, when in fact it is an autonomous daemon that connects four external services (OpenCode, Linear, GitHub, Telegram) and runs three long-lived systemd units.

A working draft for the operational paragraph, to sit above the curl install line:

> iksir is a daemon that runs OpenCode sessions on Linear tickets, one at a time, with the human routed in over Telegram only when the assay marks a question worth answering. Send a Linear URL into the Dispatch topic. iksir spawns a murshid on its own branch. The murshid works through the transmutation pipeline (extract, harmonize, transmute, decant, inscribe), opens a draft PR, and surrenders the flame whenever it is blocked. You return to it through Telegram or a slash command in the dispatch topic. Linux only (uses systemd). Requires Deno, OpenCode, a Linear API key, and a Telegram bot.

No tagged release. The release machinery is in place; cutting `v0.1.0` is one `./release patch "first tagged release"` away. The GitHub Action attaches release notes from the tag annotation. This is the cheapest single action that improves the project's distribution surface measurably before any launch beat.

No demo. The maintainer has run iksir against their own ticket flow (the `agents.md.example` file's references to "the operator" and the maintained AGENTS.md path under `~/.config/iksir/` both suggest dogfooded use). A vhs or asciinema recording of one full bloom (URL in, murshid spawned, question raised, answered, PR opened) is the killer asset and it does not exist.

No thesis post on `adils.me`. Per `policy.md` §2 and the T3-to-T2 promotion rule, the post is the launch's primary artifact. It does not need to be long. Three sections would carry it: the yield-demand control protocol (why one alchemist at the flame instead of fan-out), the question assay (most agent questions are dross, here is how iksir tells), and the transmutation pipeline (why `istikhlas`/`talaum`/`istihal`/`fasl`/`naqsh` is a different shape than `git branch && commit && push && PR`).

Not on `awesome-opencode`. The PLUGINS section of `awesome-opencode/awesome-opencode` is the natural slot. iksir is not a plugin in the strict sense (it is an OpenCode plugin plus an external daemon plus an MCP server), but the list also accepts companion runtimes. `clwnd` will land there first; iksir can follow once it has a tagged release and the operational paragraph.

No `awesome-claude-code` entry either. This is more of a stretch fit than `awesome-opencode` because iksir routes through OpenCode-as-harness rather than Claude Code directly, but the `mun_*` tool surface plus the alchemical pipeline are content the list's audience would read.

GitHub repo description is the maintainer's Arabic invitation, which is excellent register and zero distribution surface. A second-line English description in the GitHub `description` field would let scrapers and feed-readers find the repo. Keep the Arabic. Add an English second line.

Topics on the repo are not set. Adding `agentic-coding`, `opencode`, `linear`, `mcp`, `deno`, `alchemy` would make the repo discoverable through GitHub's topic pages without breaking the register.

The Linear-only constraint is real. The codebase has a `MutabiWasfa` interface that is provider-agnostic in shape (`provider`, `getIssue`, `searchIssues`, `parseUrl`, `getUrlPattern`, etc.), but the only implementation is `src/linear/client.ts`. A Jira or Linear-or-self-hosted-or-GitHub-Issues fallback is one more `MutabiWasfa` implementation away, but it is not done. Document the constraint until the second implementation lands.

The Telegram-only constraint is similarly real. The codebase has a `RasulKharij` interface and an `ntfy.ts` notifier alongside the Telegram one, and an inline-keyboard abstraction in `messenger.ts`. The Telegram dependency is structural for the question-keyboard UX. Slack or Discord adapters would be plausible later but they are not done. Document the constraint until a second messenger lands.

## Distribution state, aspirational

A landing page at `iksir.dev` (the schema URL `iksir.dev/iksir.schema.json` already implies the maintainer has the domain in mind). Three paragraphs and one demo is enough. The page is canonical for SEO and outranks the GitHub repo for the project name search.

A short companion post on `adils.me` specifically about the yield/demand protocol would do unusual long-tail work. The protocol is the most portable idea iksir contains. It is not OpenCode-specific. It is not Linear-specific. The post survives even if iksir itself never grows beyond the maintainer.

A macOS launchd port is plausible. The install script's `register_services` switches on `uname -s` and dies on anything that is not Linux. The systemd unit files in the install script are templated; a launchd plist version is mechanically straightforward and would unlock the most-likely-first-user (a maintainer who runs OpenCode locally on a Mac). Not pre-launch territory; post-launch.

An LLM-classifier-free fallback for Mumayyiz is worth thinking about. The `prompts/mayyaza-sual.md` and `prompts/mayyaza-tanbih.md` prompts route every question and every notification through an extra LLM round-trip. Caching obvious classifications, or letting the user pin specific categories, would reduce iksir's per-tick token spend. Not blocking on this.

## Plan

Pre-launch, in order:

1. Cut `v0.1.0`. Run `./release patch "first tagged release"` from a clean working tree. The GitHub Action attaches release notes from the tag annotation. This is the cheapest single action that improves the project's distribution surface measurably (RSS subscribers, dep crawlers, GitHub Releases page, install script's `latest_tag` discovery all benefit).
2. Rewrite the README opening. Add the operational paragraph above the alchemical poem. Fix the listed CLI commands to match the implemented CLI. State the Linux-only constraint, the Linear-and-Telegram-required constraint, and the OpenCode-required constraint up front. Keep the alchemical poem; it is the project's voice. Move it below the operational paragraph.
3. Update the GitHub repo description to add a second line in English: "iksir is a daemon that runs OpenCode sessions on Linear tickets, one at a time, with the operator routed in over Telegram only for questions worth answering. Linux only." Keep the Arabic first line.
4. Set GitHub topics: `agentic-coding`, `opencode`, `linear`, `mcp`, `deno`, `alchemy`.
5. Rewrite the bodies of issues #1 and #2 in place. Both asks are still correct; the framings need updating.
6. Record the demo. About sixty to ninety seconds. URL into Telegram dispatch topic. Murshid topic appears. Murshid raises a question. Operator answers via inline keyboard. Murshid completes. Embed the gif under the operational paragraph in the README. `vhs` or `asciinema` both work; `asciinema` is friendlier for terminal-only output, `vhs` for the Telegram side. The demo will probably need both: a terminal cast of `iksir status` plus a screen recording of the Telegram conversation.
7. Write the `adils.me` thesis post. Three sections (yield/demand protocol, question assay, transmutation pipeline). Open with the demo. Close with the install one-liner and the platform constraint. Do not put a legal-name byline on it.

Promote-to-T2 gate (per `metrics.md` and `policy.md`'s four-condition rule):

1. The thesis post exists and is forwardable.
2. The demo exists and shows the result, not the install.
3. Install works clean on a fresh Linux user account that has never seen iksir.
4. There is one named first user beyond the maintainer who has installed and given feedback.

Launch beats, only after the gate is met:

1. PR to `awesome-opencode/awesome-opencode` PLUGINS section.
2. Drop the post URL into the OpenCode Discord. The community there is the most concentrated form of the audience and the most willing to install something Linux-only.
3. Drop the post URL into `r/LocalLLaMA` framed around the autonomous-on-tickets angle, not as another OpenCode plugin. The post and the demo do the differentiating.
4. X thread of three to four posts: demo gif lead, then the question-assay screenshot, then the yield/demand protocol diagram, then the install one-liner and the platform constraint.
5. Tip-line submission to TLDR AI and Console.dev only after the launch-day surface settles. Both editors prefer to see organic traction before they pick something up.

Show HN is not in the launch beat list. The reason: iksir's surface area on a cold reader's first thirty seconds is too high to survive HN's attention budget. Linux-only, four external dependencies, alchemical vocabulary, autonomous daemon. The audience that will use iksir well is not on HN's home page; it is on the OpenCode Discord and `r/LocalLLaMA`. Show HN is plausible later, after iksir has a working second user and an `adils.me` post that does the work of explaining.

Post-launch, first thirty days:

1. The most-asked question will be "does it work without Linear" or "does it work without Telegram". The honest answer is no on both today, with named pull requests welcome. Have that paragraph saved.
2. The second most-asked question will be macOS support. The honest answer is launchd port is plausible, not committed. Have that paragraph saved.
3. The third question, slower-burn, will be about the alchemical vocabulary. The maintainer's HUMAN2HUMAN-style answer (the way `clwnd` documents its botanical register) is missing here and would help. A short `VOCABULARY.md` mapping each Khadim to its operational role, each pipeline stage to its git equivalent, would let a sceptical engineer understand the system without having to convert in their head every time.

## What we are not doing

Not pitching iksir as a "single-piece-flow framework." That phrase is a lean-software import, it does not appear anywhere in the code, the maintainer has not used it, and it would route the launch into a productivity-methodology audience that is not the project's audience. The project's own register is alchemical. The closest the README gets to a process claim is "ten furnaces burning wild produce only smoke. One furnace, properly tended, produces gold." That is the claim. Distribution copy explains what one-furnace means for an autonomous coding daemon. It does not adopt the lean-software vocabulary on top.

Not pitching iksir as the philosophical opposite of `clwnd`. The previous dossier framed it that way. The two projects are not opposites; they sit at different layers. `clwnd` is a sidecar for a single operator at the keyboard running OpenCode against Claude Code CLI. iksir is an autonomous daemon turning Linear tickets into PRs with the operator on Telegram. They share OpenCode as a substrate. They do not compete for attention from the same first-thirty-seconds reader. Cross-linking them as alternatives would confuse both audiences.

Not pitching iksir as an "agent queue" or a "task orchestrator". The MCP tool surface is much larger than queue management. Most of the substance is in the transmutation pipeline (`istikhlas`/`talaum`/`istihal`/`fasl`/`naqsh`), the question assay (Mumayyiz), and the maintenance loop (Hayat during quiet hours). Calling it a queue truncates the project to its smallest surface.

Not pitching iksir as a Linear plugin. Linear is the only issue tracker today, but it is one named implementation behind a `MutabiWasfa` interface. Calling iksir "a Linear plugin" lands it on the Linear marketplace's audience, which is product-management-flavoured and not the audience that will use the alchemical pipeline well. The Linear dependency is a constraint to disclose, not a positioning anchor.

Not flattening the alchemical register. The voice is the maintainer's commitment. The last forty-eight commits are vocabulary work. Distribution copy in English explains what each Khadim does. The vocabulary itself stays.

Not co-launching with `clwnd`. They are different audiences, different platforms (clwnd is macOS-and-Linux, iksir is Linux-only today), and different attention budgets. Stagger.

## Metrics

Per `metrics.md`, T3 is binary readiness, not numeric reach.

Promotion gate to T2 (all four required):

1. One-paragraph pitch exists that someone outside the project would forward.
2. A 30-second demo exists showing the result.
3. Install works clean on a machine that has never seen the project.
4. There is one named first user the project is for.

Project-specific signals to watch once T2 launches happen:

Question-assay screenshot virality. The Mumayyiz output ("dhahab: forwarded to al-Kimyawi" or "khabath: auto-answered") is the cheapest visual artifact iksir produces. If posts that screenshot a Mumayyiz classification drive more inbound traffic than posts that screenshot the alchemical hierarchy diagram, the next thread should lead with classifications. Track inbound by referrer.

Issue tracker request. The single most-likely external pull request is "add Jira" or "add GitHub Issues" or "add Plane" as a `MutabiWasfa` implementation. The shape and quality of the first such PR (does it match the existing interface, does it add tests, does it understand the alchemical register) is the leading indicator that iksir is being read carefully by people the maintainer does not know.

Linux-port pull request. The macOS launchd port is the second-most-likely external PR. Same shape-and-quality signal applies.

## Launches

None executed yet. No tagged release yet. Pre-launch checklist above is the gate.

## Notes

The previous dossier was written under three frames the code does not support. It described iksir as a "Deno-based opencode workflow tool" which is technically right but truncates the project to its substrate; iksir is an autonomous daemon that uses OpenCode as a model-running substrate, not a tool that lives inside OpenCode. It described iksir as the "philosophical opposite" of `clwnd`, which is a positioning claim rather than a description of the code, and which puts iksir into a "single-piece-flow versus fan-out" frame that is not in iksir's own register. It listed "Sacred servants. Internal roles..." as if the Khuddām were nominal flavour, when each one is a class with a sacred docblock and concrete substrate functions that the dossier needs to name to describe what iksir does. This revision replaces those frames with the project's own.

iksir's own register is alchemical. The eight Khuddām (Munadi, Katib, Arraf, Saail, Mumayyiz, Raqib, Hayat, Munaffidh) are first-class. The pipeline (`buwtaqa` → `istikhlas` → `talaum` → `istihal` → `fasl` → `naqsh`) is first-class. The control protocol (`fail`/`sakin`/`masdud`/`muntazir` plus `mun_tanazal` and `mun_talab_tahakkum`) is first-class. The vocabulary itself stays. Distribution copy in English explains what each one does in operational terms when the audience needs it.

Naming hygiene: iksir is `Iksīr الإكسير` in formal register, `iksir` in lowercase prose, four lowercase letters in URLs and the install one-liner. The repo description in Arabic is correct register; the package name on Deno (`@iksir/core`) is correct; the schema URL `iksir.dev/iksir.schema.json` implies a domain reservation that is not yet verified. The MCP server is `iksir-mcp`, the agent runtime is `iksir-agent`, the daemon is `iksir`. Three systemd units. Keep the names.

The maintainer's identity in the LICENSE is `al-Khīmiyāʾī`, not the legal name. This matches `policy.md` §2 (handle-only public identity). The convention of the file is correct. Do not add a legal-name byline anywhere in the launch surface.

GitHub issues #1 and #2 are open and the substance of both asks is still correct, but their bodies were written under the previous dossier's framing and now misdescribe iksir. Rewrite the bodies in place. Do not close them. The asks are real.

There is no `bunker` reference and no private surface in this dossier; every claim is from the public repo, the public README, the public commit history, and the public install script.

## Research log

Sources consulted, in order:

- Local clone at `~/iksir`. `deno.json`, `README.md`, `LICENSE`, `install`, `release`, `iksir.json.example`, `iksir.schema.json`, `agents.md.example`, `.env.example`.
- Source code: `src/main.ts`, `src/cli.ts`, `src/init.ts`, `src/types.ts`, `src/constants.ts`, `src/config.ts`, `src/daemon/munadi.ts`, `src/daemon/katib.ts`, `src/daemon/arraf.ts`, `src/daemon/saail.ts`, `src/daemon/mumayyiz.ts`, `src/daemon/raqib.ts`, `src/daemon/hayat.ts`, `src/daemon/munaffidh.ts`, `src/kimiya/istihal.ts`, `src/mcp/iksir-mcp.ts`, `src/mcp/serve.ts`, `src/linear/client.ts`, `src/notifications/telegram.ts`, `src/notifications/messenger.ts`, `src/notifications/ntfy.ts`, `src/git/operations.ts`, `src/github/gh.ts`, `src/opencode/client.ts`, `src/code-intel/indexer.ts`, `db/db.ts`, `plugins/iksir.ts`, `prompts/iksir-murshid.md`, `prompts/iksir-sani.md`, `prompts/mayyaza-sual.md`, `prompts/mayyaza-tanbih.md`.
- Git history: full log (forty-eight commits, all on `main`, no tags). Attention paid to the long arabicization arc (sixty renames in `munadi.ts`, the sijill schema rewrite, the `Nida*` tool call interfaces), the `Sail` to `Saail` and `Fail` to `Faail` transliteration commit (`0094105`), the install script rewrite (`f7ea0c9`, replacing the prior `kindle` script with the self-contained curl-and-bash installer), the LICENSE addition (`4856527`, copyright held by `al-Khīmiyāʾī`), and the README coherence pass (`0a5eeb6`, which fixed Saail, the full khuddām hierarchy, and removed earlier "workshop" framing).
- GitHub issues: #1 (record demo, OPEN) and #2 (operational tier in README, OPEN). Both asks are still correct in substance; both bodies misdescribe the project's mechanics under the previous dossier's framing and need rewriting in place.
- `gh repo view adiled/iksir` for description (`Taʿāl ilā hunā yā khīmiyāʾī.`), license (MIT), topics (none), latest release (none).
- `gh release list` confirming no tagged releases.
- Existing dossier at `projects/iksir.md`.
- `policy.md` (T3-to-T2 promotion rule, license defaults, voice rules, identity rule), `channels.md` (T3-iksir-specific note: operational tier in README is the gating gap; awesome-opencode and opencode community after that), `metrics.md` (T3 binary readiness gate), `projects/_index.md`, `projects/clwnd.md` and `projects/ccft.md` (dossier shape and rigor reference).
- `cat /Users/adil/distribution/projects.json` for the current `note` field on `adiled/iksir`.

Sources rejected as imported-category framings:

- "Single-piece-flow framework" / "lean software" frame from the previous dossier. The phrase appears nowhere in the code or the README. Importing it routes iksir into a productivity-methodology audience that is not the project's audience. The closest the README gets to a process claim is the alchemical poem about one furnace versus ten, and that is the right register for the launch.
- "Agent queue" / "task orchestrator" frame. The MCP tool surface is much larger than queue mechanics, and the substance is in the transmutation pipeline plus the question assay plus the maintenance loop, not in the task router. The dispatcher is one component of seven; framing iksir as a queue truncates it.
- "Opencode workflow tool" frame. iksir uses OpenCode as a model-running substrate, not as a workflow shell. The daemon orchestrates OpenCode; OpenCode does not orchestrate iksir. Calling iksir an "opencode workflow tool" puts it in opencode-dir's lane, where it does not belong.
- "Linear plugin" frame. Linear is the only `MutabiWasfa` implementation today but the interface is provider-agnostic and the launch should disclose Linear as a constraint, not adopt the Linear-marketplace audience.
- "Philosophical opposite of clwnd" frame. Both projects sit on OpenCode as a substrate, but they are at different layers (clwnd is sidecar-shaped for one operator at a keyboard, iksir is daemon-shaped for autonomous ticket-driven work). Cross-linking them as alternatives would confuse both audiences.

What surprised me in step 4, when writing the project's-own-voice description.

The yield/demand protocol is more articulate than the existing dossier credited. It is not just "one worker at a time"; it is a four-state finite state machine on each murshid (`fail`, `sakin`, `masdud`, `muntazir`) with two MCP tools (`mun_tanazal`, `mun_talab_tahakkum`) the murshid uses to surrender and reclaim control voluntarily. The dispatcher (Munadi) guards the law that only one murshid is `fail` at a time and performs an intaqala (WIP commit, branch checkout, notification routing) on transitions. This is a genuinely different concurrency model from the parallel-by-default agent fleet that the rest of the agentic-systems neighbourhood ships. The previous dossier flattened it to "single-piece-flow" which loses the voluntary-yield part entirely.

The transmutation pipeline (`buwtaqa` → `istikhlas` → `talaum` → `istihal` → `fasl` → `naqsh`) is also more developed than I expected. It is six named MCP tools plus a layered variant (`mun_istihal_mutabaqq`), implemented in `src/kimiya/istihal.ts`, with conflict detection and explicit error types (`conflicts`, `checkout_failed`, `restore_failed`, `push_failed`, `merge_failed`). The pipeline is the project's design contribution to the messy-to-clean refactor problem, and it is worth a launch artifact of its own. It is currently buried in the prompts and the MCP tool surface.

The README's poem-then-install register is a deliberate aesthetic choice that the maintainer has refined across many commits. The CLI commands listed in the poem (`iksir divine`, `iksir transmute`, `iksir rekindle`, `iksir verify`) do not match the implemented CLI (`init`, `start`, `stop`, `restart`, `status`, `update`, `check`, `sync`, `config`). This is documentation drift, not aesthetic preference; the implemented CLI is named in plain technical register. Either the implementation should follow the poem (rename `start` to `kindle`, `update` to `transmute`, `restart` to `rekindle`, `check` to `verify`, `status` to `divine`), or the poem should follow the implementation. The mismatch is the kind of thing a careful first-time visitor catches and uses as a credibility signal against the project. Pick one direction and align.

The other surprise was how fully Linux-only the install path is. The `register_services` function in `install` calls `die "unsupported OS: $OS (only Linux is supported)"` for any non-Linux operating system. The maintainer's other agentic-systems projects (`clwnd` supports both macOS and Linux; `ccft` is macOS-only) sit on different platform pickets. iksir's choice is neither and is not yet documented in the README.
