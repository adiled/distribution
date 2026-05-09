# ASKS

Things blocking distribution work that **only the human (`@adiled`) can do.** Sign-ins, account creation, OAuth grants, posting from owned identity, decisions that aren't mine to make.

Format: `[priority]` `[unblocks]`. What to do. *Why it's a human task.*

When an ask is resolved, strike it through and move it to the **Resolved** section at the bottom with the date.

---

## P0: blocks T2 launches

- **`[P0]`** **`[unblocks: every T2 launch]`** Create or confirm a **Homebrew tap repo** (`adiled/homebrew-tap`). A public empty repo on GitHub is enough for now. Formulas can be added per-project. *Account-level GitHub action. The repo is part of the public identity.*

- **`[P0]`** **`[unblocks: ccft launch, clwnd launch]`** Confirm or create credentials for **crates.io** (the Rust registry) and run `cargo login` locally. *Tied to your GitHub identity. Requires the human OAuth flow.*

- **`[P0]`** **`[unblocks: ccft launch, clwnd launch, opencode-dir launch]`** Confirm or create the **`adils.me`** publishing pipeline (whatever it is: Next.js on Vercel, a static-site generator, a hosted blog). The launch plans for `clwnd`, `ccft`, and `bhai-lang` all hinge on a thesis post landing on `adils.me`. If the site is dormant, decide whether to revive it or pick an alternative (own domain plus new static site, Substack, blog.adils.me). *Strategic decision. Affects every T2/T3 launch.*

- **`[P0]`** **`[unblocks: bhai-lang launch]`** **Decide the `bhai-lang` rename.** See `projects/bhai-lang.md` "The naming-collision problem." Recommendation is to rename to `بھائی-lang` (with `bhai-lang` as legacy slug). This decision is structural and affects every downstream piece of public copy. *Identity-level call. Only you can make it.*

- **`[P0]`** **`[unblocks: bhai-lang launch]`** **Decide the `bhai-lang` license.** Recommendation: **Apache-2.0** (patent grant matters for cryptographic and lineage work). *Legal decision. Only you.*

- **`[P0]`** **`[unblocks: clwnd launch, opencode-dir launch, ccft launch, orch/orchd launch]`** Add `LICENSE` files to repos missing them. Recommended defaults from `policy.md` §4:
  - `clwnd`: MIT
  - `opencode-dir`: confirmed MIT (verify file present)
  - `ccft`: Apache-2.0
  - `orch`: Apache-2.0
  - `orchd`: Apache-2.0
  - `clwnd-playground`: MIT (or noted "playground, no warranty")

  *These need a human keystroke (commit and push from your machine). Trivial but I shouldn't be the one signing your repos with a license without explicit confirmation per file.*

---

## P1: blocks specific channels

- **`[P1]`** **`[unblocks: ohlc-resample contributor block correction]`** Update the `ohlc-resample` README contributors block to use **`@adiled` only** (and `hello@adils.me` if you still want that contact path), removing the legal-name line. *Public identity decision. Consistent with `policy.md` §2's "Author identity is the handle" rule. I won't touch your existing-and-shipping public README without the explicit go-ahead.*

- **`[P1]`** **`[unblocks: every npm publish for new TS projects]`** Confirm **npm** account state. Does `@adiled` (or whatever your npm handle is) have a current login? Are 2FA tokens reachable? Run `npm whoami` to check. *Account credential. Only you.*

- **`[P1]`** **`[unblocks: every PyPI publish]`** Confirm **PyPI** account state for `bhai-lang` and any future Python projects. Set up an API token, save in keyring. *Account credential. Only you.*

- **`[P1]`** **`[unblocks: MCP-registry distribution]`** Confirm **Anthropic MCP registry** publishing flow. What's the current submission process, what credentials are needed, who owns submissions on behalf of `@adiled`. *Vendor account.*

- **`[P1]`** **`[unblocks: Lobste.rs submissions]`** **Lobste.rs requires an invite.** If you don't have an account, request one (`lobste.rs/invitations`). Submissions can take weeks. Lobste.rs traffic is high-quality for `orch`/`orchd` and other technical-substrate work. *Account creation only you can do.*

- **`[P1]`** **`[unblocks: Latent Space pitch for ccft]`** Decide whether to pitch as **guest essay**, **interview**, or **podcast appearance**. Different prep effort for each. Affects the timing of pre-launch sequences. *Strategic choice. Only you.*

- **`[P1]`** **`[unblocks: Reddit posting on launch days]`** Reddit account in good standing on `/r/LocalLLaMA`, `/r/algotrading`, `/r/selfhosted`, `/r/rust`. New accounts with launch-day posts get filtered as spam. If your account isn't established on each, build organic activity *now* (comments, votes) so launch posts go through. *Identity work. Only you.*

- **`[P1]`** **`[unblocks: X / Bluesky launch threads]`** Confirm `@adiled` (or chosen handle) on X, Bluesky, Mastodon. If currently dormant, post 5 to 10 organic technical posts before any launch beat to look human and active rather than launch-only. *Identity work. Only you.*

---

## P2: quality-of-life / nice-to-have

- **`[P2]`** **`[unblocks: distribution analytics]`** Decide whether to set up **plausible.io** (or similar) on `adils.me` and per-project landing pages. Distribution metrics get a lot more reliable with privacy-respecting analytics. *Account and DNS work. Only you.*

- **`[P2]`** **`[unblocks: launch coordination]`** Decide on a **launch calendar**. Per `policy.md` §5: don't launch on Fridays, max one T2 launch per week, max one T2 launch per project per quarter. Pick a target month for the first T2 launch (recommended: `opencode-dir` first as low-risk pilot, then `clwnd` about 3 weeks later). *Calendar choice. Only you.*

- **`[P2]`** **`[unblocks: GitHub profile presentation]`** Update the `@adiled/adiled` profile README to reflect the active T1, T2, T3 roster, ordered by tier rather than chronologically. Currently a profile visitor sees the chronological repo list, which buries the credibility-anchoring `ohlc-resample`. *Public identity surface. Better with your own voice.*

- **`[P2]`** **`[unblocks: substrate-project intellectual seeding]`** Compile the **list of named individuals** in agent-safety and corrigibility circles to send the relevant essay to, when ready. About 10 names. *Relationship work. Only you have the relationships.*

- **`[P2]`** **`[unblocks: bhai-lang's cultural-arc seeding]`** Compile the **list of Urdu/Pakistani tech communities** for the cultural-arc essay distribution (specific Twitter accounts, Slack/Discord groups, journalists who cover Pakistani tech). *Relationship work. Only you.*

- **`[P2]`** **`[unblocks: ohlc-resample maintenance signal]`** Decide whether to enable **GitHub Sponsors** on the `adiled` profile. Sponsors-button presence is itself a maintained-and-serious signal even if no one sponsors. *Identity decision.*

- **`[P2]`** **`[unblocks: launch-day responsiveness]`** Set up **GitHub notification routing**. Issues on T2 projects should reach you within an hour during their launch windows. Mobile notifications, email filters, or a dedicated channel. *Personal workflow. Only you.*

---

## How I'll work this list

- **I check this file at the start of each work session.** P0 items get pulled into the active work. P1 items get raised to you when their blocked work is up next.
- **I won't take destructive or identity-shaping actions without your go-ahead**, even if the action is mechanical (e.g. force-pushing a license update to a repo you've already published). P0 items framed as "add a LICENSE file" mean "tell me which license per repo, then I'll PR it." Not "I'll pick and push."
- **Anything I notice during work that needs your hand goes in here.** This file is the bottleneck registry. It should grow when you're not looking and shrink when you are.

---

## Resolved

*(empty. Strike-through resolved items here with the date they cleared.)*
