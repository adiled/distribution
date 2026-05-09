# method.md

How to produce a sharp distribution dossier for any project in this portfolio.

This is not a launch playbook (that lives in `policy.md`). This is the prior step: how to *understand* a project well enough that anything written from the dossier reflects what the project actually is, not what a market category expects it to be.

Most distribution work fails at this layer. Someone reads a README, pattern-matches to a familiar category, and writes copy that fits the category. The project gets framed as a worse version of the category leader. The actual contribution disappears. The audience that would have valued the actual contribution never sees it.

This file is the antidote, written once so the same mistake doesn't get re-imported each time a dossier is opened.

---

## Order of operations

Run these in order. Skipping ahead is the failure mode.

### 1. Read the code first

Before reading the README, before any market analysis, look at the source.

- The directory tree tells you what the project is organized around.
- The dependency manifest (`package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`) tells you what the project leans on. Light dependencies are a posture. Heavy dependencies are a posture.
- The entry point shows what is actually exposed to a user, as opposed to what is documented.
- A skim of the largest modules reveals what the project actually does.
- Tests reveal what the maintainer cares about getting right.

You are not looking for completeness. You are looking for *texture*: is this a cleanly-scoped tool with a tight mental model, or a sprawling system held together by intent? Both can be good. They distribute differently.

### 2. Read the documentation second

Now read the README, `AGENTS.md`, `SEED.md`, `SPEC.md`, anything under `docs/`, anything in the repo that documents itself.

The docs are the project's *claim* about itself. Compare to the code. Note where they align (the project knows itself), where they exceed (the project markets tomorrow's work as today's), and where they diverge (the code moved past the docs, or the docs name a different vision than the code is building).

The voice of the documentation is also data. A project written in Sīrah-rooted vocabulary or alchemical metaphor is not decorating itself. That register is structural. Distribution that flattens it loses the audience the register was meant to attract.

### 3. Read the recent history

Look at the last 50 to 100 commits, the last 20 closed issues, the open issues, any recent releases or branches in flight.

What is the project actively grappling with? What did it carve away? What does the maintainer keep coming back to fix? Recent activity is the true center of gravity. Version numbers lie. Last-push dates lie. README aspirations lie. Issue patterns and commit cadence do not.

A project named for one thing in its README and showing up as something else in its commits is a project mid-migration. The dossier needs to know which side it sits on.

### 4. Listen for the project's voice

By now you can describe, in the project's own register, what the project is. Write that description in one paragraph. Do not substitute a market category. If the project says "alchemy," use "alchemy." If the project says "stewardship," use "stewardship." If the project's name is no longer expanding to what it once did, do not force the old expansion (`ccft` may have been *Claude Code Fly Trap* once and might no longer carry that meaning).

### 5. Identify the actual audience

Ask: who is the kind of person who would install this, run it, and feel that it fit them? Not "who is in the market for X."

A project may not sit cleanly in a market category. Many of the best ones do not. If the project lives at the intersection of three audiences none of which has a name, name them yourself.

The audience comes from the project's voice and capability, not from the channel landscape.

### 6. Identify the distinguishing claim

What does this project do that nothing else does, or that nothing else does this way?

The honest answer is sometimes "nothing yet." That is fine. The dossier then knows the project is a bet on craft, on trust, on audience-formation, not on novelty. Different distribution shape.

If there is a distinguishing claim, write it in one sentence. It should be specific enough to defend.

### 7. Only now: trend and adjacency analysis

After the above is in hand, validate that channels exist to reach the audience you named. Do web research grounded in the project's own claim, not in the closest market category.

The right question: *where do the people named in step 5 actually congregate, what do they read, who do they trust?*

The wrong question: *what channels exist for the market category this project pattern-matches to?*

Channels validated this way will be specific. They may not be the obvious ones. They may include venues that don't exist for the imported category.

### 8. Write the dossier

Use the existing dossier shape (see other files in `projects/` for the section template). Fill each section from the evidence gathered in steps 1 through 7, not from priors. Quote the project's own voice where it is already sharp. Reframe only where the maintainer can do better than their own copy.

---

## Cautions

- **Imported categories are the enemy.** "Agent observability tool," "data lineage platform," "MCP gateway," "service orchestration framework," "MITM proxy" are market categories. Importing them onto a project before step 4 produces category-shaped distribution that pulls the project toward the category leader's audience instead of the audience the project actually serves. Use them only after verifying the project's own claim aligns with the category. Often it does not.

- **The README is one input, not the truth.** Maintainers revise their own perception of a project constantly. The README often lags the code, sometimes leads it, rarely matches it precisely. Both inputs matter. Neither alone is enough.

- **Do not flatten cultural register.** If a project's voice is Karachi-Urdu, Sīrah-rooted, alchemical, or otherwise distinct, distribution copy in English explains the system. It does not erase the system's constitution. See `policy.md` §2 and §3.

- **Distinguish current state from aspiration.** Both belong in the dossier, but in separate paragraphs. Launch readiness hinges on current state, not on the v1.0 vision.

- **The maintainer's hopes for the audience are not the actual audience.** Audiences form around what a project *is*, not around what its maintainer hopes it will become. The dossier must describe the audience that exists for the current state.

- **Names drift.** A project's name may no longer expand to what it originally did. Do not paste the old expansion if the project no longer signs in those letters. If the maintainer dropped the expansion deliberately, that is information, not an omission.

---

## Failure modes that show up in shallow dossiers

- Generic one-liners that could describe any project in the category ("a fast, modern X for Y").
- Channel mixes that look like the same channel mix every other project of the same category gets.
- Audience descriptions that do not name specific kinds of people.
- Repeating the README's marketing copy without verifying it against the code.
- Naming Latent Space, TLDR AI, `awesome-X` without verifying those venues currently cover what this project does.
- Letting "pre-launch" status sit as if launch is the point. Launch is a beat. The project is the substance.

---

## What a per-project research agent needs

- The repo URL or local clone path.
- This `method.md`.
- The existing dossier as a starting point, to compare against and update where evidence diverges.
- The dossier shape, taken from any of the existing files in `projects/`.
- Web search and web fetch, used at step 7 only.
- Permission to read code, fetch GitHub issue history, read package manifests.
- Time. This is not a 10-minute exercise. A serious dossier is a half-day of careful work.

---

## Output

A revised dossier at `projects/<name>.md` that:

1. Names what the project is in the project's own register.
2. Names the actual audience as specific kinds of people, not as "developers" or "users."
3. Names the distinguishing claim, or honestly admits there is not one yet.
4. Names channels grounded in step 7 research, not in priors.
5. Quotes the project's voice where the project's voice is already the right pitch.
6. Reframes only where the maintainer can do better than their own copy.
7. Marks current-state versus aspiration explicitly.
8. Stays under the `AGENTS.md` voice rules (no em dashes, no LLM cliches, sentences not bullet-fragments).
9. Updates `projects.json`'s `note` field for that project to a one-liner that matches the new dossier opening.

A short research log appended to the dossier under a `## Research log` section: which sources were consulted, which were rejected as imported-category, what the agent learned in step 4 that surprised them. This makes the next pass cheaper and keeps the methodology honest.
