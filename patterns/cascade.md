# cascade

**Problem.** Task difficulty is unknown a priori; routing everything to a strong model wastes money,
routing everything to a cheap one wastes the turn.

**Shape.** Attempt at the cheapest viable role, verify mechanically, escalate **one tier at a time**
only on proven failure — carrying the failure evidence forward:

```
base (deepseek 4.1) → verify → fail? → code/agent (mimo, kimi) → verify → fail? → deep → fail? → human
```

- **Verify is mechanical, not vibes:** tests run, command exits 0, schema validates, spec items
  checked off. If verification needs judgment, fold `adversarial` or `duel` instead of escalating.
- **Escalations pass a failure packet:** what was attempted, what broke, the exact error. The next
  model must not re-derive the previous failure.
- **Cap at 2 escalations.** Fallback must stay cheap — a third hop means the problem is misjudged
  (root-cause it, don't brute-force it).
- Never auto-escalate downward mid-task (cheapening a half-done task loses the strong model's work).

**When.** Maintenance chores, "just make it work" requests, batch items of unknown grit, anything
where you'd rather start cheap and be wrong cheaply.

**When not.** Known-hard design (start at `deep` via `sandwich`), no mechanical verifier exists
(`duel` or `sr-and-jr` instead).

**This is the jev-router philosophy, spelled out:** the cheapest model that can finish the turn.
When a local model router (`jevonian/auto`) is mounted it does this per-turn already; `cascade`
covers the multi-turn / scripted case where the harness itself drives the escalation.

**Cost profile.** Expected cost = cheap attempt + verifier, plus escalation tail only where needed.
Cheaper than frontier-everywhere by the (large) fraction of tasks that die at tier one.
