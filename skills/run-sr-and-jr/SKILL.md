---
name: run-sr-and-jr
description: Use when a routed task is an iterative draft that improves under critique and the router selected the sr-and-jr pattern. Runs a bounded junior-senior loop: a cheap junior drafts against a written contract, a senior from a different family returns accept or a numbered repair list, max three rounds. Not for exploration or one-shot answers.
---

# run-sr-and-jr

Core principle: senior time buys judgment, not keystrokes — the junior owns every revision.

## Implementation (DeepSeek Harness `workflow` JS)

```js
const task = args.task, contract = args.contract;   // contract: spec + acceptance checks
const JR = { provider: "openrouter", model: "deepseek/deepseek-v4.1-flash" };
const SR = { provider: "openrouter", model: "xiaomi/mimo-v2.6-pro" };   // different family than jr

let draft = await agent(`Task:\n${task}\n\nContract (acceptance checks):\n${contract}\n\nProduce the artifact.`,
  { label: "jr-draft", ...JR, schema: { type: "object", properties: { artifact: { type: "string" }, selfCheck: { type: "string" } }, required: ["artifact", "selfCheck"], additionalProperties: false } });

for (let round = 1; round <= 3; round++) {
  const verdict = await agent(
    `You are the senior reviewer. Judge ONLY against the contract. Do not rewrite the artifact.\n` +
    `CONTRACT:\n${contract}\n\nARTIFACT:\n${draft.artifact}\n\n` +
    `Return {verdict: "accept"|"repair", repairs: [numbered, each testable]}.`,
    { label: `sr-review-${round}`, ...SR,
      schema: { type: "object", properties: { verdict: { type: "string", enum: ["accept", "repair"] }, repairs: { type: "array", items: { type: "string" } } }, required: ["verdict", "repairs"], additionalProperties: false } });

  if (verdict.verdict === "accept") return { rounds: round, artifact: draft.artifact };
  if (round === 3) return { escalated: true, artifact: draft.artifact, openRepairs: verdict.repairs };
  draft = await agent(`Revise the artifact against exactly this repair list (nothing else):\n${
    verdict.repairs.map((r, i) => `${i + 1}. ${r}`).join("\n")}\n\nARTIFACT:\n${draft.artifact}`,
    { label: `jr-revise-${round}`, ...JR, schema: { type: "object", properties: { artifact: { type: "string" }, selfCheck: { type: "string" } }, required: ["artifact", "selfCheck"], additionalProperties: false } });
}
```

## Common mistakes

- No written contract → the senior judges vibes and the loop drifts; write the contract first.
- Senior rewriting the artifact → you've silently become a `sandwich` at senior prices.
- Endless "one more round" → the 3-round exit is the pattern; escalate (`cascade`) or ask.
- Same family on both roles → correlated blind spots; keep the families different.
