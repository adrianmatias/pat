# sandwich

**Problem.** Unstructured agent work mixes hard thinking and grunt work at one price level —
either overpaying for mechanical edits or underthinking the decisions.

**Shape.** Three stages, hard–cheap–hard, three different agents:

1. **plan** (`deep`) — one coherent agent turns the prompt into a **decision-complete** plan:
   every choice made, every file named, every acceptance check written. No "decide during
   implementation".
2. **execute** (`base`/`code`, cheap, parallel where safe) — bounded implementation against the
   plan. Swarm only genuinely independent chunks.
3. **validate** (`deep` **or** a *different family* than the author) — tests, behaviour, the plan's
   acceptance checks. The validator reads the plan + the diff, not the author's reasoning.

**Rules.**
- Do not ticket a young interconnected system — in a tightly coupled design, per-piece execution
  invites integration rot. Plan first, then slice.
- Do not swarm a single design decision — one coherent head owns each decision.
- The plan is the contract: if `execute` needs a decision the plan didn't make, the right move is a
  bounded return to stage 1, not improvisation.
- `validate` must be able to say no with evidence. If acceptance checks weren't written in stage 1,
  stage 3 is theatre.

**When.** Default shape for real changes — features, refactors with behaviour risk, anything where
the cost of a wrong decision exceeds the cost of a `deep` planning turn.

**When not.** Trivial lookups (`cascade`), context-assembly tasks (`megaprompt`), exploratory
spelunking (`expand-contract` first — *then* sandwich).

**Cost profile.** 1× `deep` + N× `base` + 1× validator turn. The expensive roles only touch
decisions and evidence; the volume rides on cheap tokens.
