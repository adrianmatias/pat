# direct

**Problem.** Most turns do not need orchestration. Routing a question, a lookup or a one-line edit
through a pattern pays a planning turn and a validation turn, sometimes switches the model, and
returns the same answer slower. There, the pattern is overhead — not shape.

**Shape.** No pattern. One agent — the one already in the session — continues:

1. **Same session, same model.** No role change, no subagents, no fresh context, no `deep`/`review`
   turn. The conversation's own context is the input.
2. **Act or answer in one pass.** Read what is needed, run the command, make the small edit, reply.
   No plan document, no todo theatre, no acceptance contract, no artifact.
3. **Verify with the cheapest direct check.** The command exits 0, the file reads back, the quote is
   in the source. That is the whole validation stage.

**Rules.**
- **No delegation, no model switch.** If the session's model is good enough to route the task, it is
  good enough to answer it.
- **One pass.** If the turn needs a second pass to be correct, it was not `direct` — re-route.
- **Re-route, do not stretch.** The moment the task turns out to need a decision with consequences,
  edits across modules, or more than one independent chunk, stop and route again (`sandwich`,
  `cheap-swarm`, …). Stretching `direct` into a feature is how partial work gets called done.
- **Cheaper, not sloppier.** Findings, numbers and quotes still obey *compute, don't compose*; a
  direct answer carries the same evidence bar as a routed one.

**When.** Questions and explanations, lookups and file reads, one-file or one-line edits, running a
command and reporting it, conversation continuations — anything one agent finishes in one pass.

**When not.** Real changes with decision risk (`sandwich`), unknown difficulty with a mechanical
verifier (`cascade`), research across sources (`fan-out`), artifacts whose correctness is
load-bearing (`adversarial`), drafts that improve under critique (`sr-and-jr`).

**Cost profile.** One turn, no extra context, no extra model. The cheapest route is the one that does
not exist — and it is the default for anything that does not earn a pattern.
