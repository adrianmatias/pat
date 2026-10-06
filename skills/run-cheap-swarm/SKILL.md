---
name: run-cheap-swarm
description: Use when a routed task decomposes into 4+ independent bounded chunks and the router selected the cheap-swarm pattern. Runs N parallel cheap workers (deepseek 4.1 family, optional hetero mix) over an identical cached preamble plus one slice each, then merges. Not for single decisions or coupled edits.
---

# run-cheap-swarm

Core principle: pay for slices, not for prompts — one byte-stable preamble, N cheap workers, one
merge.

## Quick reference

| tier | workers | mix |
|---|---|---|
| min | 3 | all `base` |
| mid | 6 | 4× `base` + 2× `hetero` |
| max | 12 | 8× `base` + 3× `hetero` + `deep` merger (two-stage reduce) |

`base` = `deepseek/deepseek-v4.1-flash` (or `deepseek-official/deepseek-flash`); `hetero` rotates
`xiaomi/mimo-v2.6-flash`, `z-ai/glm-5.3-flash`, `qwen/qwen3.8-flash`.

## Implementation (DeepSeek Harness `workflow` JS)

```js
const slices = args.slices;                    // [{id, brief}, ...] from the decomposition step
const HETERO = ["openrouter/xiaomi/mimo-v2.6-flash", "openrouter/z-ai/glm-5.3-flash"];
const BASE = { provider: "openrouter", model: "deepseek/deepseek-v4.1-flash" };
const PREAMBLE = "You are one worker in a swarm. Work ONLY your slice. " +
  "Return {result, gaps}; gaps lists anything you could not finish or need from a sibling.";

const results = await parallel(slices.map((s, i) => () =>
  agent(`${PREAMBLE}\n\nSLICE ${s.id}:\n${s.brief}`, {
    label: `swarm-${s.id}`, phase: "swarm",
    ...(i % 3 === 2 && slices.length >= 6
      ? { provider: "openrouter", model: HETERO[(i / 3 | 0) % HETERO.length] }
      : BASE),
    schema: {
      type: "object",
      properties: { result: { type: "string" }, gaps: { type: "array", items: { type: "string" } } },
      required: ["result", "gaps"], additionalProperties: false
    }
  })
));

const blocked = results.filter(r => r && r.gaps && r.gaps.length > 0);
if (blocked.length > results.length * 0.2) {
  return { aborted: true, reason: "decomposition likely wrong", gaps: blocked.map(b => b.gaps) };
}
return await agent(`Merge worker results (dedupe, reconcile conflicts, list remaining gaps):\n${
  JSON.stringify(results)}`, { label: "swarm-merge", ...BASE,
  schema: { type: "object", properties: { merged: { type: "string" }, gaps: { type: "array", items: { type: "string" } } }, required: ["merged", "gaps"], additionalProperties: false } });
```

Generic harnesses: same shape with your parallel-subagent primitive; keep the preamble identical.

## Common mistakes

- Re-wording the preamble per worker → every worker misses the cache; cost is N× again.
- Slices with hidden coupling → merge hell; if slices reference each other, you wanted `sandwich`.
- Concatenating worker output raw → the merge step is half the pattern.
- Ignoring the >20%-blocked kill switch and "fixing" it with more workers.
