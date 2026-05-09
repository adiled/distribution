# orch / orchd

**Tier:** T3 (Pre-launch) • **Repos:** [`orch`](https://github.com/adiled/orch) (parser and spec library) and [`orchd`](https://github.com/adiled/orchd) (execution engine) • **Stars:** 0 + 0

**orch:** Apache-2.0 file at repo root, no `license` field in `Cargo.toml`, README declares "Apache 2.0". Latest local tag `v0.2.0` (2026-03-07), no GitHub release published, last push 2026-03-07.

**orchd:** Apache-2.0 file at repo root, no `license` field in `Cargo.toml`, README declares "Apache 2.0". 13 commits total (`e684aab` initial docs, `f7c5d86` README plus LICENSE), no GitHub release, no tag, last push 2026-03-07. Repo description on GitHub: "Orch engine". No repository topics set. 75 unit tests plus one ignored integration test that exercises the full `orch parse` to `bare` runtime to `systemd` generate pipeline against `tests/fixtures/Orchfile`.

## What they are

This dossier covers two repos that ship as a single toolchain. `orch` is the upstream half and is described in its own register first. `orchd` is the downstream consumer.

### orch (parser and spec library)

orch is a Rust crate that parses an Orchfile, validates four named constraints, expands variables, merges multi-file overlays, and emits structured JSON. The crate's `lib.rs` exposes one module (`pub mod types`) so downstream consumers can take a `cargo add` dependency on the crate (under whatever name lands on crates.io; see the gaps section below) and `serde_json::from_str::<OrchFile>` directly off the wire. The crate also ships a binary, `orch`, with two subcommands: `orch parse <files...>` writes JSON to stdout, and `orch validate <files...>` exits 0 or 1.

The Orchfile format is the project's actual contribution. Each line is a directive in uppercase (`SERVICE`, `FROM`, `RUN`, `ENV`, `PUBLISH`, `VOLUME`, `WORKDIR`, `REQUIRES`, `AFTER`, `HEALTHCHECK`, `RESTART`, `ONESHOT`, `RECREATE`, `MEMORY`, `CPUS`, ...). The directive set is Containerfile-shaped, with service-orchestration extensions. The SPEC.md says this in its own words: "Orchfile is inspired by Containerfile's declarative simplicity, not docker-compose's configuration verbosity." The SPEC also names what the file is and is not for: "Local, ephemeral, development, and staging environments. Not for: Production deployments at scale."

Composition is modeled on systemd drop-ins, not on YAML deep-merge. A base Orchfile plus one or more overlays merge left-to-right with explicit semantics: scalars are last-wins, keyed lists (`ENV`, `PUBLISH`, `VOLUME`) merge by key with overlay winning on conflict, positional lists (`REQUIRES`, `AFTER`, `ENV_FILE`) append and dedup, and a `CLEAR <list-directive>` line resets a list before the overlay applies. Mode switching (an overlay `RUN` on a base `FROM`, or vice versa) clears the previous mode's directives. ARG values are merged before variable expansion runs, so an overlay can redefine an ARG that the base file references in `${var}` form.

The pipeline is three named stages: `parse_raw` produces a `RawOrchFile` with unexpanded values and source provenance, `merge` collapses N raw files into one, and `resolve` applies CLI/env overrides to ARGs, expands `${var}` references, parses typed values, and runs the four constraint checks. Constraint C1 is FROM XOR RUN per service. C2 and C3 enforce mode-specific directive sets. C4 is DAG acyclicity over `REQUIRES` plus `AFTER`. REQUIRES targets are also checked against the resolved service set; AFTER targets are not, by spec.

The grammar is formal EBNF in `grammar.ebnf`, regenerated to a 141KB typeset PDF in `docs/grammar.pdf` by a `pre-push` git hook running `pdflatex` (with a TinyTeX fallback path baked in). Test suite is 140 tests across `src/parser/tests.rs` and `src/merge/tests.rs`. Dependencies are `serde` and `serde_json` only. The crate is small (about 800 lines of non-test code), tightly scoped, and reads as a maintainer who already knows where the project ends.

The repo also carries a `SPEC.md` (about 870 lines) that documents every directive with platform-mapping notes per launchd and systemd, plus a worked example Orchfile and a built-in variable list (`${ORCH_PROJECT}`, `${ORCH_DATA}`, `${ORCH_STATE_DIR}`, etc.) that the spec defers to runtime resolution. orch the parser does not resolve the built-ins; orchd does.

### orchd (execution engine)

orchd is a Rust binary that takes orch's JSON, lowers it through a Runtime layer to a Platform layer, writes native service-manager artifacts to disk, symlinks them into the system's service directory, and runs the lifecycle commands (`orchd up/down/restart/status/logs/health/list/clean`). The current ship is `bare` runtime plus `systemd` platform; `containerd`, `podman`, `apple` runtimes and `launchd` platform are accepted as CLI strings and named in the docs but have no implementations behind them. The CLI surface is small and stable: ten subcommands, eleven global flags, configurable via CLI argument, environment variable, `.orchrc` file, or compiled defaults, with the merge order documented in `config.rs` as CLI > env > `.orchrc` > defaults.

The Runtime/Platform separation is the engine's structural choice. A Runtime (`Runtime` trait in `src/runtime/mod.rs`) is a function from a `Service` to an `ExecSet`, where `ExecSet` is four optional bash strings (`pre_start`, `start`, `stop`, `post_stop`). A Platform (`Platform` trait in `src/platform/mod.rs`) consumes `(Service, ExecSet)` and writes a native unit file (today, a systemd `.service`; in the docs' planning, a launchd plist). The Runtime never imports the Platform; the Platform never imports the Runtime; they communicate through `ExecSet` plus the canonical `orch::types::Service`. This is what makes the engine's pluggability claim concrete instead of aspirational, even though only one Runtime and one Platform have shipped: the seam exists, the trait is generic, and `runtime::create_runtime(name, config)` is the single dispatch point that a future containerd or podman implementation slots into.

The systemd generator does work that a hand-written unit file does not. It auto-generates a "ready gate" oneshot service for any service whose healthcheck is referenced by another service's `REQUIRES` or `AFTER`, so that `B After=A-ready.service` blocks until A's healthcheck passes rather than blocking only until A's process is up. It generates a per-namespace `.target` (`orch.target` by default) that groups all managed units, so `orchd up` becomes `systemctl start orch.target` and `orchd down` becomes `systemctl stop orch.target`. It maps Orchfile resource directives onto systemd's `MemoryMax`, `CPUQuota`, `LimitNOFILE`, `TasksMax`, `IOWeight` cgroup controls. It maps `RESTART on-failure / RESTART_DELAY 5s / START_LIMIT_BURST 3 / START_LIMIT_INTERVAL 60s` onto `Restart=on-failure`, `RestartSec=5s`, `StartLimitBurst=3`, `StartLimitIntervalSec=60s`. The unit-file output is deterministic (env keys are sorted before emission) and the generator's tests assert on string content, not on systemd behaviour, so the test suite runs anywhere.

The bare runtime is opinionated: a service in container mode (`FROM`) without a bare-mode overlay is rejected with `RuntimeError::UnsupportedMode { service, mode: "container (FROM <image>). Create a bare overlay that redefines it with RUN, or use a container runtime (--runtime containerd)" }`. The error message is the project's stance that bare is a deliberate position, not a fallback. A user who runs `orchd up` against an Orchfile with `FROM postgres:15` and no overlay sees the message and either writes a bare overlay (`SERVICE postgres / RUN /usr/bin/pg_ctlcluster 15 main start --foreground`) or switches to a container runtime that is not yet shipping. The path through bare is the path the project supports today.

Two engine niceties that are easy to miss in the README. First, the `is_up_to_date` mtime check: `orchd generate` skips work if every generated unit is newer than the Orchfile and all overlays, unless `--force`. The check is in `engine.rs` and walks the units directory. Second, the built-in variable injection: orchd writes a temp overlay at `${state_dir}/tmp/orchd-vars.orch` containing `ARG ORCH_DATA=<resolved> / ARG ORCH_PROJECT=<resolved> / ARG ORCH_STATE_DIR=<resolved> / ARG ORCH_CONTAINERS_DIR=<resolved>` and prepends it to the user's overlay list before invoking `orch parse`. This is what lets an Orchfile reference `${ORCH_DATA}/postgres` without the user passing four `--arg` flags every invocation. Both behaviours are tested.

### Pipeline contract between the two

`Orchfile -> orch parse (JSON) -> orchd Runtime (ExecSet) -> orchd Platform (native artifacts).` The schema-stable seam is the JSON that `orch parse` emits, defined by the `pub` types in `orch::types`. orch is the upstream contract; orchd is one consumer. A different consumer (a launchd generator written in Python, a CI step that emits Nomad jobs, a docs renderer for an Orchfile in a repo) would also depend on orch and ignore orchd entirely. The two-repo split is the project's stance that the spec library should be embeddable without dragging the engine in.

## Why they matter to the portfolio

orch and orchd are the most classically-shaped technical projects in the portfolio. They sit furthest from the agentic-systems thesis (clwnd, ccft, opencode-dir, iksir) and read closer to a Rust systems tool than an agent substrate. That makes them the project most legible to mainstream Rust and self-hosting audiences and the project most useful for diversifying the portfolio's reach. A Rust user who finds orch through `awesome-rust` or `lib.rs` and walks back to the `@adiled` profile sees a maintainer who ships a working parser with formal EBNF, a typeset PDF, 140 tests, and a binary plus library API; that footprint reads as a different signal than the agent-systems projects do, and serves a different filtering pass for the wider audience.

orch specifically (the library half) is also the project where the pitch is least dependent on the maintainer's voice. The README is plain technical, the SPEC is plain technical, the EBNF is the EBNF. Distribution copy can stay in plain technical register (per `policy.md` §3) without flattening anything that should be carried in another register, because there is nothing else to carry. That makes orch a useful counter-balance to the iksir / rishta-lang / clwnd ladder of register-rich projects.

## Audience

### orch (library and spec)

Three kinds of people will pick up the library half and feel it fits them.

The first is the tool author building a service-manager generator who would rather take a `cargo add` dependency on the published orch crate than re-implement Orchfile parsing. The exposed `orch::types::OrchFile` plus serde derives make this real today. Anyone writing a launchd generator, a podman-compose translator, a custom CI runner, or a docs site that renders an Orchfile takes orch and ignores the binary. orchd is one example of this consumer; there is room for others.

The second is the small audience that reads spec implementations the way other people read poetry. Formal EBNF, a 141KB typeset grammar PDF, named constraints C1 through C4, a clean three-stage pipeline (parse_raw, merge, resolve) with a typed intermediate representation between stages: this audience is small and high-engagement and tends to write the long-form critique threads that elevate a project past its star count. They overlap with the people who follow systemd internals, who star tree-sitter grammars, and who file thoughtful issues against new config formats.

The third is the Rust user who wants a working example of overlay-merge semantics in Rust source. The merge module is 207 lines and self-contained, the test file is 591 lines and covers the unintuitive corners (mode-switching clears, CLEAR-with-scalar errors, keyed-vec replacement by element index). A reader copying the pattern into their own project is a real outcome here.

The audience that does not overlap cleanly with orch (the library) is the audience that wants a docker-compose replacement to run their homelab. That audience is orchd's, not orch's. Distribution copy that conflates the two pulls library-side discovery into engine-side launches and dilutes both.

### orchd (engine)

Three kinds of people will install orchd, run it, and feel it fits them.

The first is the self-hoster running services on a Linux box (a homelab, an LXC, a small VPS, a development VM) who has watched docker-compose accrete features they do not want and looked at hand-written systemd units and decided neither shape is right. They want one declarative file per project, the file to compose with overlays for environment-specific differences, and the runtime to be the init system that is already on the machine. orchd's bare runtime plus systemd platform plus the per-namespace target is what they install. The audience is small but loud, congregates on `r/selfhosted` and the Awesome-Selfhosted list, reads `news.ycombinator.com` for new tooling, and is exactly the audience that has filed dozens of issues on docker-compose's GitHub over the last five years asking for less.

The second is the developer who works on a project with a fixed services topology (postgres, redis, an app or two, a worker, a localstack) and has been managing it with one of: a Procfile and `foreman`, a hand-written `docker-compose.yml`, a half-page README of `systemctl --user` commands, or a Justfile that calls all of the above. They want the Orchfile to be the source of truth across local development, staging, and a small production VM, and they want the platform-specific generation (systemd today, launchd later) to be invisible. orchd's `--overlay` mechanism is the answer for them: one base Orchfile, one overlay per environment, `orchd generate` then `orchd up`. The fixture in `tests/fixtures/Orchfile` (postgres + redis + a disabled service with healthchecks and dependencies) is exactly this shape and reads well as a teaching example.

The third is the Rust user who reads `awesome-rust` for tasteful tools and would rather install a small Rust binary than a shell script, a Python package, or a Go binary that wraps systemctl. orchd is 13 commits, four dependencies (`orch`, `serde_json`, `clap`, `thiserror`), one binary, no async runtime. That shape is the audience's filter. They will install via `cargo install orchd` once it is published and run it against a contrived Orchfile to see what it does. The README's plain-technical register is the right voice for them.

The audience that does not overlap with orchd is the team running production at scale on Kubernetes, Nomad, or a managed orchestrator, the developer who wants `docker-compose up` to keep working without rewrites, and the operator running multi-host service meshes. The SPEC.md's own self-positioning ("Local, ephemeral, development, and staging environments. Not for: Production deployments at scale.") is the right boundary; importing the production-orchestrator audience would mismatch the project's substance.

The macOS user is a legitimate future audience but not a present one. The launchd platform is named in the architecture doc and accepted as a CLI string, but `LaunchdPlatform` does not exist in `src/platform/`; the engine hardcodes `SystemdPlatform::new()` in `engine.rs` regardless of the resolved platform name. A user on macOS who runs `orchd up` today hits `SystemdPlatform::check()`, which fails with "systemd is not running (no /run/systemd/system)". Until the launchd platform ships, orchd is Linux-only in practice and the README needs to say so above the install snippet, not below.

## Distinguishing claim

### orch (library and spec)

Two claims, both defensible from the code.

The first claim is that orch lifts the parse step of a service-orchestration DSL into its own crate with a published library API, a stable JSON schema, formal EBNF, and 140 tests. The closest comparables do not factor this way. docker-compose's parser is fused with the docker-compose runtime and is not separately packaged. nomad's HCL parsing is HCL, not service-orchestration-specific. systemd unit parsing is C inside systemd and is not embeddable. compose-spec/compose-go is the only directly comparable extraction (the JSON-schema and Go reference implementation for the Compose spec), and orch is to Containerfile-shaped DSLs what compose-go is to YAML-shaped ones, with a different intent (Containerfile lineage, drop-in overlay model, four named constraints).

The second claim is the merge semantics. orch ports the systemd drop-in mental model (last-wins scalars, key-merge keyed lists, append-and-dedup positional, an explicit `CLEAR <list>` directive, deferred ARG expansion so overlays can redefine base-file substitutions) onto a Containerfile-shaped directive set. docker-compose's overlays via `-f base.yml -f override.yml` use YAML deep-merge with documented surprises around list handling and array replacement. systemd drop-ins themselves are well-defined but apply only to systemd unit files. There is no precedent in the service-orchestration-DSL space for a Containerfile-shaped directive set with systemd-drop-in-shaped composition. That cross is the structural contribution.

### orchd (engine)

Three claims, each defensible from the code.

The first claim is that orchd compiles a service definition into a native init-system unit and supervises it through the init system, where the comparable Rust tools either reimplement systemd (rustysd) or wrap `systemctl` for already-existing units (servicer, systemd-manager-tui). orchd writes the unit file from the Orchfile, symlinks it into `/etc/systemd/system`, runs `daemon-reload`, then drives `systemctl start <ns>.target`. The compile step (`engine::generate`) is idempotent against an mtime check, the install step is a real symlink walk plus daemon-reload, the lifecycle step is real `systemctl start/stop/restart` against named units. No comparable Rust-shaped tool in the systemd-adjacent neighbourhood does the compile-and-install half from a higher-level config, while staying out of the supervisor's job and letting systemd do what systemd already does.

The second claim is the Runtime/Platform separation. ExecSet is the four-string bridge between the two layers, and the Runtime trait and Platform trait never reference each other, only `Service` and `ExecSet`. This means a containerd runtime that produces `nerdctl create / nerdctl start --attach / nerdctl stop / nerdctl rm` ExecSets, when one is written, plugs into the same systemd platform that handles `bare` today, with no platform changes required. Symmetrically, a launchd platform plugs into the same `bare` runtime when one is written, with no runtime changes required. docker-compose's runtime is fused with its declarative layer; nomad's runtime is its own thing; servicer is `systemctl` only; rustysd is the supervisor itself. orchd's claim is that the lowering pipeline (Service to ExecSet to native unit) is the right abstraction for a multi-init multi-runtime service tool, even if only one Runtime and one Platform have shipped today.

The third claim is the auto-generated ready gates. Healthcheck-aware ordering between systemd units is a real source of pain in hand-written units, and the conventional answer is to drop a wait-loop into the dependent service's `ExecStartPre` or to write a oneshot wait-for-port unit by hand and order against it. orchd does the second one automatically, deriving the gate set from the Orchfile dependency graph: `services_needing_ready_gates` collects every service that has a healthcheck and is referenced by another service's `REQUIRES` or `AFTER`, then the systemd generator emits a `<ns>-<svc>-ready.service` oneshot per gate, then the dependent service's `After=` points at the gate not the main unit. The user writes the healthcheck once in the Orchfile and gets the ordering right without writing a single oneshot. This is one of the small but non-trivial pieces of plumbing the engine exists to do.

## Distribution state, current

### orch (library and spec)

License is Apache-2.0 by file at the repo root. The `Cargo.toml` does not yet carry a `license` field. The README declares "Apache 2.0" at the bottom but does not mention a dual-license posture. Issue #2 (open, filed 2026-05-09 by the distribution team) recommends switching to `Apache-2.0 OR MIT` per Rust dual-license convention (tokio, hyper, serde, cargo all do this), adding `LICENSE-APACHE` and `LICENSE-MIT` filenames most Rust tooling expects, and updating the README and `Cargo.toml` to declare the dual license. Issue #2 is **not** stale; the action items it lists are still pending.

Latest local tag is `v0.2.0` (2026-03-07) for the multi-file composition release. No GitHub release has been published from that tag. The repo description on GitHub is "Orchfile specification and compilers" (note the plural; the description anticipates orchd and any other downstream as the implied second compiler). The repo carries no topics. Last push to the repo was 2026-03-07; the maintainer has been on other projects (clwnd, ccft) since.

The crate is **not** on crates.io. The name `orch` is already taken on crates.io by `guywaldman/orch`, an LLM orchestration library published since 2024-07-19, max version 0.0.16, total downloads about 14,965 (as of the dossier date), license MIT, repo `github.com/guywaldman/orch`. `cargo install orch` is therefore impossible under the current name. Available alternatives that fit: `orchfile` (the file format name; not taken), `orch-spec` (not taken), `orch-parser` (not taken). Recommended choice is `orchfile`, which doubles as the file format name and reads correctly on lib.rs. The binary inside the published crate can stay named `orch` (Cargo allows package name and binary name to differ).

The `orch parse Orchfile | jq` workflow is real today and the README documents nine bash-shaped consumers ("list service names", "get port mappings", "build dependency-ordered start list", and so on). This workflow is the library's de facto demo; a reader pasting any of the snippets into a shell sees JSON come out. There is no separate gif or asciinema for orch, and arguably the bash snippets carry that work for the library audience already.

### orchd (engine)

License is Apache-2.0 by file at the repo root. The `Cargo.toml` does not yet carry a `license` field. The README declares "Apache 2.0" at the bottom. This is in tension with `policy.md` §4, which defaults end-user binaries to MIT and reserves Apache-2.0 for projects with patentable IP or runtimes intended to be embedded into downstream systems where patent assertion is a real risk. orchd is an end-user binary with no library exports and the patent grant has nothing to attach to. Issue #2 (open, filed 2026-05-09 by the distribution team) recommends MIT and is consistent with the policy default. The maintainer's choice of Apache-2.0 in the actual `LICENSE` file is the more recent action and may have been a deliberate decision to keep both repos in the same license family. The dossier flags the discrepancy and leaves the resolution to the maintainer; either MIT (per policy default) or Apache-2.0 (per current `LICENSE` file) is defensible, but the `Cargo.toml` `license` field is unambiguously missing and is a real launch-blocker for crates.io publish under either choice. Issue #2's wording is **partially stale**: a `LICENSE` file does exist, the action item "Add LICENSE (MIT)" is no longer accurate, but the substantive question (which license, with the `Cargo.toml` field set, declared in registry metadata) is unresolved and the issue should be edited rather than closed.

The `Cargo.toml` depends on `orch` via `git = "https://github.com/adiled/orch.git", branch = "main"`, not from crates.io. This is structural to the joint launch path: cargo registry rules forbid published crates from depending on git URLs, so orchd cannot be published on crates.io until orch is published under a real name. The orch publish therefore is a hard prerequisite for the orchd publish, not just a sequencing preference. Once orch lands on crates.io as `orchfile` (or whatever name lands), orchd's manifest changes one line (`orch = "0.x"`) and `cargo publish` becomes available.

There is no GitHub release for orchd. There is no tag in the local clone. Last commit is `f7c5d86 add README, LICENSE (Apache 2.0)` on 2026-03-07. The 13-commit sequence walks the project from the architecture-doc commit (`e684aab`) through scaffolding, bare runtime, systemd generator, lifecycle, then the `.orchrc` and force-flag final touches. The shape of the history is a maintainer building the project linearly through the phased-delivery doc in `docs/init/09-phased-delivery.md` and stopping when the integration test on the fixture Orchfile passed. The 75 tests in the suite cover all the unit-file directive mappings, the bare runtime's host/container behaviour, the config merge order, the engine's error wiring, the duration parser, the orchrc parser, and the built-in variable overlay; the one ignored integration test wires the real `orch` binary through the full pipeline and asserts on generated unit content. This is launch-grade test discipline for a 13-commit project.

The repo description on GitHub is "Orch engine", which is terse and accurate. No topics are set. The README is 96 lines, opens with the pipeline (`Orchfile → orch parse (JSON) → Runtime (ExecSet) → Platform (native artifacts)`), shows the Implemented/Planned matrix, lists every CLI command with flags, documents the merge order for `.orchrc` and env and CLI, and ends with a 12-line example showing a full `orchd --orchfile ./Orchfile --overlay bare.orch generate` to `orchd up` to `orchd health` to `orchd logs postgres` to `orchd clean` flow. This is a good README for a Rust user reading it cold; it is a sparse README for a self-hoster who wants to know how this differs from docker-compose. The two audiences want different first paragraphs.

Issue #1 (full runnable example in README) and issue #3 (comparison block vs docker-compose, raw systemd, nomad) are both open and both substantively correct. Neither is stale. Either or both, landed in the README, would convert a cold visitor faster than the current README does.

## Distribution state, gaps

### orch (library and spec)

The crates.io publish is blocked on the name decision. Not on the publish workflow. Picking `orchfile` is the recommended unblock; the alternative is `orch-spec`. Either lets `cargo add orchfile` (or `cargo add orch-spec`) become real for downstream consumers and gets the library onto lib.rs, where the spec-curious audience does most of its discovery.

The dual-license action from issue #2 is unblocked (it is a one-pass edit to `LICENSE-*` files, `Cargo.toml`, and the README) and should land before the crate publish. Once on crates.io, changing the license is a non-trivial coordination problem with downstream pinned-version consumers, so doing it before publish is materially cheaper than doing it after.

The README's `## Install` section currently shows `cargo build --release` plus a `cp` to `/usr/local/bin/`. After publish, the install line becomes `cargo install orchfile` (or whatever name lands), which is shorter, more discoverable, and what the Rust audience expects. This edit lands the same day as the publish.

There is no GitHub release from the local `v0.2.0` tag. Pushing the tag and writing a release body that names what shipped (multi-file composition, the systemd drop-in overlay model, the `CLEAR` directive, the `pub mod types` library API, the EBNF and PDF) costs about 30 minutes and gives the project a release-grade artifact that newsletters and aggregators can link.

The repo description is good but the topics are unset. Recommended topics for orch: `rust`, `parser`, `service-orchestration`, `dsl`, `containerfile`, `systemd`, `dropin`, `spec`. Topic surface is free reach via GitHub topic pages and is currently unused.

### orchd (engine)

The crates.io publish is blocked on orch publishing first. The `Cargo.toml` git dependency on `adiled/orch` cannot ride along through `cargo publish`. Once orch lands as `orchfile`, orchd's manifest swaps the git line for `orch = "0.x"` (or renames the dependency name to match), `license = "Apache-2.0"` or `license = "MIT"` is set in `[package]`, `keywords` and `categories` are populated, and `cargo publish` runs. The `orchd` package name itself is **available** on crates.io (verified 2026-05-09 against `crates.io/api/v1/crates/orchd`); no rename is required for the engine half.

Linux-only must be surfaced near the top of the README, not implied by the platform matrix below the install snippet. A four-line front-matter inset along the lines of "orchd ships systemd today; macOS launchd is planned. macOS users hit a 'systemd is not running' error on `orchd up`. Track issue #X" would inoculate against the launch-thread question that arrives within the first hour. There is no current GitHub issue tracking the macOS-status question; opening one to point at would carry the answer past the launch.

The README does not yet surface the engine's actual differentiator above the fold. The Runtime/Platform separation, the auto-generated ready gates, the per-namespace target, the built-in variable injection, and the mtime-based generate skip are all in the code and in `docs/init/00-architecture.md`, but the README's pitch leans on the pipeline diagram and the command list. A reader who skims the README in 10 seconds learns there is a generator, but does not learn what the generator does that hand-rolling a unit file would not. Issue #1 (full runnable example) and issue #3 (comparison block) together fix this, and either alone is high-yield.

There is no comparison block in the README and the audience needs immediate answers. The closest neighbours in the Rust systemd-tools landscape are servicer (CLI that abstracts `systemctl` for arbitrary user-supplied commands, no Orchfile shape), rustysd (systemd reimplementation in Rust, no Orchfile shape, alternative supervisor not a generator), and `iKeepLearn/systemd-service` (Rust library that programmatically generates units for a Rust binary, library not CLI, single-binary not multi-service). The closest non-Rust neighbours are docker-compose (YAML over container runtime, multi-host networking, container-shaped), Quadlet (systemd-distributed declarative units for podman containers, container-shaped not bare-shaped, ships with podman not as a separate tool), and hand-written systemd unit files (the substrate orchd compiles to). orchd's lane is between Quadlet and docker-compose: declarative file like compose, native unit-file output like Quadlet, bare-mode-first like neither. Issue #3 is the right place to write this, in 4 to 6 lines per neighbour.

There is no demo recording. The fixture Orchfile in `tests/fixtures/Orchfile` plus `orchd generate` plus a `cat /etc/systemd/system/orch-postgres.service` plus `orchd up` plus `orchd status` plus `orchd health` plus `orchd clean` is a clean 60 to 90 second asciinema. The Orchfile already exists; the recording is a one-shot session on a Linux box.

There is no `adils.me` thesis post yet. Per the joint plan section below, the post is engine-shaped (orchd's pitch is the wider story) and orch sits in the second paragraph. The post is a T3-to-T2 promotion gate, not a same-week launch artifact.

There is no Homebrew formula. macOS is not currently supported, so the formula does not earn its weight today. When the launchd platform ships, the formula entry in `adiled/homebrew-tap` is a one-hour task and the natural install path for the macOS audience.

The repo's GitHub description ("Orch engine") and topics (none set) are both a free-reach surface that costs nothing to fill. Recommended description direction: "Declarative service orchestration. Compiles Orchfiles to native systemd units." Recommended topics: `rust`, `systemd`, `service-manager`, `service-orchestration`, `self-hosted`, `cli`, `orchfile`, `linux`. Eight topics, mix of broad and niche, populates GitHub topic pages and the `awesome-rust` discovery path simultaneously.

### Joint, both repos

The two-repo split is a real discoverability cost on the user-facing side and an asset on the library-consumer side. A first-time visitor to `adiled/orchd` who clicks the orch link in `orchd`'s README has to leave one repo and read another to understand the spec; that is friction. A library consumer who wants only the parser and not the engine has the right thing to depend on; that is the asset. The cross-link copy in both READMEs is what carries this trade-off, and it currently does. orch's README links to "platform-specific generators (launchd, systemd, etc.)" without naming orchd; updating that line to link orchd by name is a one-edit improvement.

Neither repo is on `awesome-rust`, `awesome-self-hosted`, or `alternativeto.net`. These are slow-burn discovery channels and the right time to submit is after the crates publish lands. The orch entry on `awesome-rust` belongs under a parser/config subsection. The orchd entry on `awesome-self-hosted` belongs under service managers. Submitting them as one combined entry would misrepresent what each does.

There is no `adils.me` thesis post yet. Per `policy.md` §6, the post is the launch's primary artifact for any T2 push, but the project is T3 and a thesis post is part of the T3-to-T2 promotion gate. The right post is engine-shaped (orchd's pitch is the wider story) and would link to orch as the underlying spec. orch's role in that post is the second paragraph, not the lede.

## Distribution state, aspirational

### orch (library and spec)

A short doc-comment pass on `orch::types::*` would make the lib.rs surface render well on docs.rs. Each typed field already has a meaningful name and an inline comment in some cases; promoting those to `///` doc comments and adding one or two examples on `OrchFile`, `Service`, and the merge model would carry the library's API for any reader landing from a `docs.rs/orchfile` link. This work is small and it compounds.

A second consumer of orch (separate from orchd) would prove the library/engine split was worth the two-repo cost. The cheapest plausible second consumer is a `orchfile-jsonschema` crate or doc page that emits the JSON schema corresponding to `OrchFile`, so editor tooling (VS Code's YAML/JSON schema integrations) can validate Orchfiles in the editor before parse time. This is a 200-line side project for someone who has already taken orch as a dependency, and it surfaces the library nature of the parser concretely.

A short post specifically on the systemd-drop-in merge semantics ported to a Containerfile-shaped DSL would do unusual long-tail work in front of the spec-curious audience. Three to five hundred words plus one diagram (the merge rules table that already exists in SPEC.md, redrawn). This post is portable to lobste.rs and to hacker news as a non-launch submission, and pulls a different audience than orchd's docker-compose-shaped pitch will.

### orchd (engine)

The launchd platform is the most-asked-for capability that does not exist today. The architecture doc names the work explicitly ("apple runtime, launchd platform, future"), the Platform trait is generic, and the CLI/`detect_platform` already accept "launchd" as a string. Once a `LaunchdPlatform` lands in `src/platform/launchd/` with a `generate` that emits a plist with `ProgramArguments`, `RunAtLoad`, `KeepAlive`, `EnvironmentVariables`, and a `lifecycle` that calls `launchctl bootstrap/bootout/kickstart`, orchd becomes a real macOS tool. This work is bounded: the systemd platform is 24KB of Rust across `generate.rs` and `lifecycle.rs`; a launchd equivalent is in the same range. Until then, "Linux-only" is the honest framing.

A `containerd` runtime would let orchd deliver on its container/host duality without forcing the user to write a bare overlay. The runtime trait already accepts a container service and produces an ExecSet whose `pre_start` is `nerdctl pull && nerdctl create` and whose `start` is `nerdctl start --attach <name>`. The architecture doc walks through the implementation in `02-runtime-interface.md` with worked code. This is post-launch territory and probably the second-most-asked-for capability after launchd.

A short demo video showing the fixture Orchfile compiled to systemd units, the `orchd up` to `systemctl status` to `orchd health` to `orchd logs --follow` flow, with one ready gate firing visibly between postgres-ready and redis starting, would seal the engine pitch in a single screen. asciinema is the right format for the Rust audience; vhs works equally well.

A `clean` flag walkthrough in the README would land the "orchd takes responsibility for the artifacts it writes" claim. `orchd clean` removes generated units, removes their symlinks from `/etc/systemd/system`, runs `daemon-reload`, and (unless `--keep-data`) deletes the data directory. This is the kind of cleanup-completeness that hand-written units do not provide and that distinguishes orchd from "I added a wrapper around systemctl" tools.

## Plan

The plan is sequenced so the orch publish unblocks orchd's distribution path, and the orchd launch artifact carries both repos.

### Pre-launch, orch first

1. Decide the crate name. Recommended: `orchfile`. Alternative: `orch-spec`. Document the choice in this dossier's notes section before any further work. The package name on crates.io being already taken is the single most expensive prior to leave un-checked through to a launch beat.
2. Apply the dual-license action from issue #2. Add `LICENSE-APACHE` and `LICENSE-MIT` files (replacing or alongside the existing `LICENSE`), set `license = "Apache-2.0 OR MIT"` in `Cargo.toml`, update the README license section. Close issue #2 with a short comment naming the commit that resolved it.
3. Push the local `v0.2.0` tag to GitHub and publish a release body. The body names multi-file composition, the systemd-drop-in overlay model, the CLEAR directive, the library API (`pub mod types` with serde derives), and the EBNF plus PDF.
4. `cargo publish` orch under the chosen name. Verify the docs.rs render (`docs.rs/<chosen-name>`) before announcing. Add the crates.io badge and a `cargo install <chosen-name>` line to the README replacing the current `cargo build --release` install snippet.
5. Set GitHub topics on the orch repo: `rust`, `parser`, `service-orchestration`, `dsl`, `containerfile`, `systemd`, `dropin`, `spec`.
6. Update orch's README to link orchd by name in the "platform-specific generators" sentence; the cross-link is currently abstract.

### Pre-launch, orchd

The orchd pre-launch follows the orch publish, because the `Cargo.toml` git dependency on `adiled/orch` blocks `cargo publish` for orchd. Steps below assume orch has already published to crates.io under `orchfile` (or whatever name lands).

1. Resolve the license question. Either commit to Apache-2.0 (current `LICENSE` file, consistent with orch) or switch to MIT (`policy.md` §4 default for end-user binaries). Update the `LICENSE` file if the choice changes, set `license = "Apache-2.0"` or `license = "MIT"` in `Cargo.toml`, update the README license section. Edit issue #2's body to reflect the resolved choice and close it. Either choice is defensible; the policy default is MIT and the maintainer's prior action was Apache-2.0.
2. Update the `Cargo.toml` dependency on orch from the git URL to the published crate name (`orch = "0.x"`, or `orchfile = { version = "0.x", package = "orchfile" }` if the crate name on crates.io differs from the imported module name). Verify `cargo build --release` and `cargo test` still pass. The bare runtime test that does not need `orch parse` (`test_exec_set__host_service_returns_start_command` and friends) will still pass; the ignored integration test will need the published `orchfile` binary on PATH.
3. Land the README front-matter inset for the macOS-status question (four lines, plain language). This is what carries the launch through the first hour of inbound questions.
4. Land issue #1 (full runnable example) and issue #3 (comparison block) in the README. The example uses the existing `tests/fixtures/Orchfile` shape (postgres + redis with a healthcheck-gated dependency). The comparison block names servicer, Quadlet, and docker-compose in 4 to 6 lines each, honest about trade-offs.
5. Set the GitHub repo description to "Declarative service orchestration. Compiles Orchfiles to native systemd units." Set topics: `rust`, `systemd`, `service-manager`, `service-orchestration`, `self-hosted`, `cli`, `orchfile`, `linux`.
6. Tag a release on `adiled/orchd`. Recommended `v0.1.0` to mark the first release-grade cut. Write a release body that names the surface area (Runtime/Platform separation, bare runtime, systemd platform with auto-generated ready gates, full lifecycle commands, `.orchrc` plus env plus CLI config merging, mtime-based generate skip) and links the matching orch release.
7. Record the demo. About 60 to 90 seconds of asciinema: `cat Orchfile` (the fixture), `orchd generate`, `cat /etc/systemd/system/orch-postgres.service` (the generated unit), `orchd up`, `orchd status`, `orchd health -v` (showing ready gates firing), `orchd logs postgres -n 5`, `orchd clean`.
8. `cargo publish` orchd. Verify `cargo install orchd` works on a fresh Linux box. Update the README install snippet to lead with `cargo install orchd`.

The pre-launch is bounded: the code is launch-grade today, the gaps are README-shaped and metadata-shaped. A focused half-day of work plus the orch publish dependency is the path.

### Launch beat (joint)

The launch beat carries both repos through one `adils.me` post and one Show HN. orchd is the user-facing story and the post leads with orchd's run-services-on-your-laptop pitch. orch is the second-paragraph mention with a direct link to the crates.io page. Subreddit posts (`r/selfhosted`, `r/rust`) are framed for orchd and link to the post, not to either repo directly.

For orch's narrower audience (the spec-curious, the lib.rs readers), the right channels are:

1. `awesome-rust` PR under a parser/config subsection. Entry written in plain technical register: "Orchfile parser library and CLI. Containerfile-shaped service orchestration DSL with systemd-drop-in overlay semantics. Library API plus stable JSON output for downstream generators." Submit after publish, separate from orchd's `awesome-self-hosted` PR.
2. lib.rs surface naturally indexes new crates; a clean README on crates.io and good doc-comments on docs.rs do the work without a coordinated push.
3. A short side post on `adils.me` (after the main thesis post) on the systemd-drop-in merge semantics ported to a Containerfile-shaped DSL. Optional but compounds.

### Promotion to T2

orch graduates from T3 to T2 when (a) the crate is published on crates.io under a real name, (b) the GitHub release is live, (c) at least one external user has taken the dependency or filed an issue, and (d) the maintainer has decided whether the library half is worth its own thesis post. Until then orch sits at T3 alongside orchd.

## What we are not doing

Not pitching orch as a "service orchestrator" or "docker-compose alternative" by itself. orch is a parser and spec library; the runtime that those phrases point at lives in orchd. Conflating the two on orch's side dilutes the library audience that exists for the spec implementation and confuses the engine audience that exists for `orchd up`.

Not pitching orch as a generic config language. The directive set is service-shaped (FROM, RUN, PUBLISH, REQUIRES, RESTART) and the merge semantics are tuned to that shape (mode-switching clears between FROM and RUN, the four constraints are about service mode coherence). Generalizing orch to handle arbitrary key-value config would dissolve the contribution.

Not splitting the launch beat across two posts. The thesis post is engine-shaped and orch is the second-paragraph mention. Two posts compete for the same audience and split the inbound. One post with a clean cross-link does the work.

Not waiting for the launchd platform on the engine side before publishing orch. orch's value as a library is independent of which platforms orchd supports today, and crates.io publish is fast enough that there is no benefit to coupling the two release calendars.

Not pitching orchd as a "docker-compose alternative" or "container-less orchestrator" in the README's first paragraph. Both are real audience overlap points and both are imported categories. The first lands the launch in the production-orchestrator lane where Nomad and Kubernetes already live; the second is `policy.md`-forbidden vocabulary until verified, and the only verifiable framing here is "compiles Orchfiles to native systemd units" plus the bare-mode-default stance. The README and the launch posts should lead with the substance (Orchfile to systemd units, ready gates from healthchecks, Runtime/Platform separation) and let the audience supply the docker-compose comparison from their own context, with the comparison block as the structured answer when they ask.

Not pitching orchd as a "systemd wrapper" either. The wrapper category leader is servicer; orchd's claim is the compile step from a higher-level config, not a CLI sugar layer over `systemctl`. Importing the wrapper frame would route the launch to a smaller, already-served audience.

## Metrics

The project is T3 per `metrics.md`. Per-project readiness is binary, not numeric. The five gates are:

1. One-paragraph pitch that a non-author would forward.
2. 30-second demo recorded.
3. Install verified clean on a never-seen-it machine.
4. First-user persona named. (For orch: a tool author taking the library dependency. For orchd: a self-hoster running a real services stack.)
5. Coordinated launch checklist from `policy.md` §6 fully green.

For orch specifically, gate 3 is `cargo install <chosen-name>` followed by `<binary> parse <example>.orch | jq` on a fresh machine. Gate 4 is satisfied when at least one private-share recipient takes the dependency and reports back, or when a second consumer (the orchfile-jsonschema crate or similar) lands.

For orchd specifically, gate 3 is `cargo install orchd` on a fresh Linux box, then `orchd --orchfile <fixture> generate && orchd up && orchd health && orchd clean` running clean against the fixture Orchfile. Gate 4 is satisfied when at least one self-hoster has run orchd against a real services stack and reported back; the maintainer's own LXC running orchd-managed services (per `docs/init/09-phased-delivery.md` Phase 7) is the natural first instance.

Project-specific signals to watch once orch is on crates.io: weekly download cadence on `crates.io/crates/<chosen-name>`, reverse-dependency count on `crates.io/crates/<chosen-name>/reverse_dependencies`, docs.rs visit pattern (visible only in coarse aggregate; not load-bearing), and inbound issues that engage with the spec rather than the binary (issues that quote SPEC.md sections or propose grammar changes are the cleanest signal that the library audience is forming).

Project-specific signals to watch once orchd is on crates.io: install count and weekly downloads on `crates.io/crates/orchd`, the issue-shape mix (questions about systemd unit content vs questions about Orchfile semantics; the former is engine audience landing, the latter routes to orch), and inbound from `r/selfhosted` referrers vs `r/rust` referrers (the first signals docker-compose-fatigue audience, the second signals tasteful-Rust-tool audience). The two are different cohorts and the launch will land harder against one than the other.

## Launches

None executed yet. The pre-launch sequence above is the gate.

## Notes

The previous dossier pass framed both repos under a single "alternative to docker-compose" pitch and treated orch as a secondary entry to orchd's user-facing story. The library / spec angle was named in passing but not given its own audience or its own channels. This revision keeps the joint dossier shape and pulls the orch-half into its own subsections so the library audience and the engine audience can be reasoned about separately. The launch beat is still joint, because that is where the audience overlap actually pays for itself; the discovery channels split because the audiences split.

The crates.io name collision (`guywaldman/orch`, an LLM orchestration library, was on the registry since 2024-07 with about 15k downloads) was not flagged in the prior dossier and is the single most material correction in this pass. The recommended alternative is `orchfile`, which is available, doubles as the file-format name, reads correctly on lib.rs, and lets the binary inside stay named `orch` per Cargo's package-name vs binary-name distinction. `orch-spec` is the second-best alternative.

The license posture in the previous dossier ("license not declared in either README") was directionally correct but missed that orch already has an Apache-2.0 LICENSE file and a README mention. The actual gap is the dual-license action (Apache-2.0 OR MIT, the Rust convention) plus the `Cargo.toml` `license` field, which issue #2 names with the correct action items. Issue #2 is not stale and should be left open until the actions land.

The two-repo structure is the project's stance and works well on the library side. orch as a depended-on parser crate and orchd as one runtime consumer is a clean cut. A workspace-merge (one Cargo workspace with both crates) is a future option if cross-repo coordination becomes a tax, but the current state does not call for it.

The grammar PDF (`docs/grammar.pdf`, 141KB) is regenerated on every push by a `pre-push` git hook running `pdflatex` (with a TinyTeX fallback path baked in). The maintainer ships a typeset spec for a small Rust tool, and that level of investment is itself a signal to the spec-curious audience that the format is meant to be readable, not just parseable. Distribution copy on crates.io and in the README should link the PDF visibly.

The orchd license posture is in tension with `policy.md` §4. The policy default for end-user binaries is MIT; the actual `LICENSE` file at the repo root is Apache-2.0. The maintainer's prior action (`f7c5d86 add README, LICENSE (Apache 2.0)`) is the more recent decision and may have been a deliberate choice to keep both repos in the same license family. The dossier flags the discrepancy and leaves the resolution to the maintainer; both choices are defensible and both require the `Cargo.toml` `license` field to be set before crates.io publish. Issue #2's body is partially stale (the action item "Add LICENSE (MIT)" no longer matches the repo state) and should be edited to track the resolved choice rather than closed silently.

orchd's `Cargo.toml` git dependency on `adiled/orch` is the structural reason the orch publish must precede the orchd publish. This is not a sequencing preference; cargo registry rules forbid published crates from depending on git URLs. Naming this in the plan as a hard prerequisite is what carries the launch through without a mid-publish surprise. The orch publish is the unblock; the orchd publish is the launch artifact.

orchd is more launch-ready than the prior dossier credited. The README is small but accurate. The test suite is 75 unit tests plus one ignored integration test that runs the full pipeline. The lifecycle commands are real (`systemctl start/stop/restart/show`, `journalctl -u`, real symlink walks plus `daemon-reload`). The auto-generated ready gates and the per-namespace target are non-trivial systemd plumbing that hand-written units do not provide. The gaps are README-shaped (front-matter inset for macOS-status, comparison block, full runnable example) and metadata-shaped (license field, GitHub description, topics, tagged release). Half a day of focused work plus the orch publish dependency.

## Research log

Sources consulted, in order, for the orch half:

- Local clone at `/Users/adil/orch`. `Cargo.toml`, `README.md`, `SPEC.md`, `LICENSE`, `grammar.ebnf`, `examples/demo.orch`, `scripts/pre-push`.
- Source: `src/lib.rs`, `src/main.rs`, `src/error.rs`, `src/types.rs`, `src/parser.rs`, `src/parser/tests.rs`, `src/merge.rs`, `src/merge/tests.rs`, `src/resolve.rs`. Skim of test coverage, not full read.
- Git history: full log (9 commits across 2026-02-28 to 2026-03-07, one local tag `v0.2.0` at commit `aaa910f`). The two head commits (`d1b9391`, `4a1e46a`) added `is_host`/`is_container` helpers and exposed `pub mod types` with `Deserialize` for downstream library consumers; this is the moment orch became a published library and not just a CLI.
- GitHub issues: #1 (closed, the v0.2.0 multi-file composition proposal) and #2 (open, the dual-license action). Verified #2's substance against the current repo state: LICENSE is Apache-2.0 only, `Cargo.toml` lacks the `license` field, README declares "Apache 2.0" at the bottom, dual-license action items still outstanding.
- `gh repo view adiled/orch` for description, license, topics, latest release, push date.
- `crates.io/api/v1/crates/orch` to verify the package name. Result: taken by `guywaldman/orch` (LLM orchestration library) since 2024-07-19, max version 0.0.16, about 14,965 total downloads.
- `crates.io/api/v1/crates/orchfile`, `crates.io/api/v1/crates/orch-spec`, `crates.io/api/v1/crates/orch-parser`. All three return "does not exist" and are available.
- Existing dossier at `projects/orch.md` (the prior single-file dossier covering both repos).
- `policy.md` (license defaults, T3 to T2 promotion gates, voice rules), `channels.md` (per-project channel mix for `orch`/`orchd`), `metrics.md`, `projects/_index.md`, `projects/ccft.md` and `projects/clwnd.md` (dossier shape and rigor reference).

Sources rejected as imported-category framings for the orch half:

- "Service orchestrator" and "docker-compose alternative". Both are real positions for orchd, but importing them onto orch flattens the library audience into the engine audience and routes spec-curious readers to the wrong place. orch is parser, spec, and library API. The SPEC's own self-positioning is "Containerfile's declarative simplicity, not docker-compose's configuration verbosity"; that quote is the correct register for orch's framing and the dossier uses it directly.
- "Systemd wrapper". Systemd is one Platform target on orchd's side and is not orch's concern. orch's only systemd-shaped contribution is the drop-in overlay model ported into Orchfile composition semantics, which is a structural design choice not a runtime claim.
- "Config language". orch is service-orchestration-shaped (FROM, RUN, PUBLISH, REQUIRES, RESTART, four mode-coherence constraints). Generalizing the framing to "a new config language for X" loses the directive-set lineage and the merge semantics that are the contribution.

What surprised me in step 4, when writing the project's-own-voice description for the orch half:

The library nature of the crate is more developed than the prior dossier credited. Commits `4a1e46a` ("expose types as library crate with Deserialize support") and `d1b9391` ("add is_host/is_container helper methods to Service") are the two head commits and they exist specifically to make orch a depended-on Rust crate, not just a binary that emits JSON. The `pub mod types` line in `lib.rs` is six characters of code that change orch's audience entirely; without it, orch is a JSON-emitting CLI that anyone can pipe into `jq`, and with it, orch is a Rust library that downstream code can `cargo add` and `serde_json::from_str` directly into typed structs. The prior dossier read orch as a "parser/spec library" abstractly. The current code is a parser/spec library concretely, with a one-module `lib.rs` and serde-deriving public types, and the launch should reflect that.

The other surprise was the formal-grammar investment. The 141KB typeset PDF generated from a hand-written `grammar.tex` (17KB), the pre-push hook that regenerates it, the EBNF file with railroad-shaped formatting: this is the maintainer signaling that the Orchfile format is meant to be a real spec, not a marketing artifact. That investment carries a different audience than the docker-compose-vs-orchd lane reaches, and the dossier needs to send both signals through different channels.

The crates.io name collision was the third surprise. The prior dossier proposed `cargo install orch` and `cargo install orchd` as install paths without checking the registry. `orch` has been on crates.io since 2024-07 under a different maintainer with a meaningfully different project. This dossier names the collision, recommends `orchfile` as the cleanest alternative, and lists the available alternatives so the maintainer can decide before any further work.

Sources consulted, in order, for the orchd half:

- Local clone at `/Users/adil/orchd` (cloned fresh via `gh repo clone` for this pass; not previously present locally). `Cargo.toml`, `README.md`, `LICENSE`, full `src/` tree, `tests/fixtures/Orchfile`, every file under `docs/init/` (architecture, types, runtime-interface, platform-interface, systemd-platform, bare-runtime, config, cli, testing, phased-delivery, versioning).
- Source: `src/main.rs`, `src/cli.rs`, `src/config.rs`, `src/types.rs`, `src/exec.rs`, `src/engine.rs`, `src/runtime/mod.rs`, `src/runtime/bare/mod.rs`, `src/platform/mod.rs`, `src/platform/systemd/mod.rs`, `src/platform/systemd/generate.rs`, `src/platform/systemd/lifecycle.rs`. Full read of every file.
- Git history: full log (13 commits, all 2026-03-07, walked from `e684aab` initial docs to `f7c5d86` final README plus LICENSE). The commit shape tracks the phased-delivery doc linearly: scaffold, bare runtime, systemd generator and engine pipeline, lifecycle commands, logs and list and health and clean, dead-code-clean and thiserror, unit tests, integration test, force flag and mtime check, `.orchrc` loader, README and LICENSE. The maintainer paused after the integration test passed.
- GitHub issues: #1 (open, full runnable example in README), #2 (open, LICENSE/MIT recommendation, partially stale), #3 (open, comparison block in README). All three were filed 2026-05-09 by the distribution team. Verified each against the current repo state. The repo has no other issues.
- `gh repo view adiled/orchd` for description, license, topics, latest release, push date. License field on GitHub: "Apache License 2.0" (auto-detected from LICENSE). No topics. No release.
- `crates.io/api/v1/crates/orchd` to verify the package name. Result: 404, available.
- Existing dossier at `projects/orch.md` (the prior single-file dossier covering both repos) and the orch-half subsections of the in-flight revision (the parallel agent's work).
- Web research scoped to orchd's actual claim. Servicer (servicer-labs/servicer, the closest neighbour: pm2-shaped CLI on systemd, daemonless, no Orchfile shape). Rustysd (KillingSpark/rustysd, an alternative supervisor). `iKeepLearn/systemd-service` (Rust library that programmatically generates units for a single Rust binary). Quadlet (systemd-distributed declarative units for podman containers, ships with podman). Compared each on shape, audience, and what orchd does that they do not.
- `policy.md` (license defaults, T3 to T2 promotion gates, voice rules), `channels.md` (per-project channel mix), `metrics.md`, `projects/_index.md`, `projects/ccft.md` and `projects/clwnd.md` (dossier shape and rigor reference).

Sources rejected as imported-category framings for the orchd half:

- "docker-compose alternative". Real audience overlap, real category, real launch-thread question. Importing it onto orchd's own pitch lands the launch in the production-orchestrator lane (Nomad, Kubernetes, Swarm) where orchd has no business. The SPEC's own self-positioning ("Local, ephemeral, development, and staging environments. Not for: Production deployments at scale.") draws the right boundary. The comparison block in the README is the right place to acknowledge the comparison; the lede is the wrong place.
- "Container-less orchestrator". `policy.md`-forbidden vocabulary until verified. The only verifiable framing is the substance: bare-mode-default, host process supervision via systemd, optional container runtimes when those land. Adopting the marketing-shaped frame would import the audience that wants Kubernetes-without-containers, which is not the audience the project serves.
- "Systemd wrapper". The closest Rust-shaped neighbour (servicer) is in this lane; orchd is in a different lane. orchd compiles a higher-level config to a unit file before letting systemd take over; servicer takes a user-supplied command and wraps `systemctl` for it. Importing the wrapper frame would route the launch to a smaller, already-served audience and miss the engine's actual contribution (the compile step from Orchfile to native unit).
- "Process supervisor" (the pm2 / supervisord lane). orchd is not a supervisor; systemd is. orchd is the generator that makes systemd usable from a declarative file. Adopting supervisor framing would oblige questions (auto-restart-on-failure semantics, log rotation, daemonless vs daemon-managed) that orchd answers by deferring to systemd, which is a different shape of answer than the lane expects.

What surprised me in step 4, when writing the project's-own-voice description for the orchd half:

The auto-generated ready gates were the first surprise. The prior dossier did not name them. The implementation in `src/platform/systemd/generate.rs` is a small function (`services_needing_ready_gates` plus `generate_ready_gate`) that derives the gate set from the Orchfile dependency graph and emits a oneshot per gate, then routes the dependent services' `After=` at the gate, not the main unit. This is the kind of plumbing that hand-written systemd units skip and that orchd's claim ("declarative service orchestration") is structurally about. The README does not mention ready gates. The launch should.

The Runtime/Platform separation is more developed than the README credits. The trait surface is small, the seam (ExecSet) is four optional bash strings, and the dispatch point (`runtime::create_runtime(name, config)`) is the single line that a containerd or podman implementation slots into. Today only `bare` ships, and the `match name` arm rejects everything else with `unknown runtime '{}'. Available: bare`. The architecture doc walks through a containerd implementation in detail (with worked code) but the runtime/mod.rs is missing the `pub mod containerd` line that would bring it to life. The seam exists; the work is bounded; the README's pitch is currently weakest exactly where the project's structural contribution is strongest.

The prior dossier's "no LICENSE" and "no tagged release" claims, fact-checked against the current repo: LICENSE is present (Apache-2.0), no tagged release exists. The first claim is stale; the second is correct. Issue #2's specific recommendation (MIT) is in tension with the maintainer's actual choice (Apache-2.0), and the issue body needs editing to reflect the discrepancy rather than closing silently. Issues #1 and #3 (full runnable example, comparison block) are substantively correct and should stay open until the README edits land.

The third surprise was orchd's git dependency on adiled/orch. The Cargo.toml line is `orch = { git = "https://github.com/adiled/orch.git", branch = "main" }`. This is structural to the joint launch path: `cargo publish` for orchd is blocked until orch publishes to crates.io under a real name (cargo registry forbids git deps in published crates). The prior dossier proposed publishing both crates in parallel; this dossier sequences orch first as a hard prerequisite.
