# opencode-dir

**Tier:** T2 (Live) • **Repo:** https://github.com/adiled/opencode-dir • **License:** MIT • **Stars:** 13 • **Latest release:** v1.0.8 (2026-05-04)

## What it is

opencode-dir is a single-file opencode plugin that adds three slash commands (`/cd`, `/mv`, `/add-dir`) to mutate a running session's working directory and tool-access permissions without restarting opencode. The plugin is plain technical scope. The README does not reach for metaphor and the package keywords are mechanical (`cd`, `mv`, `add-dir`, `directory`, `monorepo`). The maintainer's own one-liner in `package.json` is "Directory operations for opencode sessions" and that is the right frame.

The mechanism is unusual for an opencode plugin. `lib.ts` opens opencode's sqlite database file directly (resolved via the same logic as `packages/opencode/src/storage/db.ts`), updates `session.directory`, `session.project_id`, and `session.permission` in one statement, and optionally rewrites `path.cwd` and `path.root` across every message row inside a transaction. The plugin then injects defaults at three plugin-API surfaces (`tool.execute.before` for `bash`/`glob`/`grep`, `shell.env` for `PWD`, and `experimental.chat.system.transform` to rewrite the system prompt's "Working directory:" line) so the live tool execution honours the new directory immediately. An in-memory `Map<sessionId, Override>` is persisted to disk at `$XDG_DATA_HOME/opencode/opencode-dir-overrides.json` so the override survives plugin reloads. The companion document `OPENCODE_GAP.md` is a structured upstream proposal addressed to opencode maintainers, naming three options (session-scoped `Instance.directory` override, a new `instance.directory` plugin hook, or exposing nested `Instance.provide()`), with file paths and pseudocode, and a "what this would eliminate" section showing the plugin reducing to a single `command.execute.before` hook if the upstream change lands.

The project name does not abbreviate. There is no expansion to recover. The four-letter package name `opencode-dir` reads as "directory" and is the GitHub repo name, the npm package name, and the keyword set.

## Why it matters to the portfolio

opencode-dir is the portfolio's smallest agentic-substrate artifact and the one closest to a working upstream conversation. `clwnd` lives in the opencode <-> Claude-CLI seam and replaces a large editing surface. opencode-dir lives entirely inside the opencode plugin API plus its sqlite file, fixes one specific runtime constraint (`Instance.directory` is set once at startup and immutable from plugins), and pairs the workaround with a written proposal to retire it. The pattern is portable. It is the same maintainer pattern that `iksir` and `clwnd` use at larger scope: ship the workaround, document the gap, invite the upstream fix.

The 13 stars on a niche plugin with no coordinated promotion are also signal. The audience that found the project found it because the pain is acute and the search terms are obvious. Distribution work here is about widening the surface a few well-aimed clicks at a time, not a Show HN spike.

## Audience

The audience is narrow and specific. Three kinds of people will install this and feel it fits them.

The first is the opencode user working across two or more repositories or inside a monorepo who has hit the "session is anchored to its launch directory" wall, opened opencode in repo A, asked the agent to read a file in repo B, and watched the tool fail. Upstream opencode issue #2177 ("Allow explicitly changing working directory") collects 39 comments of this user describing variations on the same workflow: worktrees, frontend/backend monorepos, Terraform subdirectories, submodules, renamed project folders. The plugin's `/cd` and `/mv` commands are written for this user. They show up reading that issue thread, finding the maintainer's comment that names this plugin, and clicking through.

The second is the opencode user who needs a sibling-package read or write inside a monorepo without abandoning the session's working directory. `/add-dir` is for them. It writes an `external_directory` allow rule into the session's `permission` array, surviving across turns. This is the same shape as Claude Code's `--add-dir` flag, and several commenters on issue #2177 explicitly ask for that ergonomics.

The third audience is smaller and more interesting. It is the opencode contributor or plugin author reading `OPENCODE_GAP.md` and recognizing the document as the kind of upstream proposal they would write themselves before opening a PR. This audience does not produce stars; it produces issues, pull requests, and follow-on plugins that copy the pattern. The README does not currently surface this artifact, which is a missed shape for a kind of reader the project would benefit from.

The audience that does not overlap: anyone not on opencode. The plugin is structurally tied to opencode's plugin API, its sqlite schema, and its `Instance.directory` runtime. It does not generalize to Claude Code, aider, cline, or generic agent harnesses. The Claude Code ecosystem is the wrong distribution surface for it.

## Distinguishing claim

Two claims, both defensible from the code.

The first claim is that opencode-dir is the only opencode plugin (verified against the awesome-opencode list and a `gh search repos "opencode plugin"` sweep on 2026-05-09) that mutates opencode's sqlite database from a plugin context to update session state. Other plugins live entirely inside the plugin API surface. opencode-dir treats the database as the truth, writes to it directly, and patches the plugin hooks downstream so live tool execution sees the new directory.

The second claim is the upstream-proposal-attached-to-the-workaround posture. `OPENCODE_GAP.md` is a 99-line document, structured as a proposal to opencode maintainers, with three concrete options (preferred / alternate / flexible), file references into the opencode source, pseudocode for the preferred change, and an explicit list of what the plugin would shed if the upstream change lands. The plugin and the document are designed to be read together. No other opencode plugin in the public list ships an upstream-gap document of this shape. The nearest neighbour pattern in the agentic-tools world is `cc-statusline`'s integration documents, which are smaller and not proposal-shaped.

## Distribution state, current

The repository ships a working install path on paper. v1.0.8 is on npm with provenance attestation (`npm publish --provenance`, signed by `adilshaikh`). The `install` shell script writes `opencode.json` with the plugin entry. `package.json` declares MIT, has the correct `repository` field, lists `opencode` and `opencode-plugin` in keywords, and pins a `peerDependencies` floor of `@opencode-ai/plugin >=1.2.0`. `LICENSE` is at the repo root. The release pipeline (`.github/workflows/publish.yml`) publishes on tag push and creates a GitHub Release with autogenerated notes from the tag annotation or commit log. `scripts/version.sh` bumps and tags. The pre-push hook auto-bumps the patch version and amends the commit. The maintainer has already announced the plugin in upstream opencode issue #2177 in a single, concise comment.

`lib.ts` is well-tested. 54 unit tests pass under `bun test`. The test surface covers `loadOverrides`/`persistOverrides`, `meetsMinVersion`, `createSchema`/`ensureProject`/`updateSession`, `rewriteMessages`, `resolveTarget`, `getSessionInfo`/`getCurrentDirectory`, `appendDirPermission`, and the success and error paths of `execMove` and `execAddDir`. The e2e test (`e2e.test.ts`) launches a real opencode server with isolated XDG dirs and exercises the plugin functions against opencode's actual drizzle-migrated schema. That suite currently fails locally because the server build is breaking on a missing `@opencode-ai/core/util/log` module path on the maintainer's machine; the failure is on the opencode side, not the plugin side, but the e2e suite is not currently green and will need a fresh opencode checkout to re-validate.

Error reporting is wired to a Sentry envelope POST with no SDK dependency, scoped to `opencode-dir` releases. The plugin self-checks the running opencode version against `MIN_OPENCODE_VERSION = "1.4.3"` and toasts the user if too old. The plugin polls npm for newer versions on load and purges opencode's plugin cache when a newer version exists, so a restart picks up the update.

## Distribution state, gaps

The current published v1.0.6, v1.0.7, and v1.0.8 ship an `index.ts` that does not parse. The file references `exec.result` before `let exec: ExecResult` is declared, has an orphan `}).catch(() => {})` block at line 184, and an empty `if (exec.oldDir && exec.newDir) { }` body at line 148. `bun build index.ts --target=node` errors with "Expected ';' but found ')'" on line 184. `bun -e "import('./index.ts')"` fails to import. The lib tests pass because they import from `lib.ts` directly. The e2e test does not exercise the plugin's hook entry point. The publish workflow has no parser check, no build step, and does not run `bun test`. v1.0.6 was tagged at 06:25Z, v1.0.7 at 06:52Z, v1.0.8 at 07:25Z on 2026-05-04, three patch releases in 60 minutes with no intermediate work commits. A user who installs `opencode-dir@latest` from npm right now will get a plugin that throws on load. This is the dominant launch blocker. It supersedes every other gap on this list.

The GitHub repo description on the public sidebar is empty. The repo has no topics. A visitor scrolling the repo sees only the README. The package's npm page has the description from `package.json`. The "Packages" sidebar does not yet appear on the repo home page; that linkage activates when both `package.json` `repository` is correct and a release has been published, which is now the case, so it should appear shortly after the next correct publish.

A draft GitHub release titled `v2.0.0` has been sitting unpublished since 2026-04-14. It was created before the v1.0.0 release shipped. It now misrepresents the project's state to anyone scrolling the Releases page. Either delete it or repurpose it for an actual v2.0.0 milestone.

The README does not embed a demo. For a plugin whose value is "type `/cd` and the session moves," a 6 to 10 second `vhs` or asciinema capture is the single most-converting README addition. It also does not say "why this is a plugin and not upstream," which is the question a visitor evaluating a long-lived dependency will ask first. `OPENCODE_GAP.md` answers that question, but the README does not link to it. A reader who sees only the README is missing the more interesting half of the project.

The plugin is not listed on `awesome-opencode/awesome-opencode`. The PLUGINS section already accepts entries in the existing format. There is no other directory-management plugin in the list, so the entry would be a clean opening rather than an "X among many" listing.

opencode upstream issue #2177 is open and assigned to `thdxr` (sst's main maintainer). PR #8943 (Leka74) implements only `/add-dir` and a collaborator (`rekram1-node`) commented "we will ship it soon just gonna make some tweaks." When that PR lands, opencode-dir's `/add-dir` becomes redundant in core. Issue #2177 implies `/cd` is also under consideration. opencode-dir's most defensible feature is `/mv` (rewriting `path.cwd` and `path.root` across message history), which mutates persisted session state and is unlikely to land in core because of the schema-touching footprint. The launch window for the plugin's discovery is narrowing as upstream catches up. The post-merge story for the plugin is "the JSONL-rewriting `/mv` command and a thinner workaround layer for builds older than the upstream feature." That story exists and is good. It needs to be written before the merge happens, not after.

There is no `adils.me` post on this project. Per `policy.md` §6, the post is the launch's primary artifact. A short post (300 to 500 words) that opens with the plugin's three commands, links to `OPENCODE_GAP.md`, and explains why the plugin is shaped the way it is would do unusual long-tail work for a niche tool. The maintainer's voice in issue threads is dense and technical and would carry the post register.

## Distribution state, aspirational

A short demo capture would do most of the social-thread work. Six to ten seconds, terminal-only, showing `opencode` running, `/cd ../sibling-repo`, the session toast, and one tool call landing in the new directory. asciinema or vhs both work. Embed it in the README above the install snippet. This is the single most-converting README change available to the project.

A second short clip showing `/mv` rewriting message history (a `before` and `after` of the same path string in the session list, taken from the message rows) would seal the differentiation between the plugin and any upstream `/cd` that ships. The history-rewrite is what core opencode is unlikely to add, and the visual makes that legible without the user having to read the source.

A second `adils.me` post specifically on the "plugin as upstream-gap demonstration" pattern would be portable beyond the opencode audience. The pattern (ship the workaround, write the gap doc, invite the upstream fix) is the same one `clwnd` and `iksir` use at larger scope, and a portfolio-level post on that pattern lifts the read-through to those projects.

If opencode lands `/cd` and `/add-dir` upstream, the plugin can either compress to `/mv` plus a thin compatibility shim for older opencode builds, or it can deprecate cleanly with a README pointer to the upstream feature. Both are good outcomes. Plan for them now rather than scramble at merge time.

## Plan

Pre-launch, in order:

1. Fix `index.ts` and ship a v1.0.9 (or v1.1.0 if the maintainer prefers the signal). The current syntax error blocks every other launch beat. Verify with `bun build index.ts --target=node`, `bun -e "import('./index.ts')"`, and one local `opencode` session that exercises `/cd`, `/mv`, and `/add-dir` end-to-end. This is the only blocker that supersedes everything else.
2. Add a parser-and-test gate to `.github/workflows/publish.yml` so a tag push fails before npm publish if `bun build index.ts --target=node` errors or `bun test` fails. The current workflow ships unverified source. This is a 10-line CI change that prevents the v1.0.6-to-v1.0.8 class of bug from ever shipping again.
3. Delete or repurpose the draft `v2.0.0` GitHub release. It has sat unpublished since 2026-04-14 and misrepresents project state on the Releases page.
4. Set the GitHub repo description to a one-liner and add topics. Suggested topics: `opencode`, `opencode-plugin`, `monorepo`, `worktree`, `slash-commands`. Suggested description: "opencode plugin for /cd, /mv, /add-dir. Change a session's working directory and grant access to additional directories without restarting." This is the description a visitor sees on the repo sidebar and on Google.
5. Record a 6 to 10 second `vhs` or asciinema demo of `/cd`. Embed in the README above the install snippet. This is the single most-converting README change available to the project.
6. Add a one-paragraph "why a plugin instead of upstream" framing to the README, with a link to `OPENCODE_GAP.md`. The framing should name what the plugin does today, name the upstream opportunity (`Instance.directory` immutability, with a link to opencode issue #2177), and name what the plugin's narrow long-term scope is (`/mv` and the message-history rewrite, in particular).
7. Close opencode-dir issue #3 (tag v0.1.0). Twenty releases including v1.0.0 through v1.0.8 have shipped since the issue was filed; the request is satisfied many times over. Close opencode-dir issue #5 (package.json repository field). The field is already correct as of `package.json` line 24 to 27, and the integrity of that linkage will surface as soon as a clean publish lands.
8. Write the `adils.me` post. Open with the three commands and one screenshot. One paragraph each on `/cd`, `/mv`, `/add-dir`, and the upstream gap. Close with the install one-liner and a pointer to `OPENCODE_GAP.md`. The post should be short (300 to 500 words). Do not turn it into a manifesto.

Launch beats, in order, on a Tuesday or Wednesday morning US Pacific:

1. Publish the `adils.me` post.
2. PR to `awesome-opencode/awesome-opencode` PLUGINS section. The entry needs a one-line description in 30 words or fewer in plain technical register (per `policy.md` §3 plain-technical setting). Working draft direction: "opencode plugin adding `/cd`, `/mv`, and `/add-dir` slash commands to change a session's working directory and grant access to additional directories without restarting opencode."
3. Update the existing comment on opencode issue #2177 with the post URL and a one-line note that the plugin now ships the three commands the thread is asking for. Do not start a new comment unless the existing one is too short to edit; the maintainer's `rekram1-node` is already in that thread and pinging once is enough. Add a short note that PR #8943 makes the plugin's `/add-dir` redundant in core, the plugin will continue to ship `/cd` and `/mv` regardless, and `OPENCODE_GAP.md` is the architectural request behind both.
4. Drop the post URL into the opencode community surfaces (Discord channel for plugins, the announcement thread on whatever the active community surface is on launch day). Do not lead with the proxy-routing or model-mux framing; the plugin is shaped as a directory utility and the audience there will read it that way.
5. Submit to Console.dev's weekly. The plugin fits "OSS dev tool of the week" with the opencode angle. The pitch should name `/cd`, `/mv`, `/add-dir` and the OPENCODE_GAP document. One paragraph.
6. X post: 30-second screen capture of `/cd` in action, one-line pitch, repo link. No thread. The work speaks. The opencode-shaped audience on X is small enough that a single anchor post does more than a thread.
7. Optional, post-launch: cross-reference from clwnd's eventual launch only if clwnd launches after this one. No ordering inversion.

Deliberate non-launches:

- No Show HN. The audience is too narrow. HN traffic on a niche opencode plugin is mostly not-the-audience and produces noise without conversions.
- No `r/LocalLLaMA`. Audience overlap is poor. That subreddit is about local model running, not opencode plugin plumbing.
- No `awesome-claude-code` PR. Verified absent of opencode references on 2026-05-09. This plugin is not in the Claude Code ecosystem's distribution surface.

Post-launch, first 30 days:

1. The "isn't this going to be in core" question will arrive within the first hour. The honest answer is in the README's plugin-vs-upstream framing (added per pre-launch step 6). Have the four-line plain version saved for thread responses.
2. Watch upstream PR #8943 (`/add-dir`) and any new PRs against opencode issue #2177. If `/add-dir` lands, post a comment on the plugin's issue tracker and the README naming the deprecation path: `/add-dir` becomes a thin shim; users on opencode versions older than the merge get the plugin, users on newer versions get the upstream feature.
3. The OPENCODE_GAP.md document will draw long-tail attention from plugin authors. Triage the issues and PRs that land against it as architectural conversations rather than feature requests. They are not the same shape.
4. The 13 stars are organic baseline. If the launch lands well, the floor is 25 added stars in 30 days; the target is 100; the stretch is 200. These numbers are scaled down from the standard T2 floors because the audience is genuinely narrower than `clwnd`'s or `ccft`'s, not because the launch is smaller in ambition.

## What we are not doing

Not pitching opencode-dir as a "session manager," "workspace manager," or "project switcher." The category leaders in those names (VS Code workspaces, Cursor session memory, Claude Code's `--add-dir`) carry expectations the plugin does not meet. The plugin is three directory commands. Naming it more than that imports a category that pulls the launch toward the wrong audience.

Not pitching opencode-dir as an opencode database tool or a sqlite mutator. The database write path is real but it is plumbing, not the pitch. Leading with it lands the launch among the platform-engineering audience that is not the audience for a directory plugin.

Not bundling more commands into the plugin to grow the surface for the launch. The tight scope is a feature. Each new command increases the upstream-collision risk and dilutes the per-command demo. Resist the urge to bundle.

Not co-launching with `clwnd`. Both projects sit in the opencode ecosystem and a co-launch dilutes attention on each. opencode-dir is the lower-stakes, lower-risk launch that warms the opencode surface; `clwnd` is the heavier follow-up. The ordering is opencode-dir first, then `clwnd`.

Not flattening the plain technical register into marketing copy. The README is plain on purpose. The plugin's audience reads documentation that looks like documentation. The voice that lands a directory plugin is "this is the function, this is the argument, here is what happens next," not "supercharge your opencode workflow."

## Metrics

Per `metrics.md` T2 per-launch floor / target / stretch, scaled for the narrower audience.

| Metric | Floor | Target | Stretch |
|---|---|---|---|
| Stars added (30d) | 15 | 50 | 200 |
| Unique installers (npm downloads, 30d) | 50 | 250 | 1k |
| External issues (30d) | 1 | 3 | 8 |
| awesome-opencode inclusion | yes | yes | n/a |

Project-specific signals to watch.

Click-through from opencode issue #2177 to the repo. If the comment update converts a meaningful share of the 39-thread audience to installs, the issue thread is doing more than the README. Track inbound by referrer.

opencode-dir mentions in upstream PR conversations. If `OPENCODE_GAP.md` is cited in a PR description or review comment on opencode core, the document is doing portfolio-level work. That is the highest-value mention type the plugin can attract.

Issue-to-star ratio. opencode-dir is technical enough that any external issue is engagement, not noise. A single-digit issue count over 30 days against the star floor is healthy if the issues are install-path or behaviour reports rather than "does it work with X provider" thread fallout.

## Launches

None executed yet. The pre-launch checklist above is the gate. The v1.0.6-to-v1.0.8 syntax error is the dominant blocker; the rest is sequencing.

## Notes

The previous dossier was written under three priors that the current state contradicts. It claimed "Not on npm" and listed npm publish as optional; the package has been on npm since v0.2.x and v1.0.0 through v1.0.8 are published with provenance attestation. It claimed "No tagged GitHub Releases"; twenty tagged releases ship through `.github/workflows/publish.yml` on every tag push. It listed open issues #2, #3, #4, #5 as the active distribution work; #3 (tag v0.1.0) and #5 (package.json repository field) are stale and should be closed. It did not flag the index.ts syntax error in v1.0.6 through v1.0.8, because the error postdates the previous dossier by a day and the previous dossier did not run the parser. This revision replaces those frames.

The biggest correction from the prior pass is the actual launch state. The previous dossier framed opencode-dir as launch-ready pending five pre-launch tasks. The current published artifact does not load. The pre-launch checklist starts with "fix the published plugin" rather than "record a demo." Until v1.0.9 (or whatever number is chosen) ships with a working `index.ts` and a CI gate that prevents recurrence, every other distribution beat is wasted.

The OPENCODE_GAP.md document is the most underused asset on the project. It is the document that converts a casual visitor into a plugin author or contributor. The README does not link to it. Adding the link is a one-line README change.

The maintainer has already announced opencode-dir on opencode issue #2177 in a single one-line comment. That comment is the highest-converting channel the project has, because it lands in front of 39 commenters who are actively asking for exactly what the plugin ships. Updating that comment with a post URL and the plugin-vs-upstream framing is more reach per minute than any new comment elsewhere.

The pre-push hook auto-bumps the patch version on every push to main and amends the commit. This is fine when each commit is a real change, and dangerous when the commits are version-bump commits without intermediate work, which is what produced the v1.0.6 to v1.0.8 cluster on 2026-05-04. Consider gating the auto-bump on a non-trivial diff (i.e., skip the bump if the diff is package.json-only or empty), or move version bumping to an explicit `scripts/version.sh` invocation only and remove the auto-bump path entirely.

## Research log

Sources consulted, in order:

- Local clone at `/Users/adil/opencode-dir`. `package.json`, `README.md`, `LICENSE`, `OPENCODE_GAP.md`, `bun.lock`, `install`, `e2e-patch.cjs`, `e2e-patch.js`, `scripts/version.sh`, `.githooks/pre-push`, `.github/workflows/publish.yml`.
- Source code: `index.ts`, `lib.ts`, `lib.test.ts`, `lib.version.test.ts`, `e2e.test.ts`. The lib tests pass (54 pass, 0 fail). The e2e test fails locally on a missing `@opencode-ai/core/util/log` module path on the maintainer's checkout of opencode.
- Parser checks: `bun build index.ts --target=node` against tags v1.0.0, v1.0.4, v1.0.5, v1.0.6, v1.0.7, v1.0.8. v1.0.6 through v1.0.8 fail to parse. The break landed in the v1.0.6 commit.
- Git history: 69 total commits across 2026 work. Last 50 read in detail. Attention to commits `b24f4d9` (remove accidental test-server-probe), `4182159` (self-update check with cache purge), `5b49218` (add /add-dir), `1acd42d` (rewrite system prompt Working directory), `9a179a8` (Sentry envelope), `20988a0` (v1.0.6, the broken bump), `5260617` (v1.0.7), `999d0a0` (v1.0.8).
- GitHub issues on `adiled/opencode-dir`: #1 (closed, real bug from 2026-04-14), #2 (open, README "why a plugin" framing, valid), #3 (open, tag v0.1.0, stale), #4 (open, demo recording, valid), #5 (open, package.json repository field, stale).
- Upstream opencode issues and PRs: `anomalyco/opencode#2177` (open, 39 comments, assigned `thdxr`, the canonical "/cd" feature request), `anomalyco/opencode#6697` (closed, the architectural bug behind OPENCODE_GAP.md), `anomalyco/opencode#8943` (open, upstream `/add-dir` PR by Leka74).
- npm registry: `https://registry.npmjs.org/opencode-dir/latest` confirms v1.0.8 published with provenance attestation by user `adilshaikh`.
- GitHub repo metadata via `gh repo view --json`: empty description, no topics, MIT license, 13 stars, 1 fork, latest release v1.0.8 published 2026-05-04T07:34:25Z.
- `awesome-opencode/awesome-opencode/README.md` fetched via `gh api`: opencode-dir not listed. No directory-management plugin in the list. PLUGINS section accepts new entries in `<details><summary>Name <stars> - <one-line></summary><blockquote>longer-paragraph</blockquote></details>` format.
- `gh search repos "opencode plugin"` (top 20) on 2026-05-09: no other directory-management plugin in the public ecosystem.
- Web search for "opencode plugin directory cd switch session monorepo 2026" surfaced opencode docs, the upstream issues #2177 and #6697, and several adjacent plugins. opencode.cafe was reviewed and found to currently list zero extensions; future channel rather than launch channel.
- `awesome-claude-code` README via `gh api`: no opencode references. Confirmed not the right channel for this plugin.
- Existing dossier at `projects/opencode-dir.md`.
- `policy.md`, `channels.md`, `metrics.md`, `projects/_index.md`, `projects/ccft.md`, `projects/clwnd.md` (dossier shape and rigor reference).

Sources rejected as imported-category framings:

- "Workspace manager" or "project switcher" frame. The plugin is three directory commands writing to opencode's session table. Workspace-manager language imports VS-Code-workspace expectations the plugin does not meet (no project list, no per-workspace settings, no UI shell).
- "Session manager" frame. The plugin mutates a single session's directory; it does not manage sessions in any broader sense. Adopting the frame would route the launch toward audiences expecting tools like tmuxinator, asdf, or direnv-style multi-session shell management.
- "Database tool" or "opencode internals" frame. The sqlite write path is real but it is plumbing. Leading with it lands the launch among platform-engineering audiences instead of the opencode-user audience the plugin actually serves.
- "Agent observability" or "agent telemetry" frame. The Sentry envelope is error reporting, not telemetry. The plugin is not OpenTelemetry-shaped, does not export metrics, and is not designed for org-scale observation. Adopting the frame would mis-route the launch.
- "MCP gateway" or "MCP plugin" frame. The plugin does not expose an MCP server, does not register MCP tools, and does not interoperate with the MCP registry. Category fit was never plausible from the code.

What surprised me in step 4, when writing the project's-own-voice description.

The OPENCODE_GAP.md document is the most distinctive artifact in the repository, and the README does not link to it. The document is structured as a real upstream proposal: three options labeled A/B/C, file paths into opencode's source tree at specific line numbers, pseudocode for the preferred change, and a "what this would eliminate" section that names exactly which plugin hooks would retire if upstream lands the change. It is the document a contributor reads and recognizes as the kind of work they would write before opening a PR. The previous dossier framed opencode-dir as a "tightly-scoped, single-purpose, well-named" plugin and treated it as a discoverability play. That framing is correct as far as it goes but it misses the more interesting half. opencode-dir is also a working argument for an upstream feature, with the workaround code as the evidence that the feature is needed. The maintainer has already argued the case in the upstream issue thread. The project is more conversational with opencode core than the prior dossier credited.

The other surprise was the v1.0.8 syntax error. The published artifact does not load. v1.0.6, v1.0.7, and v1.0.8 all ship `index.ts` with a hard parse failure. The publish workflow has no parser check. The pre-push hook auto-bumps the patch version on every push to main, which is what produced three patch releases in 60 minutes on 2026-05-04 with no intermediate work commits. The previous dossier listed npm publish as an "optional" pre-launch step; the package has actually been on npm since v0.2.x and is currently broken on npm. This is the largest single correction in this revision.

The third surprise was upstream PR #8943. The opencode core team has an open PR for `/add-dir` and a collaborator has said it will land soon. The launch window for opencode-dir's `/add-dir` is narrowing. The plugin's most defensible long-term feature is `/mv` (the JSONL-rewriting variant), which mutates persisted message history and is unlikely to enter core. The launch should foreground `/mv` and OPENCODE_GAP.md, not `/add-dir`, because that is what survives the upstream merge.
