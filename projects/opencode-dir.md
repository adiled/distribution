# opencode-dir

**Tier:** T2 (Live) • **Repo:** https://github.com/adiled/opencode-dir • **License:** MIT • **Stars:** 13

## What it is

An opencode plugin that adds three commands (`/cd`, `/mv`, `/add-dir`) to manage directory context inside a session without restarting. Solves the "session is stuck in the directory it was started in" pain, especially across monorepos and multi-repo work. Tightly scoped, single-purpose, well-named.

## Why it matters to the portfolio

This is the **highest signal-to-noise project in the portfolio**. 13 stars on a niche opencode plugin with zero coordinated promotion is meaningful organic pull. It validates that there's an audience for tightly-scoped opencode tooling under `@adiled`, which underwrites the broader bet on `clwnd`, `iksir`, and other plugin-shaped work.

Distribution-wise, this is a **lower-risk, lower-stakes T2** than `clwnd`. It can be launched first, in a smaller way, to warm up the opencode community before the heavier `clwnd` push.

## Audience

- opencode users working across multiple repos and monorepos.
- Anyone who has typed "the session is stuck in the wrong dir" once and wants the fix.

The audience is *narrower* than `clwnd`'s but *more concentrated*. Every opencode user has felt this pain.

## Distribution state: what's done

- ✅ Working `curl | bash` install script.
- ✅ MIT license (already declared).
- ✅ Concise, well-structured README.
- ✅ Each command has its own short, scannable docs section.
- ✅ Manual install path documented.
- ✅ "After moving" section anticipates the user's next confused thought. Good UX writing.

## Distribution state: gaps

- ❌ Not on npm. Plugin distribution via bash install is fine but not standard.
- ❌ No demo content (no gif, no asciinema). For a plugin whose value is "type `/cd` and the session moves," a 10-second demo is *worth more than the README*.
- ❌ Not on `awesome-opencode` (your fork, easy add).
- ❌ No tagged GitHub Releases.
- ❌ The README does not explain *why* this is a plugin instead of upstream. Useful framing for visitors evaluating whether the project will survive opencode's own evolution.
- ❌ No landing page (probably overkill for the scope, ignore for now).

## Plan: coordinated mini-launch (T2, lighter)

**Pre-launch (must all be green):**

1. **Demo recording.** A 10-second `vhs` or asciinema showing `/cd` in action. Embed in README above install.
2. **Tagged release.** `v0.1.0` on GitHub Releases with release notes.
3. **npm publish (optional).** Adds discoverability via `npm search opencode plugin` and dep crawlers. Low effort given the scope.
4. **`awesome-opencode` PR.** Self-merge if the fork is yours, otherwise PR upstream.
5. **README addendum.** One-paragraph "why a plugin instead of upstream" framing.

**Launch beats (light, sequenced):**

1. **Week N.** Post in opencode community channels (Discord, X, wherever opencode users coalesce).
2. **Week N.** X post: 30-second demo, 1-line pitch, repo link. No thread, the work speaks.
3. **Week N+1.** Submission to `Console.dev` weekly. Direct fit (OSS dev tool of the week, opencode angle).
4. *(optional)* Cross-reference from `clwnd`'s eventual launch, but only if `clwnd` is launched after this one. No ordering inversion.

**Deliberate non-launches:**
- **No Show HN.** Audience is too narrow. HN traffic on a niche plugin is mostly not-the-audience and produces noise without conversions.
- **No `/r/LocalLLaMA`.** Audience overlap is poor. That subreddit is more about local model running than agent-tool plumbing.

## What we are *not* doing

- **Not adding more commands to the plugin** for the sake of distribution. The tight scope is a *feature*. Resist the urge to bundle.
- **Not co-launching with `clwnd`.** See `clwnd.md` "Notes." Staggered launches preserve attention.

## Metrics

T2 per-launch metrics from `metrics.md` apply, but with relaxed expectations given the narrower scope:

| Metric | Floor | Target | Stretch |
|---|---|---|---|
| Stars added (30d) | 15 | 50 | 200 |
| External issues (30d) | 1 | 3 | 8 |
| `awesome-opencode` inclusion | yes | yes | n/a |

## Launches

*(None yet.)*

## Notes

If `opencode-dir` continues to outpace expectations on stars-per-week, that is signal that **building more small, well-named opencode plugins is high-yield distribution work**. Entertain a short series of follow-on plugins (`opencode-X`, `opencode-Y`), each shipped on the same template. The "many small plugins under one author" pattern is a real distribution strategy unto itself.
