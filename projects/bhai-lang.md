# bhai-lang

**Tier:** T3 — Ambitious / pre-launch • **Repo:** https://github.com/adiled/bhai-lang • **License:** TBD (per README) • **Stars:** 0

## What it is

A **Karachi-native programming language with cryptographically verifiable data lineage as a first-class primitive.** Two interpreters live in the repo:

- `bhai.py` — a conventional tree-walking interpreter with Urdu keywords (variables, functions, closures, control flow, lists, built-ins, four stdlib modules: file IO, time, math, regex)
- `rishta.py` — the **رشتہ primitive runtime**, where every value is a `کردار` carrying its lineage, consent cascades automatically through descendants, PII leaks are queryable as `رساؤ`, and every claim is a SHA-256 Merkle chain (`ثبوت`) over the lineage graph

The novel contribution lives in `rishta.py`: kinship-typed lineage edges (`استاد` for teacher → student / training data → model, `سوتن` for fallback parent / COALESCE, `گواہ` for read-only audit snapshot, `جوڑ`/`بیوی`/`شوہر`/`سمدھی` for JOIN-with-kinship-semantics, `دشمن` for adversarial cascade, `لے_پالک` for externally imported, `رضاعی` for cached/mirrored). The README (correctly) claims this is **~50× richer than the 3-edge ontology of OpenLineage / DataHub / Marquez.**

Crypto layer (v0.9): `گواہی` cryptographic testimony — the lineage is mathematically demonstrable to a third party without trusting the vendor.

## Why it matters to the portfolio

This is a **dual-arc project**:

1. **Cultural/aesthetic arc.** A serious programming language written in Urdu script, rooted in Karachi vocabulary. This appeals to a culturally-distinct audience that no agent-systems project in the portfolio currently reaches.
2. **Technical/governance arc.** Data lineage with cryptographic verification, kinship-rich edge ontology. Directly relevant to GDPR / CPRA right-to-delete, ML feature provenance, AI agent auditability, healthcare/finance audits.

Either arc could carry the project on its own. Together they make it the most **intellectually distinctive** project in the portfolio.

## The naming-collision problem

There is an existing, well-known `bhai-lang` — a **Hindi-keyword joke language** with ~14k stars on GitHub. It is fundamentally different (Hindi devanagari script, comedic intent, no novel runtime contribution), but the name collision is a **search-engine and discovery disaster** if not addressed:

- Anyone searching "bhai-lang" finds the famous one first.
- Mentions, awesome-list submissions, and search-result clicks will be diluted by the name conflict.
- The user's project is meaningfully different (Urdu script, lineage-typed, GDPR-relevant); flattening into "another bhai-lang" is a disservice to its actual contribution.

**Three resolution paths, in decreasing order of recommendation:**

1. **Rename to `بھائی-lang`** (the Urdu rendering, already used in the README header) and treat `bhai-lang` as the repository slug only. Public-facing references everywhere — landing pages, awesome-lists, X bio, talks — use `بھائی-lang`. This honors the cultural origin and disambiguates cleanly.
2. **Rename to a `rishta-` family** (e.g. `rishta-lang`, `rishta`). The رشتہ primitive *is* the contribution; naming the project after it foregrounds the novelty rather than the language-with-Urdu-keywords framing. Trade-off: loses the cultural identity that makes the cultural-arc audience care.
3. **Keep `bhai-lang`** but lead every external surface with a hard subtitle: *"بھائی-lang — a kinship-typed lineage language (not the Hindi keyword variant)"*. Cheapest option but means living with diluted search results forever.

**Distribution recommendation:** option 1 (`بھائی-lang`). Honors the project's actual identity, disambiguates structurally, and reframes "bhai-lang" as legacy slug — not the public name.

This decision is a **hard precondition for any T2 promotion.** Track in `ASKS.md`.

## Audience

- **Cultural arc:** Urdu-speaking developers, language-preservation enthusiasts, esolang communities, programming-language theorists interested in non-Latin-script designs.
- **Technical arc:**
  - GDPR / CPRA practitioners — lawyers and engineers responsible for delete cascades.
  - Data lineage / observability community (OpenLineage, DataHub, Marquez users; Monte Carlo / Acryl orbits).
  - ML governance audiences — AI training data provenance, feature lineage.
  - AI agent auditability discourse — overlaps with the broader agent-systems work.
  - Academic PL community — kinship-typed lineage is a publishable contribution if framed correctly.

## Distribution state — what's done

- ✅ Comprehensive README: keywords table, operators, built-ins (organized by stdlib module), example programs, value model, propagation rules, primitives table, persistence, cryptographic testimony.
- ✅ `examples/` directory with named example programs (factorial, hello, rishta_leak, rishta_gdpr, rishta_modules, rishta_save, rishta_load, rishta_proof, rishta_typed, stdlib_demo).
- ✅ Roadmap published with version history.
- ✅ Comparative claim against existing tools (OpenLineage / DataHub / Marquez ontology size) — gives the technical audience a mental landmark.

## Distribution state — gaps

- ❌ **License TBD.** Hard blocker. Recommended: **Apache-2.0** (patent grant matters for cryptographic + lineage work; downstream embedders need clarity).
- ❌ **Naming collision unresolved.** Hard blocker. See "The naming-collision problem" above.
- ❌ Not on PyPI. The technical audience expects `pip install <name>` as a credible install path.
- ❌ No published `rishta` JSON schema for the persisted lineage graph. The README mentions "auditors can inspect it without بھائی installed" — publishing a documented schema turns that claim into something a non-user can engage with.
- ❌ No CLI binary entry point. `python3 rishta.py file.bhai` is fine for early users; `bhai file.bhai` (or `rishta file.bhai`) via a console-script is what an installer expects.
- ❌ No essay establishing the technical arc. The README is dense reference material; an essay framing *why kinship is the right shape for lineage* is the artifact that lands with the lineage / governance audiences.
- ❌ No demo content. Both arcs benefit dramatically from showing the language *running* (a `rishta_gdpr.bhai` demo where revoke→cascade visibly hides downstream values is the killer demo).

## Plan — pre-launch (in this order)

**Phase 0 — Resolve the blockers (weeks 1–2):**
1. **Decide the rename.** Recommendation: `بھائی-lang` as the public name; `bhai-lang` remains repo slug.
2. **Choose the license.** Recommendation: Apache-2.0.
3. **Add `LICENSE` file. Update README front-matter** with the public name and the disambiguation subtitle.

**Phase 1 — Distill (weeks 3–6):**

4. **Record the killer demo.** ~90s asciinema or video showing a GDPR cascade: declare a sensitive data tree, run a `تپکا` revoke, watch every descendant cascade to `دو نمبری`, attempt to read the report and see the value hidden. This is the demo.
5. **Write the technical-arc essay** for `adils.me`. Title direction: *"Why data lineage needs kinship: the رشتہ primitive."* The essay positions the project against OpenLineage/DataHub/Marquez, presents the kinship-typed contribution, and links to the demo.
6. **Write the cultural-arc essay** for `adils.me`. Title direction: *"Programming in Karachi Urdu: بھائی-lang"*. This essay can be much shorter; its purpose is to establish that the language is a serious work of cultural practice, not a meme.

**Phase 2 — Seed (weeks 7–10):**

7. **PyPI publish** with both interpreters as console-scripts (`bhai`, `rishta`).
8. **Submit the technical-arc essay** to Latent Space, Console.dev, and the OpenLineage community Slack/forum. The OpenLineage submission is the highest-yield: the audience is *exactly the right shape*.
9. **Submit the cultural-arc essay** to Urdu/Pakistani tech communities (specific groups vary; track in ASKS).
10. **Esolangs.org entry.** Even though the language is not exactly an esolang, the new-language indexes pick up entries here.
11. **Academic submission** *if appropriate*: the kinship-typed lineage contribution may be publishable at a workshop (PLATEAU, PLDI workshops, OOPSLA workshops). Lower urgency than the essays; pursue only if the essays land cleanly.

**Phase 3 — Promote to T2:**

When (a) the rename is done, (b) the license is set, (c) the technical-arc essay is published with at least one third-party reference (OpenLineage thread, blog response, podcast mention), and (d) PyPI is shipping — the project graduates to T2 with the launch checklist from `policy.md` §6.

## What we are *not* doing

- **Not pretending the rename problem doesn't exist.** Launching with the colliding name and hoping people figure it out is the worst option.
- **Not splash-launching as "an Urdu programming language."** That undersells the contribution and pulls drive-by traffic that won't engage with `rishta.py`.
- **Not entering Hacktoberfest-style "fun language" lists.** They diminish the technical claim.
- **Not over-claiming** the cryptographic guarantees in marketing copy. The README is precise; keep it precise everywhere.

## Metrics

Per `metrics.md` T3 readiness gates. Project-specific signal to watch: **whether the technical-arc essay produces inbound from the lineage / observability community.** That inbound is the single highest-fidelity signal that the kinship-typed contribution is landing as intended.

## Launches

*(none yet — blocked on rename + license)*

## Notes

This project is **simultaneously** the most culturally distinctive and the most technically novel in the portfolio. It is also the one most at risk of being misread because of a name. Get the rename right and the rest of the distribution path is straightforward; get it wrong and every other piece of work is fighting headwinds.
