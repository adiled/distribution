# rishta-lang

**Tier:** T3 (Ambitious / pre-launch) • **Repo:** https://github.com/adiled/rishta-lang • **License:** TBD (Apache-2.0 recommended) • **Stars:** 0 • **Latest release:** none tagged

## What it is

rishta-lang is a research-grade programming language whose runtime substrate is `رشتہ` (kinship), so every value is a `کردار` (character/entity) carrying its lineage, every operation extends a typed kinship graph, every revocation cascades, and every claim about the graph is content-addressable through a Merkle chain the maintainer calls `گواہی` (testimony, witness-bearing).

The repository ships two interpreters that share a lexer architecture and module system. `bhai.py` is the general-purpose dialect with `سن` for variables, `کام` for functions, `جبتک` and `ہرایک ... میں` for loops, `کلٹی` for return, closures, lists, four standard library modules (`فائل` for file IO, `وقت` for time, `حساب` for math, `جال` for regex), and a 19-builtin surface across them. `rishta.py` is the lineage-native dialect, no control flow, programs are sequences of declarations and queries against the kinship graph. The novel contribution lives in `rishta.py` (1005 lines of Python, no third-party dependencies). `bhai.py` is the substrate that proves the lexer and the keyword vocabulary work as a real language, not a sketch.

The kinship vocabulary is structural, not decoration. `کردار` is the value. `شجرہ` is the lineage tree. `تپکا` is the revoke verb (Karachi street idiom for "got hit, fell out"). `سوتن` (co-wife) is the fallback parent, the relationship `COALESCE` and A/B fall-through have no name for in any data-systems tool. `سمدھی` (co-in-laws) is the relationship between two source tables that have a shared join descendant. `استاد` (teacher) is the training-data-to-model edge. `گواہ` (witness) is a read-only audit snapshot. `جوڑ ... سے ... بنا` (joining a with b makes c) is the JOIN primitive that constructs `بیوی` and `شوہر` parent edges from result to sources. `دشمن` (enemy) is the adversarial mark that cascades to descendants. `لے_پالک` (adopted) is an externally-imported value with no native lineage. `رضاعی` (foster) is a cached or mirrored copy. `رساؤ` (leak) and `دشمن_رساؤ` (enemy leak) are the two PII / poisoning audit queries. `ثبوت` (proof) prints the SHA-256 over the lineage. `سنبھال` and `اٹھا` (save and pick up) are the persistence primitives, and `اٹھا` verifies every `گواہی` on load and aborts on tamper with the offending name.

The README's `Status` section is precise: "experimental", "the novelty lives in `rishta.py`". The paper in `paper/` is an actual PDF (compiled from a Typst source and a maintained LaTeX twin), 7 pages, abstract through related work, with venue targets named (`TaPP` workshop first, then PLDI / OOPSLA, then SIGMOD / VLDB, then CCS for the cryptographic-testimony angle). The compile path documents that tectonic 0.16+ segfaults on Nastaliq shaping on macOS Tahoe and steers the user to MacTeX or Typst, which is the sort of operational detail that signals the maintainer actually compiled the paper on their own machine.

## Why it matters to the portfolio

rishta-lang is the portfolio's first project that makes a serious claim in two unrelated audiences at once. The agentic-substrate thesis the rest of the portfolio is making (clwnd, ccft, opencode-dir, iksir) sits adjacent to but does not include programming-language design and data-systems research. rishta-lang opens that lane.

The cultural register and the technical claim are not separable. The paper's central argument is that the data-lineage vocabulary in OpenLineage, DataHub, and Marquez is structurally insufficient because it has three to thirteen edge types, and that Karachi Urdu kinship vocabulary supplies the missing distinctions because it carries roughly fifty relationship terms that English data-systems language has no equivalents for. The thesis is "lineage is not an annotation problem; it is a vocabulary problem solved by making derivation relationships first-class". A distribution that flattens the Urdu vocabulary into "Urdu-flavored programming language" loses the argument. A distribution that flattens it into "kinship-typed lineage" without showing the actual `سوتن`, `سمدھی`, `استاد` edges loses the same argument. The vocabulary is the contribution.

The portfolio's other long-vocabulary projects (clwnd's botanical register, iksir's alchemical register) make the same constitutional move at different scales. rishta-lang is where the move is most defensible because the vocabulary is doing computational work: each edge has a propagation rule, each query walks a specific edge subset, each tag enters the SHA-256 input. The English dossier explains the system. It does not erase it.

## Audience

Three kinds of people will pick this up and recognize themselves in it. The cultural and technical arcs do not share an audience and do not need to.

The first is the data-lineage and data-governance practitioner who has run a GDPR delete cascade by hand and has answered the question "did this feature derive from sensitive data" by code review. This person knows OpenLineage, DataHub, Marquez, Atlan, Monte Carlo, and Acryl. They have read at least one PROV-DM paper. The pitch is the four case studies in the paper: GDPR cascade, ML feature PII leak detection, JOIN provenance via `سمدھی`, tamper detection via `گواہی`. They will care that the kinship vocabulary types relationships their existing tools cannot name. They will not care that the keywords are in Urdu script until they sit with the examples for ten minutes, at which point the cultural register becomes part of why the design is interesting.

The second is the programming-language researcher who reads PLATEAU, OOPSLA, PLDI, and HOPL. This person has seen Qalb (Ramsey Nasser, 2012), UrduScript, and the broader non-Latin-script PL lineage. They will engage with the contribution as the formal claim it is: a kinship-typed edge ontology, a Merkle-chained value-level provenance protocol, and a small reference implementation that runs the four case studies end to end. The paper is the artifact for them. The TaPP workshop is the venue.

The third is the Karachi or Urdu-reading developer who has watched English-keyword languages flatten every other technical surface they touch and recognizes that this language is not a joke. They will register `تپکا` as the Karachi street verb it is, `سن` and `کام` as the unforced Urdu they are, and `پھوٹ` as exactly the right register for "say it out loud". The pitch for them is the source code itself, then the cultural-arc essay if it gets written. This audience is small, sharp, and disproportionately likely to forward the project to the second audience above.

The audience that does not overlap cleanly with any of the three: the esolang novelty crowd. Esolang readers will install the language for the keyword surface, tweet a fizzbuzz, and move on. The contribution is in `rishta.py`'s lineage runtime and in the paper. Pitching to esolangs.org pulls drive-by traffic that bounces off the rest. The right channel for this project is the data-lineage community and the PL workshop circuit, not Brainfuck-adjacent indexes.

## Distinguishing claim

Two claims, both defensible from the code and the paper.

The first claim is value-level lineage as runtime substrate, not as instrumentation. Every `کردار` carries `parents`, `children`, `consent`, `trust`, `sensitivity`, `dushman`, and `origin` as first-class fields. Every binary operation in `rishta.py`'s `_e_Binary` constructs a child `کردار` and links it to both operands as `باپ` and `ماں`, propagates trust as max, propagates sensitivity and adversarial as OR, and marks the consent state as `دو نمبری` (tainted) if either parent is non-VIP. No comparable language treats lineage as the value model rather than as an annotation layer the runtime can ignore.

The second claim is the kinship-typed edge ontology. The implementation currently exposes about ten user-assertable family relations (`باپ`, `ماں`, `بیٹا`, `بیٹی`, `بہن`, `چچا`, `ماموں`, `دادا`, `نانا`, `والد`) for the `رشتہ:` declarative-edge form, plus six interpreter-set typed edges (`سوتن`, `استاد`, `گواہ`, `بیوی`, `شوہر`, `رضاعی`) and three node-level marks (`دشمن`, `لے_پالک`, `سی نسٹیویٹی`). The paper claims roughly fifty distinct edge types in Karachi Urdu kinship vocabulary and argues that this is the right ceiling. The current ceiling in code is closer to sixteen typed edges. That gap is honest and is named in the roadmap; the claim that the vocabulary is structurally richer than OpenLineage's three is already true at sixteen, and the contribution holds.

A third claim worth flagging but currently aspirational: cryptographic testimony makes lineage verifiable without trusting the producer. The implementation in `_hash_kirdaar` is sound (deterministic SHA-256 over canonical-JSON encoding of value, tags, sorted parent hashes), the `سنبھال` / `اٹھا` round trip verifies correctly on tampered input, and the paper's threat model is precise about what tamper-evidence catches and what it does not (omission is not caught, external anchoring is the open problem). This claim becomes a load-bearing distinguishing claim once a second implementation exists or once the format is adopted by anyone outside this repo.

## Distribution state, current

The repository is sharper than the prior dossier credited on the language and paper dimensions, and as blocked as the prior dossier said on the license dimension.

The rename from `bhai-lang` to `rishta-lang` has fully landed at the GitHub level, in the README front-matter (now `# رشتہ Rishta`), in the paper title (`Rishta: A Karachi-Native Programming Language for Cryptographically Verifiable Data Lineage`), in the paper source filenames (`rishta.tex`, `rishta.typ`, `rishta.pdf`), and in the install snippet (`git clone git@github.com:adiled/rishta-lang.git`). Issue #2 (rename downstream cleanup) is closed and is correctly closed. The disambiguation against DulLabs/bhai-lang lives in the README as a single blockquote and was deliberately removed from the paper on 2026-05-09 (commit `806be05`) on the grounds that academic reviewers will not have heard of the toy project and a defensive comparison signals naming anxiety the artifact should not carry. That editorial choice is correct.

The README is 338 lines, well-organized, and reads as a serious language reference: status, requirements, install and run, hello-world, a `bhai.py` keyword table, an operators paragraph, four built-in tables (one per stdlib module), an example each for recursion and closures, then a full `رشتہ primitive` section covering value model, propagation rules, declaration, automatic lineage, primitives, modules, cryptographic testimony, typed-edge primitives, adversarial / external / cached primitives, GDPR cascade example, and PII leak detection example. The roadmap names every shipped version with `Done` and the two open ones (v1.0 bytecode VM, v2.0 Rust runtime + LLVM backend, v0.6 deferred REPL).

The `examples/` directory has fourteen named programs covering the language surface: `hello`, `factorial`, `fizzbuzz`, `closure`, `lib_pii`, `rishta_leak`, `rishta_gdpr`, `rishta_typed`, `rishta_v07`, `rishta_modules`, `rishta_save`, `rishta_load`, `rishta_proof`, `stdlib_demo`. The rename commit's message claims all eleven examples still pass after the em-dash eradication pass; the examples directory now has fourteen, suggesting three more landed alongside the v0.7 / v0.8 / v0.9 work.

The paper is real. `paper/rishta.pdf` is compiled and current. The Typst source is the recommended path with a single 50MB binary install. The LaTeX source is maintained for venue submission compatibility. The `paper/README.md` documents both compile paths, names the Noto Nastaliq Urdu font dependency, and warns about the tectonic 0.16+ Nastaliq shaping segfault on macOS Tahoe. Target venues are named in priority order. Open work is enumerated honestly (author ORCID, formal hash-property theorems, larger-scale evaluation, deeper PASS-line related work, copy-edit pass).

Personal validation: the maintainer wrote 1003 lines of `bhai.py` and 1005 lines of `rishta.py`, both compile and run, and has compiled the paper to PDF on their own machine. The interpreter handles Urdu identifiers, Urdu digit literals (`۰` to `۹`), Urdu punctuation (`،` for comma, `۔` for semicolon), and Urdu identifier composition (`_` plus alpha plus Urdu digits). This is more substantial than a sketch.

## Distribution state, gaps

License is the single hard blocker. README declares "TBD". Issue #1 (Add LICENSE, Apache-2.0 recommended) is open and is correctly open. The recommendation is Apache-2.0 because the v0.10 roadmap explicitly intends to embed the رشتہ runtime in existing Python codebases ("compile-to-Python: embed the رشتہ runtime in existing Python codebases"), and Apache-2.0's patent grant earns its weight when downstream code embeds a runtime. This is the only readiness item that is genuinely not done.

The GitHub repo description on `adiled/rishta-lang` currently uses an em dash between "Rishta" and the tagline. The em dash is a typography violation per the maintainer's own house rule (the rename commit `4ae1eea` had "eradicate em dashes" in its title). The fix is a one-second `gh repo edit --description "Rishta. A Karachi-native programming language for cryptographically verifiable data lineage"` (period, not em dash) before the launch.

GitHub topics are unset. Suggested set, in order of pull: `programming-language`, `data-lineage`, `data-provenance`, `gdpr`, `urdu`, `kinship-typed`, `merkle-tree`, `interpreter`, `python`. These pull from both audiences without conflating them.

Not on PyPI. The technical audience expects `pip install rishta` (or `pip install rishta-lang`) as a credible install path, and the rename commit's downstream-cleanup checklist named `rishta` as the runtime console-script and `bhai` as the conventional-interpreter console-script. Both names need an availability check on PyPI before publish. A `pyproject.toml` with two `[project.scripts]` entries (`rishta = "rishta:main"`, `bhai = "bhai:main"`) and a small `__init__.py` shim are the substantive packaging work; the rest is metadata.

No tagged releases on GitHub. Twelve commits, no `git tag`, no GitHub release. The version history in the README's roadmap ("v0.3 ... done, v0.4 ... done") is implicit and not anchored to commit hashes a downstream consumer can pin. A first tagged release should ship the language as it stands today (v0.9 cryptographic testimony, the four propagation rules, the seventeen-or-so typed edges) with hand-written notes that name the surface area.

No `adils.me` essay yet. Per `policy.md` §6 the post is the launch's primary artifact. Two essays are useful here, written in different registers for different audiences, but the technical-arc essay is the one that has to ship before any submission to TaPP, OpenLineage Slack, or the PL workshop circuit. Title direction: "Why data lineage needs kinship: the رشتہ primitive". Open with one full GDPR cascade example. Show the `رساؤ` query catching a derived feature. Show the `گواہی` chain detecting a single-byte tamper. Close with the install one-liner once PyPI is live.

No demo recording. The single highest-yield artifact for both audiences is a 60-to-90-second screen capture of the GDPR cascade running: declare a sensitive `صارف`, derive `پروفائل` and `رپورٹ` from it, run `تپکا صارف`, watch every descendant cascade to `دو نمبری`, attempt a `پھوٹ رپورٹ` and see the value hidden. asciinema or vhs both work. The visual is the argument because nothing in any other language does this.

The "~50 edges" claim in README and paper is not yet matched by the implementation. The implementation has roughly ten user-assertable family relations plus six interpreter-set typed edges, totalling about sixteen distinct typed edges. The claim is honest as a vocabulary count from natural Karachi Urdu and as a research aspiration; it is misleading as a description of the current runtime. Either the README and paper add a "implemented now / vocabulary ceiling" sentence, or a v0.10 release expands the typed-edge set toward the paper's claim. The first option is faster and is what the launch should ship behind.

The MIT-vs-Apache-2.0 discussion in issue #1 is precise and decided, but the issue body still says `[ ]` next to the three action items. Closing the issue requires actually adding `LICENSE`, declaring the license in the README (replacing "TBD"), and noting the license in the eventual `pyproject.toml` packaging.

## Distribution state, aspirational

A second essay specifically on the cultural arc would do unusual long-tail work. The argument that Karachi Urdu's port-city creole vocabulary is computationally richer than English for relationship typing is the kind of thesis that lands in the academic-PL community on its own merits and lands in the Urdu / Pakistani / South-Asian tech press on its own merits, and the two pieces of attention compound. The maintainer's voice is well-suited to this register; the README's creole-positioning paragraph already does most of the work.

A workshop submission to TaPP (`USENIX` Theory and Practice of Provenance) is the highest-yield academic move available. The paper is implementation-driven, the venue is friendly to implementation-driven contributions, the case studies are exactly the venue's idiom, and a TaPP acceptance produces a citation surface that pulls into the OpenLineage and DataHub neighborhoods. The 2026 cycle is not yet announced; check the venue page directly when planning.

A `rishta-lineage-passport` JSON schema published as a separate small artifact, derived from the `سنبھال` output format, would let the format outgrow this repo. The audit JSON is already plain text and self-verifying; documenting it as a schema other implementations could emit turns the format into something a non-user can engage with. This is post-launch territory and is the substance for a follow-up post on inter-organization lineage hand-off.

A bytecode VM (v1.0 in the roadmap) is the right next implementation step because the case studies in the paper top out at four to ten nodes and academic reviewers will want ten thousand to one hundred thousand. A tree-walking interpreter at one hundred thousand nodes is on the wrong side of the demo line. The Rust runtime + LLVM backend (v2.0) is the right step after.

## Plan

Pre-launch, in order:

1. Resolve the license. Apache-2.0 per the embedding-intent argument in issue #1. Add `LICENSE` at the repo root, declare the license in the README's footer, close issue #1 with the commit hash.
2. Fix the GitHub repo description's em dash. `gh repo edit adiled/rishta-lang --description "Rishta. A Karachi-native programming language for cryptographically verifiable data lineage"`. One command.
3. Set GitHub topics. `gh repo edit adiled/rishta-lang --add-topic programming-language --add-topic data-lineage --add-topic data-provenance --add-topic gdpr --add-topic urdu --add-topic kinship-typed --add-topic merkle-tree --add-topic interpreter --add-topic python`. One command.
4. Reconcile the "~50 edges" claim with the current implementation. Two-sentence addition near the typed-edge primitives section: "The runtime currently exposes about sixteen distinct typed edges, drawn from the natural-language ceiling of roughly fifty Karachi Urdu kinship terms. The remainder are roadmap targets." Same paragraph in the paper's §4.
5. Verify the package names `rishta` and `rishta-lang` on PyPI. If both are available, claim both and publish under `rishta-lang` with `rishta` as the console-script. If `rishta` is taken, publish as `rishta-lang` and use `rishta-cli` as the console-script.
6. Cut a v0.9.0 tag with hand-written release notes that name the surface area in the language's own vocabulary and resolve to plain English at the bottom. Attach the compiled `rishta.pdf` as a release asset.
7. Record the GDPR cascade demo. 60 to 90 seconds, asciinema or vhs. The cinematic version of `examples/rishta_gdpr.bhai`.
8. Write the technical-arc essay for `adils.me`. Open with the demo. Show one full case study. Close with the install one-liner. Working title: "Why data lineage needs kinship: the رشتہ primitive".

Launch beats, in order, on a Tuesday or Wednesday morning US Pacific:

1. Publish the `adils.me` essay.
2. Submit the paper to the next TaPP cycle (check `https://www.usenix.org/conferences/byname/186` for the open call). The paper is already at workshop-grade length and the four case studies match the venue's idiom directly.
3. Drop the essay into the OpenLineage Slack (`openlineage.slack.com`, an open community Slack the OpenLineage project runs). Frame the post around the kinship-typed edge ontology and the GDPR cascade case study, not around the cultural register. The Slack audience is the closest concentrated form of the first audience named above.
4. Show HN with the essay URL. Title direction: "rishta-lang: a programming language where data lineage is the runtime". Not "Show HN: I made an Urdu language". The post is the artifact, the demo is the proof.
5. PR to one or two well-maintained PL indexes (the `learn-anything/programming-languages` list, the `ericspring08/Awesome-Programming-Languages` list). Skip `awesome-X` lists that read as joke-language directories.
6. Twitter / X thread of four to five posts: demo gif lead, then a single case-study screenshot (GDPR cascade or PII leak), then the propagation-rules paragraph, then the paper PDF link, then the install one-liner. Keep it short.
7. Submission to one or two Pakistani / South-Asian tech outlets (specific venues: Profit Pakistan, Dawn tech section, MIT Technology Review Pakistan if it still publishes). The cultural-arc essay (separate from the technical-arc essay) is the artifact for these venues.
8. Skip esolangs.org for this launch. It is the wrong audience and the wrong frame. Revisit only if the PL community pulls the project there organically.

Post-launch, first 30 days:

1. The "is this a joke language" misread will arrive within the first hour of any HN post. Have a one-paragraph response ready that points to the paper, the GDPR case study, and the comparative table of edge-type counts (3 in OpenLineage, 13 in PROV-DM, ~16 in the current Rishta runtime).
2. The "why Urdu specifically" question will arrive next. The paper's §8 cultural-framing paragraph already answers it: Karachi Urdu's port-city creole vocabulary is the most relationship-rich substrate the maintainer found, the design is downstream of the vocabulary not upstream of it, and other natural-language kinship systems (Tamil, Mandarin, Yoruba) would yield comparable insight. Have that paragraph saved.
3. The "how does this compare to OpenLineage" question is the most valuable to answer carefully, because the answer is the paper's central argument. Avoid being defensive. The frame is "OpenLineage is correct for what it is, an annotation layer over heterogeneous systems; Rishta is correct for what it is, a runtime where lineage is the value model. They are not in the same lane."
4. Watch for a single inbound from a named OpenLineage or DataHub maintainer. That signal is worth more than 100 stars from the cultural-arc audience. If it arrives, the project is over the T3-to-T2 promotion line on the audience-formation criterion.

## What we are not doing

Not pitching rishta-lang as an Urdu programming language. The contribution is the lineage runtime and the kinship-typed edge ontology. Leading with the keyword surface pulls drive-by traffic that does not engage with `rishta.py`, undersells the technical claim, and lands the project in the joke-language neighborhood the rename was deliberately moving away from.

Not pitching rishta-lang as an esolang. The audience overlap with the actual first and second audiences (data-lineage practitioners, PL researchers) is small. esolangs.org and the Brainfuck-adjacent indexes are the wrong venues even though the Urdu keyword surface looks like esolang material from a distance.

Not pitching rishta-lang as a successor to or replacement for OpenLineage / DataHub / Marquez. The lanes are different. Annotation-layer tools over heterogeneous production systems are not in competition with a runtime where lineage is the value model. The right frame is parallel, not displacing.

Not flattening the Karachi-Urdu vocabulary into transliteration in the artifacts. The vocabulary is computational. `سوتن`, `سمدھی`, `استاد`, `گواہ`, `جوڑ`, `بیوی`, `شوہر`, `رضاعی`, `لے_پالک`, `دشمن`, `تپکا`, `رساؤ`, `ثبوت`, `سنبھال`, `اٹھا`, `گواہی`, `کردار`, `شجرہ`, `دو نمبری`, `شاپنگ`, `VIP`, `جانی`, `بھائی`, `حساس` are all carrying their own propagation rules and query semantics in the runtime. The English distribution copy explains them. It does not transliterate them away.

Not relitigating the rename. The decision is made, the `rishta-lang` name is on GitHub, the paper, the README front-matter, the planned PyPI package, and the planned console-scripts. The cultural identity (`بھائی-lang`, the trust keyword `بھائی`, the source-code voice) lives in the language, not in the project name.

Not over-claiming the cryptographic guarantees. The README and paper are precise (tamper-evidence catches modification, not omission; external anchoring is the open problem; the threat model is integrity not confidentiality). Distribution copy stays at this level of precision. The cryptographic claim is strong enough that overstating it is the only way to weaken it.

## Metrics

Per `metrics.md` T3 readiness checklist (binary, not numeric).

| Item | Status |
|---|---|
| One-paragraph pitch a non-author would forward | Yes (the README's first three paragraphs, post-rename) |
| 30-second demo recorded | No |
| Install verified clean on a never-seen-it machine | Partial (the install path is `git clone` plus Python 3.8+; no PyPI verification yet) |
| First-user persona named (and ideally one real example acquired through private demo) | Not yet |
| Coordinated launch checklist from `policy.md` §6 fully green | No (license, GitHub description, topics, PyPI, demo, essay all open) |

When all five are checked the project promotes to T2 and the per-launch metrics table applies.

Project-specific signals to watch.

Inbound from the data-lineage community (a named OpenLineage / DataHub / Marquez maintainer engaging on the Slack drop, on the Show HN post, or in a follow-up issue). This is the single highest-fidelity signal that the kinship-typed contribution is landing as intended.

Paper citations. A workshop acceptance at TaPP plus one external citation within the first six months would put the project on the academic-PL tracking surface and pull in second-audience attention compounding over years.

Forks that touch the typed-edge set. A fork that adds three or more edges drawn from a different natural-language kinship vocabulary (Tamil, Mandarin, Yoruba, as the paper conjectures) would be the strongest possible signal that the conceptual move is portable, not specific to one creole.

## Launches

None executed yet. The current state is pre-rename-completion-plus-license-blocker.

## Notes

The prior dossier was correct on the structural shape and got several specific facts wrong. The DulLabs/bhai-lang star count is 4.1k, not 14k (verified on `https://github.com/DulLabs/bhai-lang` 2026-05-09). The "✅ Repo description updated" claim is true at the GitHub level but the description currently contains an em dash, which violates the maintainer's own house rule and was named in the same commit that performed the rename. GitHub topics are not set yet. Issue #1 is correctly still open; license is not yet resolved. Issue #2 is correctly closed; the rename downstream-cleanup is genuinely done at every level except PyPI publishing (which has not been attempted).

The "~50 edges" claim in the README and paper is more vocabulary aspiration than runtime fact. The current implementation has about sixteen distinct typed edges in `rishta.py`. The claim is defensible as a paper argument (the natural-language ceiling exists, and the contribution is showing that conventional tools cap at three to thirteen) but it is not yet defensible as "the runtime ships fifty edges". A two-sentence reconciliation in the README and paper closes this gap without weakening the contribution.

The two interpreters share a lexer architecture and a module system but are otherwise independent. `bhai.py` is the substrate that proves the keyword vocabulary is real and the lexer handles Urdu identifiers, digits, and punctuation. `rishta.py` is the novelty. Distribution copy should name `rishta.py` as where the contribution lives and `bhai.py` as the supporting general-purpose dialect, not the other way around.

The rename commit `4ae1eea` (2026-05-09) is the single most informative artifact in the repo's history. It performed the rename, eradicated em dashes across every source format (`.md`, `.py`, `.bhai`, `.tex`, `.typ`, `.bib`), switched paper authorship from legal name to handle (`adiled`), and rebuilt the README from scratch after a prior pass had emptied it. The follow-up commit `806be05` dropped the DulLabs disambiguation paragraph from the paper on the grounds that defensive comparison signals naming anxiety. Both edits are correct and both are visible in the file.

The paper is workshop-grade today. It is not yet conference-grade because the case studies are 4 to 10 nodes and academic conference reviewers will want larger-scale evaluation. TaPP is the right venue for the workshop-grade artifact; PLDI / OOPSLA / SIGMOD / VLDB are the right venues for the conference-grade artifact after the bytecode VM lands and the case studies scale.

## Research log

Sources consulted, in order:

- Local clone at `/Users/adil/bhai-lang` (the local directory still uses the old name; the remote is `adiled/rishta-lang`). README, `bhai.py` (1003 lines), `rishta.py` (1005 lines), `examples/` (fourteen `.bhai` files), `paper/README.md`, `paper/rishta.typ` (567 lines), `paper/rishta.tex` (384 lines), `paper/rishta.pdf` (compiled, 281 KB).
- Source code: the lexer keyword tables in both interpreters, the `Kirdaar` value model, the `_e_Binary` propagation logic in `rishta.py`, the `_hash_kirdaar` Merkle implementation, the `_x_LoadStmt` verification path, and the kinship-edge set actually exposed (`باپ`, `ماں`, `بیٹا`, `بیٹی`, `بہن`, `چچا`, `ماموں`, `دادا`, `نانا`, `والد`, `سوتن`, `استاد`, `گواہ`, `بیوی`, `شوہر`, `رضاعی`).
- Examples: `rishta_gdpr.bhai`, `rishta_proof.bhai`, `rishta_typed.bhai`, `stdlib_demo.bhai` to confirm the language reads as intended in practice.
- Git history: thirteen commits, all on `main`, from 2026-04-16 (`7c98ad6` initial) through 2026-05-09 (`806be05` paper disambiguation drop). Particular attention to `4ae1eea` (the rename and em-dash eradication pass) and `806be05` (the editorial decision to drop the DulLabs disambiguation from the paper).
- GitHub state via `gh repo view adiled/rishta-lang --json`. Description, license, topics, latest release, push date. Confirmed: rename landed, description contains em dash, no license, no topics, no releases.
- GitHub issues via `gh issue list --repo adiled/rishta-lang --state all`. Two issues. #1 (Add LICENSE) open and correctly so. #2 (Rename downstream cleanup) closed and correctly so.
- Existing dossier at `projects/rishta-lang.md` (read for shape and to fact-check its claims).
- `policy.md` (identity register, T3 launch checklist, voice rules), `channels.md` (rishta-lang section and PyPI / esolang / academic notes), `metrics.md` (T3 readiness checklist), `projects/_index.md` (portfolio context), `projects/ccft.md` and `projects/clwnd.md` (dossier shape and rigor reference), `asks.json` (existing asks against this project), `AGENTS.md` (voice contract).
- Web research scoped to the project's own claim. DulLabs/bhai-lang current star count (4.1k, verified). OpenLineage Slack as the open community Slack venue. TaPP USENIX workshop as the named provenance-research venue (2026 cycle not yet announced as of this dossier). Qalb (Ramsey Nasser, 2012) as the Arabic-script PL precedent. UrduScript as the prior Urdu PL precedent. PLATEAU 2026 workshop on programming-language usability and HCI as the secondary academic venue. PROV-DM and OpenLineage edge-type ontology size to verify the paper's "3 to 13 edges" comparison.

Sources rejected as imported-category framings:

- "Esolang" frame. The keyword surface looks like esolang material from a distance and the prior dossier flagged esolangs.org as a target. The contribution is the lineage runtime, not the keyword surface, and the audience for esolangs.org is fundamentally different from the audience for OpenLineage Slack. Submitting to esolangs.org pulls drive-by traffic that bounces off `rishta.py`.
- "Urdu programming language" frame. The cultural register is structural and matters, but leading with "Urdu programming language" lands the project in the UrduScript / Qalb neighborhood, which is a smaller and differently-shaped audience than the data-lineage community the paper is addressed to. Both audiences exist for this project; the technical-arc essay leads, the cultural-arc essay supports.
- "Joke language" / "fun" frame. The DulLabs/bhai-lang collision was the genesis of the rename precisely to avoid this frame. The disambiguation footnote in the README and the paper's previous (now removed) disambiguation are both careful about this. Public copy goes nowhere near "fun" or "joke".
- "Better OpenLineage" frame. OpenLineage is a correct annotation layer over heterogeneous production systems. Rishta is a runtime where lineage is the value model. They are parallel, not displacing. Pitching rishta-lang as a successor invites the wrong question (production deployment readiness) and skips the right one (is the kinship-typed value model the right shape).
- "Cryptographic data provenance" / "verifiable AI lineage" frame. The cryptographic-testimony layer is real and works, but it is one of three contributions, and leading with it pulls the project into the blockchain-and-Merkle-log neighborhood (Trillian, Certificate Transparency) where the audience is different again. The paper sequences correctly: kinship-typed edges first, then cryptographic testimony as the verifiability layer over them.

What surprised me in step 4, when writing the project's-own-voice description.

The maintainer's framing is more developed and more specific than the prior dossier credited. The README's creole-positioning paragraph is the right pitch in two sentences ("a port-city creole shaped by Mohajir, Sindhi, Punjabi, Memon, Gujarati, Marwari, Persian, and English contact ... about 50 distinct lineage relationships have natural names in this register that English data-systems vocabulary lacks"). The paper's central argument is sharper than the README's ("lineage is not an annotation problem; it is a vocabulary problem solved by making derivation relationships first-class"). Neither of those sentences appears in the prior dossier. The prior dossier flattened the contribution into "novel-language arc" and "data-lineage arc" as if they were two adjacent pitches; in the maintainer's own framing they are one argument with two audiences, and the argument is that the cultural register is computationally productive, not just authentically representational.

The other surprise was how operational the paper's compile path is. The Typst-vs-LaTeX choice with the explicit tectonic-segfault warning, the explicit MacTeX disk-size note (5GB), the named font dependency (Noto Nastaliq Urdu), and the per-venue retargeting note ("retarget from PLDI to TaPP if reviewers push back on novelty-vs-pragmatism") all signal that the maintainer has actually compiled the paper, considered the venues, and worked through the operational details. This is not a sketch. It is workshop-ready substance with a license blocker on the front and a PyPI publish on the back.

The prior dossier got two specific facts wrong worth flagging for the maintainer to close on the asks tracker: DulLabs/bhai-lang has 4.1k stars not 14k (the order-of-magnitude error mostly does not change the rename argument, but the rename argument is now even stronger because the collision is half what the dossier estimated), and the GitHub repo description currently violates the maintainer's own em-dash rule (this is a one-second `gh repo edit` fix and should ride along with the next push).
