---
name: run-sandwich
description: Use when a routed task is a real change with decision risk and the router selected the sandwich pattern. Runs plan on a deep model, parallel cheap execution against the decision-complete plan, and an independent validation pass. Default shape for features, refactors, and behavior changes. Not for lookups or exploration.
---

# run-sandwich

Core principle: expensive roles touch only decisions and evidence; volume rides on cheap tokens.

## Implementation (DeepSeek Harness `workflow` JS)

```js
const DEEP = { provider: "openrouter", model: "xiaomi/mimo-v2.6-pro" };
const BASE = { provider: "openrouter", model: "deepseek/deepseek-v4.1-flash" };
const REVIEW = { provider: "openrouter", model: "z-ai/glm-5.3" };       // never author's family

const plan = await agent(`Turn this prompt into a DECISION-COMPLETE plan: every choice made, ` +
  `every file named, numbered implementation chunks (independent where possible), and explicit ` +
  `acceptance checks.\n\nPROMPT:\n${args.prompt}`,
  { label: "plan", ...DEEP,
    schema: { type: "object", properties: { plan: { type: "string" }, chunks: { type: "array", items: { type: "string" } }, acceptance: { type: "array", items: { type: "string" } } }, required: ["plan", "chunks", "acceptance"], additionalProperties: false } });

const built = await parallel(plan.chunks.map((c, i) => () =>
  agent(`Implement EXACTLY this chunk from the plan; do not make decisions the plan didn't make. ` +
    `If you need one, return it in blockers instead.\nCHUNK ${i + 1}:\n${c}\nPLAN CONTEXT:\n${plan.plan}`,
    { label: `exec-${i + 1}`, ...BASE,
      schema: { type: "object", properties: { done: { type: "string" }, blockers: { type: "array", items: { type: "string" } } }, required: ["done", "blockers"], additionalProperties: false } }))
);

const validation = await agent(`Validate against the acceptance checks. Findings need evidence ` +
  `(a failing check or concrete risk). PASS/FAIL + findings.\nACCEPTANCE:\n${plan.acceptance.join("\n")}\n` +
  `BUILT:\n${JSON.stringify(built)}`,
  { label: "validate", ...REVIEW,
    schema: { type: "object", properties: { verdict: { type: "string", enum: ["pass", "fail"] }, findings: { type: "array", items: { type: "string" } } }, required: ["verdict", "findings"], additionalProperties: false } });

return { plan: plan.plan, built, validation };
```

## Common mistakes

- A plan with open decisions → execution improvises; send it back to `plan` (bounded), don't absorb.
- Validating with the author's own family → it agrees with its blind spots; the `review` role
  invariant is "never the author's family".
- Acceptance checks invented at validation time → theatre; they belong in the plan stage.
