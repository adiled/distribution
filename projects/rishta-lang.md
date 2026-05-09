# rishta-lang

**Tier:** T3 (Ambitious / pre-launch) • **Repo:** https://github.com/adiled/rishta-lang (renamed from `bhai-lang`) • **License:** TBD • **Stars:** 0

## What it is

A **Karachi-native programming language with cryptographically verifiable data lineage as a first-class primitive.** Two interpreters live in the repo:

- `bhai.py`. A conventional tree-walking interpreter with Urdu keywords (variables, functions, closures, control flow, lists, built-ins, four stdlib modules: file IO, time, math, regex).
- `rishta.py`. The **رشتہ primitive runtime**, where every value is a `کردار` carrying its lineage, consent cascades automatically through descendants, PII leaks are queryable as `رساؤ`, and every claim is a SHA-256 Merkle chain (`ثبوت`) over the lineage graph.

The novel contribution lives in `rishta.py`: kinship-typed lineage edges (`استاد` for teacher to student or training data to model, `سوتن` for fallback parent or COALESCE, `گواہ` for read-only audit snapshot, `جوڑ`/`بیوی`/`شوہر`/`سمدھی` for JOIN-with-kinship-semantics, `دشمن` for adversarial cascade, `لے_پالک` for externally imported, `رضاعی` for cached/mirrored). The README (correctly) claims this is **about 50× richer than the 3-edge ontology of OpenLineage / DataHub / Marquez.**

Crypto layer (v0.9): `گواہی` cryptographic testimony. The lineage is mathematically demonstrable to a third party without trusting the vendor.

## Why it matters to the portfolio

This is a **dual-arc project**:

1. **Cultural/aesthetic arc.** A serious programming language written in Urdu script, rooted in Karachi vocabulary. This appeals to a culturally-distinct audience that no agent-systems project in the portfolio currently reaches.
2. **Technical/governance arc.** Data lineage with cryptographic verification, kinship-rich edge ontology. Directly relevant to GDPR / CPRA right-to-delete, ML feature provenance, AI agent auditability, healthcare/finance audits.

Either arc could carry the project on its own. Together they make it the most **intellectually distinctive** project in the portfolio.

## Naming: resolved

Originally shipped as `bhai-lang`, which collided with the well-known Hindi-keyword joke language (`DulLabs/bhai-lang`, about 14k stars on GitHub). The collision was a search-engine and discovery problem.

**Resolution.** The repo has been renamed to `rishta-lang`. The README front-matter now leads with `# رشتہ Rishta`, the body uses Rishta as the public name, and a footnote disambiguates from the original project. The `rishta-` framing foregrounds the novel contribution (the رشتہ primitive runtime) rather than the language-with-Urdu-keywords surface, and it disambiguates cleanly from the joke language without competing against it.

**Trade-off accepted.** The cultural identity (`بھائی-lang`, Karachi-Urdu vocabulary) is no longer in the project name. It still lives in the source code, the keyword set, and the README's voice. Distribution copy can lean on the cultural register where it matters (the cultural-arc essay, talks, Urdu-language community outreach) without the project name carrying it.

**No downstream to align.** No PyPI publication yet, no console-scripts shipped, no awesome-list entries, no external mentions. Future artifacts use `rishta-lang` and `rishta` from the start. There is nothing to clean up because nothing has been published under the old name.

## Audience

- **Cultural arc.** Urdu-speaking developers, language-preservation enthusiasts, esolang communities, programming-language theorists interested in non-Latin-script designs.
- **Technical arc:**
  - GDPR / CPRA practitioners. Lawyers and engineers responsible for delete cascades.
  - Data lineage and observability community (OpenLineage, DataHub, Marquez users, Monte Carlo / Acryl orbits).
  - ML governance audiences. AI training data provenance, feature lineage.
  - AI agent auditability discourse. Overlaps with the broader agent-systems work.
  - Academic PL community. Kinship-typed lineage is a publishable contribution if framed correctly.

## Distribution state: what's done

- ✅ Repo renamed to `rishta-lang`. Naming-collision blocker resolved.
- ✅ Repo description updated on GitHub. Reads as a Karachi-native programming language for cryptographically verifiable data lineage.
- ✅ README front-matter leads with `# رشتہ Rishta`. Disambiguation footnote points to `DulLabs/bhai-lang` so search-engine confusion does not compound.
- ✅ Thorough README: keywords table, operators, built-ins (organized by stdlib module), example programs, value model, propagation rules, primitives table, persistence, cryptographic testimony.
- ✅ `examples/` directory with named example programs (factorial, hello, rishta_leak, rishta_gdpr, rishta_modules, rishta_save, rishta_load, rishta_proof, rishta_typed, stdlib_demo).
- ✅ Roadmap published with version history.
- ✅ Comparative claim against existing tools (OpenLineage / DataHub / Marquez ontology size). Gives the technical audience a mental landmark.

## Distribution state: gaps

- ❌ **License TBD.** Hard blocker. Recommended: **Apache-2.0** (the runtime is intended to be embedded per the v0.10 roadmap entry). Tracked as issue [#1](https://github.com/adiled/rishta-lang/issues/1).
- ❌ Not on PyPI. The technical audience expects `pip install rishta` (or similar) as a credible install path.
- ❌ No published JSON schema for the persisted lineage graph. The README mentions "auditors can inspect it without بھائی installed." Publishing a documented schema turns that claim into something a non-user can engage with.
- ❌ No CLI binary entry point. `python3 rishta.py file.bhai` is fine for early users. `rishta file.bhai` via a console-script is what an installer expects.
- ❌ No essay establishing the technical arc. The README is dense reference material. An essay framing *why kinship is the right shape for lineage* is the artifact that lands with the lineage and governance audiences.
- ❌ No demo content. Both arcs benefit dramatically from showing the language *running* (a `rishta_gdpr.bhai` demo where revoke and cascade visibly hide downstream values is the killer demo).

## Plan: pre-launch (in this order)

**Phase 0: Resolve the remaining blocker (week 1):**
1. **Choose the license.** Recommendation: Apache-2.0. Tracked as issue [#1](https://github.com/adiled/rishta-lang/issues/1).
2. **Add `LICENSE` file** at the repo root.

**Phase 1: Distill (weeks 2 to 5):**

3. **Record the killer demo.** About 90 seconds, asciinema or video, showing a GDPR cascade: declare a sensitive data tree, run a `تپکا` revoke, watch every descendant cascade to `دو نمبری`, attempt to read the report and see the value hidden. This is the demo.
4. **Write the technical-arc essay** for `adils.me`. Title direction: *"Why data lineage needs kinship: the رشتہ primitive."* The essay positions the project against OpenLineage/DataHub/Marquez, presents the kinship-typed contribution, and links to the demo.
5. **Write the cultural-arc essay** for `adils.me`. Title direction: *"Programming in Karachi Urdu: the language behind rishta-lang"*. This essay can be much shorter. Its purpose is to establish that the language is a serious work of cultural practice, not a meme. The fact that the project name no longer carries the Urdu identity is itself part of the story this essay can tell.

**Phase 2: Seed (weeks 6 to 9):**

6. **PyPI publish** with both interpreters as console-scripts (`bhai` for the conventional interpreter, `rishta` for the runtime).
7. **Submit the technical-arc essay** to Latent Space, Console.dev, and the OpenLineage community Slack or forum. The OpenLineage submission is the highest-yield. The audience is *exactly the right shape*.
8. **Submit the cultural-arc essay** to Urdu/Pakistani tech communities (specific groups vary, see `inbox/`).
9. **Esolangs.org entry.** Even though the language is not exactly an esolang, the new-language indexes pick up entries here.
10. **Academic submission** *if appropriate*. The kinship-typed lineage contribution may be publishable at a workshop (PLATEAU, PLDI workshops, OOPSLA workshops). Lower urgency than the essays. Pursue only if the essays land cleanly.

**Phase 3: Promote to T2:**

When (a) the license is set, (b) the technical-arc essay is published with at least one third-party reference (OpenLineage thread, blog response, podcast mention), and (c) PyPI is shipping, the project graduates to T2 with the launch checklist from `policy.md` §6.

## What we are *not* doing

- **Not relitigating the rename.** The decision is made. Public copy goes forward as `rishta-lang`.
- **Not splash-launching as "an Urdu programming language."** That undersells the contribution and pulls drive-by traffic that won't engage with `rishta.py`.
- **Not entering Hacktoberfest-style "fun language" lists.** They diminish the technical claim.
- **Not over-claiming** the cryptographic guarantees in marketing copy. The README is precise. Keep it precise everywhere.

## Metrics

Per `metrics.md` T3 readiness gates. Project-specific signal to watch: **whether the technical-arc essay produces inbound from the lineage and observability community.** That inbound is the single highest-fidelity signal that the kinship-typed contribution is landing as intended.

## Launches

*(None yet. Now blocked only on license.)*

## Notes

The rename narrows the cultural-arc audience and sharpens the technical-arc audience. The cultural framing still ships through the source code and the keyword set, so a reader who arrives at the README finds the Urdu vocabulary intact. The project name itself is now doing different work: foregrounding the novel contribution (`rishta` as the unit of lineage) rather than the cultural register. This is a defensible trade-off and the essays should explain it explicitly rather than pretend it didn't happen.
