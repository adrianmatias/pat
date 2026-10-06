# cheap-swarm

**Problem.** A task decomposes into many independent bounded chunks; serial execution wastes wall
time and paying frontier prices for each chunk wastes money.

**Shape.** N parallel workers on the `base`/`main` role (deepseek 4.1 family), one identical
preamble + one bounded slice per worker; a contraction step merges results. Swarm size follows the
chunk count — never the ambition:

| tier | workers | when | mix |
|---|---|---|---|
| `min` | 3 | 3–6 independent chunks | all `base` |
| `mid` | 6 | 7–15 chunks | 4× `base` + 2× `hetero` |
| `max` | 12 | 16+ chunks, or wide sweeps (research, audit) | 8× `base` + 3× `hetero` + 1× `deep` merger (two-stage reduce) |

**Hetero mix.** Different model families fail differently — a couple of `hetero` slots (mimo, glm,
qwen, kimi…) decorrelate worker errors and catch what a monoculture misses. Use all-`base` when
output *uniformity* matters (style, format, mechanical edits); use the mix when *coverage* matters
(research, review sweeps, hostile input).

**Cache economics (the point of deepseek 4.1 here).** Build every worker as **identical preamble +
slice**: the preamble is written once and cache-read by all N workers. Keep it byte-stable — no
timestamps, no per-worker wording. Swarm cost ≈ 1× preamble + N× slices, not N× full prompts.

**Rules.**
- Swarm only independent chunks — no shared mutable state between workers; every result must be
  mergeable without knowing its siblings. (Do not swarm a single design decision.)
- Give each worker a *contract*: input slice, output schema, done-when. A worker that cannot state
  its done-when is not a chunk — decompose again.
- Contraction is load-bearing: a merge pass (`base`, or `deep` for `max`) dedupes, reconciles
  conflicts, and reports gaps. Never concatenate worker outputs raw.
- **Kill switch:** if >20% of workers answer "blocked / depends on X", the decomposition is wrong.
  Stop the swarm; fall back to `expand-contract` (to find the real seams) or `sandwich`.

**When not to.** Single decisions, tightly coupled edits, tasks whose chunks share state, or any
turn where the *thinking* is the work (use `sandwich` plan stage).

**Cost profile.** min ≈ 3 cheap turns + merge. mid ≈ 6 cheap + merge. max ≈ 12 cheap + tree-reduce.
All far below one frontier pass over the same volume.
