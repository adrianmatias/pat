# duel

**Problem.** One high-stakes answer where a single draft plus review still shares one model's blind
spot.

**Shape.** Two independent drafts from *different families*, then a cheap judge against a rubric:

1. Draft A on `base` (deepseek 4.1) and draft B on `hetero` (mimo, glm, kimi…) — same contract,
   **no shared context** beyond it (no cross-reading, no early peeking).
2. `judge` (`base` or `glm-flash`; never one of the two authors) scores both against a **rubric
   written before the drafts** — criteria, weights, disqualifiers. Returns: winner + which
   losing-side properties to keep.
3. Ship the winner, optionally harvest the loser's good properties into a final light merge pass.

**The rubric is the duel.** Written before drafting (or the judge grades vibes and the stronger
*writer* wins, not the better answer). If you cannot write the rubric, the task is not ready for a
duel — run `brainstorming`/`expand-contract` first.

**When.** Tricky bug fixes, architecture choices, security-sensitive changes, any "we only ship one
of these" turn. Also good as the `validate` stage of `sandwich` when a single reviewer family might
echo the author.

**When not.** Decomposable work (`cheap-swarm`), iterative drafts (`sr-and-jr`), cheap chores
(`cascade`). A duel costs ~2 drafts + 1 judgment — spend it where one wrong answer is expensive.

**Relation to `adversarial`.** `adversarial` critiques one draft (cheap, one extra pass). `duel`
produces two candidates (robust, ~3× cost). Escalate to a duel when adversarial review keeps
passing drafts you don't trust.

**Cost profile.** 2× mid drafts + 1× cheap judgment. The diversity is the product.
