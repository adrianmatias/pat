---
name: run-adversarial
description: Use when an artifact, plan or diff needs an independent adversarial read before it lands — a different model family, the contract and the artifact but never the author's reasoning, findings with evidence, and an adjudication trail. This is the executable recipe behind the standing invariant "reviewers come from a different family than the author".
---

# run-adversarial

Core principle: a reviewer that shares the author's family shares the author's blind spots. The value of
the pass is not more eyes — it is **different eyes over the same contract**, with an output shape that
cannot degrade into praise.

## When

- Before anything a human will sign: a plan, a pack, a pricing model, a public claim.
- When the author model was cheap and the stakes were not.
- When a prior review passed but something feels under-examined — run a *different* family, not the same
  one harder.

**Not** for: a one-line edit, a lookup, a draft that is still moving. Review of a moving artifact is
theatre; freeze it first.

## Family selection (never the author's)

| author | reviewer |
|---|---|
| deepseek (`base`) | glm-5.3 · glm-5.3-flash · mimo-v2.6-pro · gemini-4-argon |
| glm (Z.ai) | deepseek-v4.1-flash · gpt-6.1-sol · kimi-k3.1-pro |
| mimo (Xiaomi) | glm-5.3 · gemini-4-argon · gpt-6.1-sol |
| kimi (Moonshot) | glm-5.3 · gpt-6.1-sol · mimo-v2.6-pro |
| gpt (OpenAI) | glm-5.3-flash · mimo-v2.6-pro · kimi-k3.1-pro |

Two passes beat one model twice: a cheap family pass (`glm-5.3-flash`) for breadth, then a premium pass
(`kimi-k3.1-pro`, `gpt-6.1-sol`) if the first found anything structural. Escalation rides
`patterns/cascade.md`, not repetition.

## The dispatch

Hand the reviewer exactly three things, in this order:

1. **The contract** — the acceptance checks, the plan, the spec, the thing the artifact promises.
2. **The artifact** — the file(s), with paths; a diff for code.
3. **The evidence** — where claims are supposed to come from (paths, dates, the source-of-truth spine).

Never hand it: the author's reasoning, the conversation, the author's summary of what was built. Fresh
context is the feature. If the reviewer has to reconstruct why a choice was made, that is a question for
the contract, not for the author.

On this harness: `subagent` with `agent: reviewer-glm` (cheap) or `agent: reviewer-kimi` (premium), or any
agent whose model is a different family. Ask for no more than 10 checks, each falsifiable.

## The output contract

The reviewer must return, in this shape, or its answer is not usable:

- `PASS` / `FAIL`, first line, nothing before it.
- **Must fix** — numbered, each with `file:line`, the defect, the evidence (a failing check, a quoted
  source, a concrete scenario), and the smallest repair.
- **Should fix** — the same, but not gating.
- **Checked and sound** — what it tried to break and could not. This is not praise; it is the list of
  things the parent can stop worrying about.
- **Could not test** — the residual risk, named.

## Adjudication (the parent's job, not the reviewer's)

Every finding gets one disposition, written down:

| disposition | when | what happens |
|---|---|---|
| **accept** | the evidence holds | repair, then re-run the *specific* check that would have caught it |
| **accept, defer** | real but out of scope | record with the reason and the scope it belongs to |
| **reject** | preference, not defect — or the evidence does not hold | record the counter-evidence, one line |

Never repair silently and never accept silently: an unreviewed finding is a finding that reappears.
A review whose findings are all accepted unverified is a second author, not a reviewer.

## Kill switch

Stop the pass when two consecutive reviewers return only style findings, or when the parent starts
arguing with the reviewer instead of with the evidence. Both mean the artifact is at the level this
method can reach; the next useful move is a human read, not another model.
