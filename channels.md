# Distribution Channels

Catalog of every channel under consideration. Each entry has: who it reaches, effort to use, expected yield, and which projects fit.

**Effort tiers** — `S` (set-and-forget, < 1 hr to set up), `M` (recurring author work, 1–4 hr per use), `L` (named launch beat, 4+ hr including prep + day-of presence), `XL` (talk / interview / longform, days of prep).

**Yield tiers** — relative, not absolute. `★` weak signal, `★★` reliable but small, `★★★` material lift, `★★★★` thesis-altering when it lands.

---

## Package registries (publish targets, not promo)

These are not promotion — they are existence requirements. A project not on its language's registry is not actually shipped.

| Channel | Effort | Yield | Audience | Fit |
|---|---|---|---|---|
| **npm** | S | ★ | JS/TS users discovering via `npm search` and dep crawlers | `ohlc-resample` (live), `clwnd`, `opencode-dir` |
| **PyPI** | S | ★ | Python users, plus search engines (`pypi.org` ranks well) | `bhai-lang` (when ready) |
| **crates.io** | S | ★ | Rust users; lib.rs and crates.io both index | `ccft`, `orch`, `orchd` |
| **Homebrew tap (`adiled/tap`)** | M | ★★ | macOS CLI users; one-line install is real reach | Any standalone CLI: `clwnd`, `iksir`, `ccft`, `orch`, `orchd` |
| **GitHub Releases** | S | ★★ | Search, RSS subscribers, dep crawlers — surprisingly material | All; mandatory for tagged releases |
| **MCP registry (Anthropic)** | M | ★★★ | Claude Code users; biggest channel for agentic dev tools right now | Whichever projects expose tools via MCP — currently a gap. See ASKS. |
| **Claude Code plugins/marketplaces** | M | ★★★ | Same audience, different surface | `clwnd`, `ccft` are natural plugin candidates. |
| **opencode plugins** | M | ★★★ | opencode users; small but high-engagement | `opencode-dir` (already shipping), `clwnd` (depends on opencode), `iksir` |
| **Docker Hub / ghcr.io** | M | ★ | Mostly self-hosters; matters for `orch`/`orchd` ecosystem credibility | `orchd` example images |

**House rule:** before any T2 launch beat, the project must already be live on its registry — otherwise the launch traffic lands on a "see source / clone repo" wall and bounces.

---

## Aggregator & curation lists

Slow-burn discovery — every entry sends consistent low-volume traffic for years.

| Channel | Effort | Yield | Notes |
|---|---|---|---|
| **`awesome-mcp`** | M | ★★ | High-quality list, accepts PRs. Direct relevance for any MCP-shipping project. |
| **`awesome-claude-code`** | M | ★★ | Curated, growing audience. Submission requires basic fit + a working repo. |
| **`awesome-opencode`** (already a fork in your repos) | M | ★★ | Smaller list, but `opencode-dir` and `clwnd` belong here on day one. |
| **`awesome-rust`** subsections | M | ★★ | For `orch`/`orchd`/`ccft`. Service-orchestration + agent-tooling subsections both apply. |
| **`awesome-typescript`** | M | ★ | Large, diluted. Limited yield unless project is a popular library. |
| **`awesome-self-hosted`** | M | ★★★ | Direct fit for `orch`/`orchd` (service orchestration) and any locally-runnable substrate work. High-traffic list. |
| **`crypto-trading` / `algotrading` lists** | M | ★★ | `ohlc-resample` belongs here; submit to one or two well-maintained ones. |
| **`PyPL`-equivalent language pages** | S | ★ | Only matters for `bhai-lang` (esolang / new-language indexes: esolangs.org, ProgrammingLanguages.io). |
| **`alternativeto.net`** | M | ★ | If the project is positioned as an alternative to a known tool (e.g. `orchd` vs docker-compose), worth the entry. |

---

## Discovery sites (one-shot launch beats)

These are coordinated single-day events. Pick at most one per launch.

| Channel | Effort | Yield | Notes |
|---|---|---|---|
| **Show HN** | L | ★★★★ | The biggest single beat available. Unforgiving — broken install on launch day = lost quarter. T2-ready projects only. Ideal: Tuesday–Thursday 8–10am PT post. |
| **Product Hunt** | L | ★★ | Less developer-dense than HN. Better for end-user-shaped products. Marginal fit for most of this portfolio (maybe `clwnd` if positioned as a product). |
| **Hacker News (regular submission)** | M | ★★★ | Submitting a thoughtful blog post about the project is often higher-yield than Show HN — less perform-y. |
| **Lobste.rs** | L | ★★★ | Smaller, technical, much harder to land but higher-quality discussion when it does. Needs an invite (request on `lobste.rs/invitations`). |
| **Reddit `/r/rust`, `/r/programming` (with caution), `/r/MachineLearning`, niche subs** | M | ★ to ★★ | Mostly hostile to self-promotion. Better when someone else posts your project. |
| **Reddit `/r/algotrading`** | M | ★★ | Active, technical, friendly to OSS tools. `ohlc-resample` natural fit. |
| **Reddit `/r/LocalLLaMA`** | M | ★★★ | Direct fit for any agent-substrate work. Audience is the right shape for several T3 substrate projects when they're ready. |
| **`/r/selfhosted`** | M | ★★ | Fit for `orch`/`orchd` and locally-runnable substrate work. |

---

## Social — writing for distribution

| Channel | Effort | Yield | Notes |
|---|---|---|---|
| **X / Twitter (technical tier)** | M | ★★ to ★★★ | Threads with one good demo gif outperform any other tweet shape. Reach depends on relationships, not posting cadence. |
| **Bluesky** | M | ★ | Smaller dev audience than X but growing; cross-post for free. |
| **Mastodon (fediverse + tech instances)** | M | ★ | Low yield but the audience that does engage is high-quality. |
| **LinkedIn** | M | ★ | Mostly noise; occasional spike when a recruiter or VC reshares. Worth a single launch post for T2/T3 with the founder/maintainer angle. |
| **Personal blog (`adils.me`)** | L | ★★★ | The single highest-leverage owned channel. Each piece is permanent and SEO-able. Use for thesis posts, postmortems, and "why we built X" pieces. |
| **dev.to / Hashnode mirror** | S | ★ | Auto-mirror posts. Free traffic from the platform's homepage. |

---

## Newsletters / curated digests

These do not accept self-submission unguardedly; the relationship is "produce something interesting → editor includes it." Effort is the *content* effort, not the submission.

| Channel | Effort | Yield | Audience | Notes |
|---|---|---|---|---|
| **TLDR AI** | XL (write good content) | ★★★ | AI engineers, product, infra | Covers tools, papers, releases. Tip line accepts submissions; landing depends on novelty. |
| **Latent Space (newsletter + podcast)** | XL | ★★★★ | High-quality AI engineering audience | Editorial; landing requires either being interviewed or writing a guest piece. |
| **Ben's Bites** | M | ★★ | General AI; high volume, lower per-mention lift | Tip submissions accepted. |
| **The Pragmatic Engineer** | XL | ★★★★ | Senior eng / engineering leadership | Editorial. Material when relevant; not a fit for everything in the portfolio. |
| **Console.dev** | M | ★★ | OSS dev tools weekly | Submission form on site. Fits `clwnd`, `opencode-dir`, `ccft`, `orch`/`orchd`. |
| **DevURLs / Hackernewsletter / Changelog Weekly** | S | ★★ | Aggregators that scrape HN front pages — no direct submission needed; they catch HN hits. |

---

## Talks / podcasts / streams (XL-effort, highest-yield)

| Channel | Effort | Yield | Notes |
|---|---|---|---|
| **Conference talks (LangCon, AI Engineer Summit, JSConf, RustConf)** | XL | ★★★★ | A talk that lands becomes the canonical introduction to a project for years. CFP windows are public. |
| **Podcasts (Latent Space, Software Engineering Daily, Changelog, FLOSS Weekly)** | L | ★★★ | Reach out to producers with a one-paragraph hook + clip. Episode + transcript = months of search traffic. |
| **Live streams / YouTube demos** | L | ★★ | Lower per-event yield but compounds; embed links from READMEs. |
| **University / research seminars** | XL | ★★ | Right venue for `bhai-lang` (lineage / GDPR) and certain T3 substrate projects. Academic audiences engage seriously. |

---

## SEO — owned surface

| Channel | Effort | Yield | Notes |
|---|---|---|---|
| **Per-project landing page** | L (one-time) | ★★★ | Vercel/Cloudflare Pages + a simple Next.js or static page. Outranks GitHub for the project name in search; collects email signups. Mandatory before T2 launch. |
| **GitHub Pages from the project repo** | M | ★★ | Cheap second-best when a custom landing isn't justified. |
| **`adils.me` portfolio updates** | M | ★★ | Bring all active T1/T2 projects above the fold. The portfolio page itself is a distribution channel. |
| **GitHub profile README (`@adiled/adiled`)** | S | ★★ | Currently the only entry for a profile visitor. Should mirror the active roster, not the chronological one. |
| **Topics & description on every repo** | S | ★★ | Free reach via GitHub search and topic pages. Currently underused — most repos have empty descriptions. |

---

## Per-project channel mix (first cut)

These are starting points, not fixed plans. Per-project dossiers can override.

### `ohlc-resample` (T1)
**Maintain.** npm publishing pipeline + Changesets is already solid. Promote on awesome-algotrading lists; stop active campaigning. The asset is the long tail. Add to `adils.me` portfolio above the fold.

### `clwnd` (T2)
Coordinated launch: opencode plugin marketplace post + `awesome-opencode` PR + Show HN + `/r/LocalLLaMA` + `adils.me` thesis post. Demo gif (already exists) is the lede everywhere.

### `opencode-dir` (T2)
Lighter coordinated launch: `awesome-opencode` PR + opencode community channels + an X thread with `/cd` demo. No Show HN — the audience is too narrow; HN flames niche tools.

### `ccft` (T2)
Show HN candidate but with a different framing: not "I made a tool" but "here's what your Claude Code session is actually doing — proxy + token ledger." Pair with an `adils.me` post showing real ledger output. `awesome-claude-code` PR follows.

### `bhai-lang` (T3)
Two arcs: (1) the *novel-language* arc — esolangs.org, programming-languages communities, eventually a research-style writeup; (2) the *data lineage* arc — GDPR/AI-governance audiences, OpenLineage / DataHub adjacencies. Naming collision with the Hindi `bhai-lang` is a real problem; address before any launch.

### `iksir` (T3)
Currently positioned in pure mythic register — that's authentic, but the README also needs a "what this actually does in operational terms" tier so an opencode user lands and gets value within 60 seconds. Once that exists, ship through `awesome-opencode` + opencode community + a demo video.

### `orch` / `orchd` (T3)
Position as **alternative to docker-compose for local services** — that framing gives a search-friendly anchor. crates.io publish + `awesome-rust` + `awesome-self-hosted` + a comparison post on `adils.me`. Talk submissions to RustConf / FOSDEM down the line.

### `khowarpedia` (T5)
Cultural project; promote among Khowar-speaking communities and language-preservation groups, not the agent-systems audience. Separate orbit.

### `clwnd-playground` (T4)
No standalone push. Linked from `clwnd` README as "see what running this looks like." Companion content.

---

## What we are *not* doing

- **No paid ads.** Performance marketing for OSS dev tools converts terribly and reads as inauthentic.
- **No mass DMs.** Outreach is targeted and individual or it doesn't happen.
- **No tweet-storms with 12 emojis.** Voice rules in `policy.md` apply to social copy.
- **No splashing across all channels for every release.** The portfolio is large; concentration on the right channel beats fan-out every time.
