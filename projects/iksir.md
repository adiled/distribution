# iksir

**Tier:** T3 — Ambitious / pre-launch • **Repo:** https://github.com/adiled/iksir • **License:** MIT • **Stars:** 0

## What it is

A Deno-based opencode workflow tool framed as **alchemical transmutation**: multiple "alchemists" (workers) prepare formulae in their own vessels (branches), but only one performs the Great Work at a time. Yields when blocked. Demands when ready. Single-flame discipline over chaotic parallelism.

Operationally, beneath the mythic register, iksir provides:
- **Sequential serialized work-queue** for opencode-driven coding sessions (one alchemist at the flame at a time).
- **Per-session isolation** via branches/vessels — no cross-contamination.
- **Crucible / Jawhar / Kitab al-Hikmah** — accumulating workspace, distilled essences, permanent record (likely SQLite-backed, per the `Sijill` mention in the layout).
- **Sacred servants** — internal roles (Munadi caller, Katib scribe, Arraf intent-diviner, Saail truth-diviner, Mumayyiz separator, Raqib guard, Hayat night-watcher, Munaffidh executor) that map to concrete substrate functions.
- **Telegram channels** as the default communication conduit; Linear as the default formula-store.

Install: `curl -fsSL ...iksir/main/install | bash` (after Deno + opencode are present).

## Why it matters to the portfolio

Iksir occupies a distinct position from `clwnd`: where `clwnd` is a *daemon-supervisor* for parallel agentic processes, iksir is **its philosophical opposite — single-flame discipline.** The two together demonstrate that the maintainer has thought about both ends of the parallelism question, which is itself a credibility signal.

The cultural register (Arabic alchemical vocabulary, Sufi-adjacent framing) is also distinctive — distinctive in a way the rest of the public portfolio is not.

## Audience

- opencode users for whom *parallelism is a problem*, not a feature — those who want serialized, deeply-considered transmutation rather than fan-out.
- A smaller subset of `clwnd`'s audience (roughly inverse: `clwnd` users who realize they want *less* concurrency, not more).
- The "single-piece-flow" / lean-software audience; this is a small but engaged niche.
- Practitioners drawn to mythic / alchemical framings as productivity / discipline anchors (small but devoted audience — see Tools for Thought, Hyperland, etc.).

## Distribution state — what's done

- ✅ MIT license declared.
- ✅ Working install script with documented update / uninstall paths.
- ✅ Distinctive, cohesive vocabulary (the README *is* the brand).
- ✅ XDG-compliant directory layout.
- ✅ Service registration via daemons.
- ✅ Vocabulary table (sacred hierarchy, sacred laws, terms with Arabic glyphs and English glosses).

## Distribution state — gaps

- ❌ **No operational tier in the README.** This is the biggest gap. A first-time visitor cannot answer "what does this *do*?" within 60 seconds. Concrete mechanics (one queue, one worker at a time, branch-per-vessel, telegram-by-default-conduit) are buried under vocabulary.
- ❌ **No demo content.** A `vhs` recording showing one alchemist at the flame, blocked, yielding to the principal, then resuming — would be the killer demo.
- ❌ **No screenshots or example sessions.** What does the Sijill register actually look like? What does a typical Jawhar entry contain? Show, don't only mythologize.
- ❌ Not on the `awesome-opencode` list yet.
- ❌ No tagged release.
- ❌ No essay establishing the *why* (single-flame vs fan-out, when to use which).
- ❌ Telegram + Linear defaults are presented as if every user has them. State the prerequisites explicitly and document substitutes.

## Plan — pre-launch

**Phase 1 — Add the operational layer (weeks 1–3):**

1. **Add an "Operationally" section to the README** (top, before the alchemy poem), with concrete mechanics:
   - "iksir runs a serialized work-queue for opencode sessions. Submit a recipe (formula). Workers execute one at a time on isolated branches. When a worker is blocked, you're called. When complete, the result is presented for transmutation into the codebase."
   - Concrete mechanics table: queue lives in SQLite at `~/.local/share/iksir/sijill.db`; branches named `iksir/<recipe-hash>`; default channel: Telegram; default sahifa: Linear.
   - Prerequisite checklist: Deno, opencode, Telegram bot token (or substitute), Linear API key (or substitute).
2. **Record the demo.** ~60–90 seconds: submit a recipe, see worker pick it up, watch it block on a question, principal answers via Telegram, worker resumes, completion notice.
3. **Tagged release.** `v0.1.0`.
4. **README screenshots.** At least: Sijill register row (anonymized), Telegram conversation snippet, Kitab al-Hikmah entry.

**Phase 2 — Seed (weeks 4–6):**

5. **`adils.me` essay:** *"Why I built a single-flame alchemy tool when everyone else is going parallel."* The essay establishes iksir's philosophical position (single-piece-flow vs fan-out) and gives the cultural framing room to breathe in long-form. Cross-link to the operational section of the README.
6. **`awesome-opencode` PR.**
7. **Selective sharing in opencode community channels.** No broad social yet — let the right audience find it.
8. **Cross-link from `clwnd`'s eventual launch material** as the philosophical opposite. ("If `clwnd` is a fleet, iksir is a single furnace. Pick the one that matches your work.")

**Phase 3 — Promote to T2 (when ready):**

When operational README + demo + essay are public + at least one external user has installed and reported back, the project graduates to T2 with the launch checklist.

## What we are *not* doing

- **Not flattening the alchemical register.** The vocabulary is part of what the project *is*, and the audience that responds to it is the audience that will actually use the tool well. The fix is *adding* an operational tier, not *replacing* the mythic one.
- **Not co-launching with `clwnd`.** They'll cannibalize each other's attention. Stagger.
- **Not pitching it on Show HN with the mythic-register README as the landing.** The HN audience will dismiss it without reading the operational substance. Ship the operational tier *first*; HN is plausible afterward.
- **Not pushing it on `/r/programming`.** Hostile audience for tools with strong aesthetic positioning.

## Metrics

T3 readiness gates from `metrics.md`. Project-specific watchpoint: **whether the operational README addition produces inbound from cold-readers.** A measurable change in install-script-hits before/after the operational tier is added is the cleanest signal that the README change worked.

## Launches

*(none yet — blocked on operational README + demo)*

## Notes

iksir is the project most at risk from the gap between *how it presents* and *what it does*. The fix is not to dilute the presentation; it is to add an operational tier that lets cold readers earn their way into the alchemical layer. Once that's done, the mythic vocabulary becomes an *asset* (memorable, distinctive, cite-able) rather than a friction.
