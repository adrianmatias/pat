# adversarial

**Problem.** The author's model family shares its blind spots — self-review confirms rather than
challenges, and a same-family reviewer tends to agree with the same wrong assumptions.

**Shape.** One independent review pass by a **different model family** than the author, over
precisely crafted context:

1. Hand the reviewer: the **contract** (spec / plan / acceptance checks), the **diff or artifact**,
   and the **test delta** — *not* the author's full reasoning, not the whole conversation. Fresh
   context is a feature: the reviewer owes nothing to the author's framing.
2. Reviewer returns **findings**, each one: claim + evidence + a failing case (or the concrete risk
   if no failing case exists yet). Ranked: blocker / should-fix / nit.
3. The author (or `jr`) verifies each finding before implementing it — feedback is not
   automatically true. Re-review only the contested fixes, not the world.

**Rules.**
- **`review` never shares the author's family.** Author deepseek → reviewer mimo/glm/kimi; author
  claude → reviewer deepseek/glm. This is a role invariant, not a preference.
- **Verify each finding against source before applying** — feedback is not automatically true
  (that rule is already in step 3; it earns its own line because skipping it has bitten before).
- Adversarial, not hostile: the reviewer's job is to find the missed case, not to win. A review
  that invents hypotheticals without a failing case is weak, not deep.
- Scope the pass: correctness against the contract first, style never. One focused pass beats three
  diffuse ones.
- Cheaper than `duel` — one artifact + one pass. If adversarial review keeps passing drafts you
  don't trust, escalate to `duel` (two candidates + judge).

**When.** Load-bearing correctness: migrations, auth, money, concurrency, "review the plan before we
execute", and as the `validate` stage of `sandwich` when a fresh family is the cheaper validator.

**When not.** Mechanical output verifiable by tests alone (trust the tests), exploration, drafts
still in flux (`sr-and-jr`'s repair loop is the right surface there).

Ship-blocking variant: the standing *validator* role — "you do not improve the pack; you pass or
fail it; a fail blocks release until the generator re-runs"; unit of work is the **release
surface**, not the artifact type.

**Cost profile.** +1 mid-tier turn per review, bounded context. Buy it where one wrong answer costs
more than a hundred reviews.

*(The "review role: adversarial pass, never same family as the author" rule is documented in
`~/pro/dsp/AGENTS.md`'s model-roles table.)*
