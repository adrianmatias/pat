# sr-and-jr

**Problem.** A task benefits from a few refinement rounds, but running a frontier model through all
rounds pays senior prices for junior work.

**Shape.** A bounded two-role loop over ONE artifact:

1. **`jr` (`base`, deepseek 4.1)** — executes the task or drafts the artifact against a written
   contract (spec, rubric, or acceptance checks).
2. **`sr` (`deep`/`agent`; a *different family* when correctness is load-bearing)** — reads only the
   contract + `jr`'s output and returns exactly one verdict: `accept`, or a **bounded repair list**
   (numbered, each item testable).
3. `jr` revises against the repair list. Repeat. **Max 3 rounds** — then escalate (`cascade`) or
   surface to the human. Round 3 is not "one more try"; it is the exit.

**The contract is the pairing.** `sr` never re-reads the whole task history and never edits the
artifact itself — it judges against the contract. If `sr` cannot judge without redesigning, the
contract was missing: write it (or run `brainstorming`/`expand-contract` first) before pairing.

**When.** Drafts that measurably improve under critique: non-trivial scripts, refactorings, docs,
config surgery, test suites. Cheap iteration beats front-loaded planning when feedback per round is
fast and local.

**When not.** Exploration without a spec (expand first), one-shot lookups (`cascade`), single
high-stakes answers (`duel`), decomposable batches (`cheap-swarm`).

**Why not just sandwich.** `sandwich` is plan → execute → validate as a *one-pass* pipeline with
three agents. `sr-and-jr` is a *loop* with two roles: it trades a weaker plan for 2–3 cheap
improvement cycles, and the senior's exposure stays bounded to output + contract.

**Cost profile.** ~3× `base` turns + 2–3 small `sr` reads. Escalation never free-falls into `deep`
per round — `sr` is the only senior surface.
