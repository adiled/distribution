# clwnd-playground

**Tier:** T4 (Companion) • **Repo:** https://github.com/adiled/clwnd-playground • **License:** *not declared* (issue #1 open and accurate) • **Stars:** 0 • **Last push:** 2026-04-26

## What it is

clwnd-playground is the public scratch directory of one clwnd-driven agentic session. Every file in the repo, sixty-five of them, arrived in a single commit on 2026-04-26, authored by `clwnd <clwnd@test>` and titled `🚀 Initial commit: Animal Arena, Space Mission & Zoo`. The repo has not been touched since. Reading it is reading the working surface of one OpenCode session as it was driven through Claude Code via the clwnd daemon, with zero curation after the fact.

The contents fall into three rough zones, only one of which the README actually advertises.

The advertised zone is the zoo: ten or so creature classes (`wolf.py`, `octopus.py`, `pangolin.py`, `axolotl.py`, etc.), an interactive arena tournament (`arena.py` plus a non-interactive `arena_demo.py` seeded for reproducible chaos), a much larger `space_mission.py` (329 lines, animal crew, ship state, event table, planet roster), and `chaos.py` (397 lines, an XP-and-coins game where the level-up rewards reference repository content the agent expected to find). These are the parts the README puts in a creature table with emoji.

The unadvertised zone is more interesting. `mcp-bench.ts` is a real benchmark for clwnd's MCP tools that imports `executeTool` from `/root/clwnd/mcp/tools.ts` and times in-process calls against round-trips through the daemon's Unix socket at `clwnd.sock.http`. It measures wall-clock latency, payload bytes, and an explicit "tax" column for the HTTP framing cost. `config.json` is an actual opencode config with a provider entry named `opencode-clwnd` pointing at the plugin path under `$XDG_DATA_HOME/clwnd/src/plugins/opencode/dist/index.js`, listing twenty-plus Claude model variants. `drive.sh` POSTs prompts asynchronously to `127.0.0.1:14568/session/$SID/prompt_async` with that same `opencode-clwnd` providerID, then polls `/session/$SID/message` until the assistant turn finishes. These three files are not zoo content. They are the operational rigging the agent was building or running clwnd through.

The third zone is residue: dozens of single-line markdown files (`o.md` says "hello world", `wassup.md` says "wasup.com", `kapita.md` says "kitanu", `sonya.md` says "ai"), short text files testing specific cases (`hello.txt`, `jello.txt`, `mello.txt`, `challo.txt` all contain "testing permissions"), animal stubs that exist only to print one chaos number (`narwhal.py`, `lemur.py`, `gecko.py`, `flamingo.py`, `sloth.py`), and conventional Python that doesn't fit the zoo theme at all (`code.py` is a 224-line CLI task manager, `winning.py` is a 176-line win-tracker with celebrations like "Built different.", `thunk.md` is a 73-line essay on React Hooks vs Vue 3 Composition API). The residue is the most legible record of what the session was actually doing: exercising clwnd's `do_code`, `do_noncode`, `read`, and permission tools across plain text, code, JSON, and Markdown.

The README closes with `*Built with vibes by clwnd* 🔥` and that line is the truest description of the artifact.

## Why this is T4

Companion role to clwnd, confirmed. The repo is not a project. It is one clwnd session's public exhaust. The demonstration value is exactly that: a visitor who has read the clwnd README and wants to see what one session of clwnd actually produces can click through here and read every file the session wrote. The whimsy and the residue and the operational rigging together are the demonstration, not the zoo.

T4 framing holds, with one correction. The prior dossier said the playground would be "linked from clwnd's README as 'see what running this looks like.'" That link does not exist. `grep` across `~/clwnd/README.md`, `HUMAN2HUMAN.md`, `NOTICE`, and the entire clwnd source tree returns zero references to clwnd-playground. The companion relationship is implied by the org structure and the playground's own README footer, but it is not currently linked anywhere a clwnd visitor would find it.

## Distribution role, current

Standalone discovery is zero by design. The repo description on GitHub is empty, the topics list is empty, the license field is null, and the last push is 2026-04-26. None of that is a problem for a T4. It is, in fact, the right posture: nothing here is asking for traffic on its own.

The companion link from clwnd to clwnd-playground does not yet exist. If the playground is to do its job of showing clwnd visitors what one session looks like, the link has to live somewhere clwnd's launch-day visitors will scan. Two natural places: a small "what running this looks like" section near the bottom of clwnd's README, and a sentence in the `adils.me` clwnd thesis post that links the playground as a worked example.

The `Built with vibes by clwnd` footer in the playground README is the only attribution either side currently carries. That is enough register, but it could be one sentence sharper. Suggested addition under the footer: "every file in this repo was written by an agentic session running through clwnd. nothing was hand-edited after the fact." That tells the visitor what they are looking at without dressing it up.

## Distribution role, gaps

License is the only real ask. Issue #1 is open, accurate, and not stale: `gh repo view adiled/clwnd-playground --json licenseInfo` returns `null`. Two reasonable resolutions, depending on the maintainer's read.

MIT, the portfolio default for an end-user artifact under `policy.md` §4. This treats the playground content as ordinary code that someone might lift a snippet from, and removes the friction.

Or a five-line "no license, this is a session artifact, take it as-is" note in the README in the playground's own register. This treats the contents as observation rather than reusable code, which is closer to what the repo actually is, and matches the "Built with vibes by clwnd" footer voice.

The maintainer's call. Either resolves issue #1.

A second small ask, not blocking: an `attribution` paragraph in the README, two or three sentences, naming what the visitor is looking at (one session's output, single commit, no curation, here for clwnd demonstration). The current README's creature table is fine for the zoo zone; the attribution paragraph would name the meta-artifact the visitor actually arrived to see.

## Distribution role, aspirational

Not a launch surface. Not an awesome-list candidate. Not a Show HN. None of those would land for the right reason, and a stranger arriving at the playground without clwnd context would be confused, which is the failure mode the prior dossier already named correctly.

If the playground's role expands at all, the most plausible direction is a second commit that adds one file: a brief `SESSION.md` or similar, naming the session that produced everything else (when, what model, what prompt, how many turns, peak `clwnd savings` readout from the session). That makes the artifact self-documenting for visitors who arrive without clwnd's README open in another tab, and it keeps the demonstration property intact. No need to clean the residue. The residue is the demonstration.

The chaos.py game references files that do not exist in this repo (a `repro/` folder, an `8.6MB clwnd-plugin.log`, `.claude.json`, `poooop.md`). That residue from the original session is itself part of the artifact's honesty. Not a defect to fix.

## What this is not

Not a Python tutorial. The conventional `code.py` task manager and `winning.py` tracker are not the point of the repo and treating them as teaching examples imports the wrong category. They are content the session happened to produce.

Not an MCP benchmarking project. `mcp-bench.ts` is a real benchmark for clwnd's tool surface, but it lives here as an artifact of one session, not as a maintained benchmark. If the benchmark itself becomes a thing the project leans on, it belongs back inside the clwnd repo where the tool definitions live. Marketing it from the playground would invert the relationship.

Not an opencode plugin example. `config.json` and `drive.sh` are operational, not pedagogical. They show one session's actual configuration; they are not examples for a new user to copy. A new user copying `drive.sh` would be POSTing to a port the session was using and a session id the session owned.

Not a creature-classes library. The zoo is the README's lede, not the repo's substance. Distribution copy that picks up the creature framing as the project's identity flattens the artifact's actual claim, which is "this is what one clwnd-driven session produces, exhibited unedited."

Not promoted on social. The companion-only stance from the prior dossier holds.

## Plan

There is no launch plan. The playground does not launch.

The two small follow-ups are both inside other artifacts.

First, in clwnd's README revision, near the bottom, a single sentence: "for a worked example of one clwnd-driven session's output, see [clwnd-playground](https://github.com/adiled/clwnd-playground)." This is the link that currently does not exist and that lets the playground do the job the prior dossier already assigned it.

Second, in the `adils.me` clwnd thesis post (still pending; tracked in the clwnd dossier), one sentence with the same link, framed around the demonstration property rather than the zoo: "everything here was written by an agentic session through clwnd, in one shot, unedited."

In the playground itself, two minor edits when the maintainer has five minutes. License decision (MIT or in-README disclaimer, closes #1). One attribution paragraph above or below the creature table. Nothing else.

## Metrics

Not measured. Per `metrics.md` §T4, health is the parent project's health. If the link from clwnd's README ever lands, the only signal worth watching is whether visitors who came from clwnd's README open more than one file before leaving. Even that is incidental.

The repo's own counters (zero stars, zero forks, zero issues besides the LICENSE one, zero topics) are correct for what it is. Movement on any of them would be noise unless it traced back to a clwnd referrer.

## Notes

The single commit by `clwnd <clwnd@test>` on 2026-04-26 is the artifact's most legible fact. Whatever framing the dossier reaches for, the underlying truth is that this is one session's output, not a project. The dossier should keep that fact in front, because every other framing eventually drifts toward "small Python project" or "demo repo," both of which are wrong.

The `mcp-bench.ts`, `config.json`, and `drive.sh` files together are the technical confirmation that the session was real and was driving clwnd as a provider. A reader who already knows clwnd recognizes them immediately. A reader who does not know clwnd reads the zoo and the chaos game and wonders why a Python playground has a TypeScript MCP benchmark in it. That contrast is part of the demonstration; the operational rigging being mixed in with the zoo is what makes the artifact read as one session's surface, not a curated showcase.

Issue #1 (LICENSE) is real and current. It is not stale. The asks.json entry for `adiled/clwnd-playground#1` remains correct as written.

The prior dossier's framing was directionally right but understated. It described the playground as "chaotic Python," which captured the zoo zone but missed the operational zone (`mcp-bench.ts`, `config.json`, `drive.sh`) and the residue zone (single-line markdown, conventional Python, the React/Vue essay). The chaos is the point, but the chaos has texture, and the texture is what makes the artifact useful as a clwnd demonstration. This dossier names all three zones.

The "linked from clwnd's README" claim in the prior dossier was aspirational, not actual. The link does not exist yet. It belongs in the clwnd README revision pass, not in the playground itself.

## Research log

Sources consulted, in order:

- Local clone at `~/clwnd-playground` after `gh repo clone adiled/clwnd-playground`. Full directory listing, `.gitignore`, `README.md`, every `.py`, every `.md`, every `.txt`, `config.json`, `drive.sh`, `mcp-bench.ts`, `thoughtconfig`, `example.html`.
- Source code spot-reads with attention to texture: `arena.py`, `arena_demo.py`, `space_mission.py`, `chaos.py`, `chaos_generator.py`, `code.py`, `winning.py`, `mcp-bench.ts`, `drive.sh`, `config.json`, `tui.py`, `bird_feeder.py`, plus the full set of creature stubs and residue files to confirm the three-zone reading.
- Git history: `git log` returns one commit (`eb0807e`, 2026-04-26, author `clwnd <clwnd@test>`, 65 files added). No subsequent activity. The single-commit fact is structural to the artifact.
- GitHub issues: `gh issue list --state all` returns one open issue, #1 (Add LICENSE), filed 2026-05-09 by the distribution playbook itself. Verified against `gh repo view adiled/clwnd-playground --json licenseInfo` which returns `null`. Issue is current, not stale.
- Repo metadata: `gh repo view` confirms empty description, no topics, no license, last push 2026-04-26, zero stars, zero forks.
- Cross-check against `~/clwnd`: `grep -ri "clwnd-playground" ~/clwnd/` returns zero results, and `grep -i playground ~/clwnd/README.md` returns zero results. The companion link the prior dossier described does not currently exist.
- Existing dossier at `projects/clwnd-playground.md` (T4, prior version) and the freshly-revised `projects/clwnd.md` for companion context.
- `policy.md` §1 (T4 definition), §3 (voice settings), §4 (license defaults), `metrics.md` §T4, `channels.md` `clwnd-playground` row, `asks.json` entry for `adiled/clwnd-playground#1`.
- `WebSearch` for "agentic coding session demo repository playground claude code opencode 2026". Confirmed there is no recognized market category called "agent session demo repo" or similar. Imported categories rejected: "python tutorial," "demo repo," "opencode plugin example," "MCP benchmarking project."

What surprised me in step 4, when writing the project's-own-voice description.

The repo is more layered than the README suggests. The README sells the zoo. The zoo is real but it is one of three zones, and it is the least diagnostic one. The operational zone (`mcp-bench.ts`, `config.json`, `drive.sh`) is the one that confirms the artifact's claim about what produced it; the residue zone (single-line markdown, the React/Vue essay, the task manager, the win tracker, the bone-dry creature stubs) is the one that confirms the session was real and unedited rather than curated. A dossier that summarizes only the README's creature table would lose what is interesting about the artifact.

The other surprise was how disciplined the repo's posture already is for a T4. Empty GitHub description, no topics, no stars, no fork attempts, no advertising. The single commit. The `clwnd <clwnd@test>` author. The footer line `Built with vibes by clwnd`. None of these were edits done after the fact for distribution; they are what the session left behind. That is the right shape for a companion repo. It would be possible to over-curate this artifact into something less interesting, and the prior dossier's "do not sanitize, the chaos is the point" instruction reads correctly under inspection. This dossier sharpens the framing without changing that instruction.
