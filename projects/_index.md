# Active Projects: Roster

Live state of every public OSS project under `@adiled` that is in scope for distribution. Update on every status change. **Tier definitions live in [`policy.md`](../policy.md) §1.**

Last refreshed: 2026-05-09

| Project | Tier | Lang | Stars | Last push | Status | Dossier |
|---|---|---|---|---|---|---|
| [`ohlc-resample`](https://github.com/adiled/ohlc-resample) | T1 | TS | 26 | 2026-05-09 | Library plus CLI for resampling OHLCV candles between time frames. Live on npm at 1.2.1; v2.0.0 staged on `feat/cli-improvements`. | [→](./ohlc-resample.md) |
| [`opencode-dir`](https://github.com/adiled/opencode-dir) | T2 | TS | 13 | 2026-05-04 | Single-file opencode plugin adding `/cd`, `/mv`, `/add-dir` by writing to opencode's sqlite session state. Paired with OPENCODE_GAP.md upstream proposal. v1.0.6 to v1.0.8 ship a syntax error; fix-and-republish is the dominant launch blocker. | [→](./opencode-dir.md) |
| [`clwnd`](https://github.com/adiled/clwnd) | T2 | TS | 9 | 2026-05-05 | OpenCode sidecar that delegates model calls to Claude Code CLI and replaces the file/edit toolset with AST-grounded tools, JSONL grafting, and a context-loss watcher. v0.24.19 shipped. | [→](./clwnd.md) |
| [`ccft`](https://github.com/adiled/ccft) | T2 | Rust | 0 | 2026-05-08 | Claude Code companion that scores bot/driver vibe over time. v1.5.0 release-ready. crates.io and Homebrew remaining. | [→](./ccft.md) |
| [`rishta-lang`](https://github.com/adiled/rishta-lang) | T3 | Python | 0 | 2026-04-15 | Programming language whose runtime substrate is رشتہ (kinship). Kinship-typed lineage edges, cascading consent revocation, Merkle-chained گواہی testimony. | [→](./rishta-lang.md) |
| [`iksir`](https://github.com/adiled/iksir) | T3 | TS (Deno) | 0 | 2026-03-30 | Linux daemon that runs OpenCode sessions on Linear tickets one at a time. Operator routed in over Telegram only when the question assay marks it worth answering. | [→](./iksir.md) |
| [`orch`](https://github.com/adiled/orch) / [`orchd`](https://github.com/adiled/orchd) | T3 | Rust | 0 | 2026-03-07 | Containerfile-shaped Orchfile spec plus parser library (orch) and the engine that compiles Orchfiles to native systemd units (orchd). bare runtime and systemd platform shipping. crate name `orch` is taken on crates.io; publish blocked on a rename to `orchfile`. | [→](./orch.md) |
| [`khowarpedia`](https://github.com/adiled/khowarpedia) | T5 | JS | 0 | 2026-03-29 | Browser-native Khowar lookup served from adils.me/khowar. ~8,200 entries from the rachitrali corpus, rendered in Awami Nastaliq. | [→](./khowarpedia.md) |
| [`clwnd-playground`](https://github.com/adiled/clwnd-playground) | T4 | Python | 0 | 2026-04-26 | Public scratch directory of one clwnd-driven session, single commit, unedited. Demonstration of clwnd in action, not a project. | [→](./clwnd-playground.md) |

## Projects deliberately out of distribution scope

| Project | Why |
|---|---|
| `awesome-opencode` (fork) | Companion curation list for the opencode ecosystem. Useful as a *destination* for `opencode-dir` and `clwnd` entries. Not promoted as a standalone `@adiled` project. |
| `claude-code` (fork) | Educational fork. No standalone distribution. |
| `opencode` (fork) | Upstream of the actual product. No standalone distribution. |
| `awesome-dev-task`, `actor-demo`, etc. (private) | Out of scope for public distribution. |
| Older portfolio (`viteer`, `web3-elements`, `skale-js`, `web3-vue`, `openapi-automate`, `adils.me`) | T5. No active distribution. Track only if a strategic reason to revive emerges. |

## Read order for new context

If you're picking this up cold:

1. [`../README.md`](../README.md). What this repo is.
2. [`../policy.md`](../policy.md). The constraints.
3. This file (`_index.md`). What's in flight.
4. The dossier of whichever project is the current focus.
5. [`../asks.json`](../asks.json). What's blocked on the human (registry of curated GitHub issues, schema in [`../asks.schema.json`](../asks.schema.json)).
