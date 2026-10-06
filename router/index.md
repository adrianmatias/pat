# router — one screen

Route first. Load only what you route. Everything deeper is on demand.

**Route format:** `route: <pattern>[/<tier>] · models: <role>=<id>… · skills: <skill>…`
State it in one line before working.

---

## 1 · models (roles)

| role | one line | today |
|---|---|---|
| `base`/`main` | cheap loop + swarm workhorse | **deepseek 4.1 family** (speed, price, cache-hit) |
| `deep` | plans, architecture, synthesis | deepseek-v4-pro / **gpt-5.1-sol** (iq/cost) / mimo-v2.6-pro |
| `agent` | hard agentic turns | **gpt-5.1-sol** (iq/cost) / mimo-v2.6-pro / kimi-k3 |
| `code` | bounded implementation | `base` first, `agent` if dense |
| `review` | adversarial pass — **never the author's family** | mimo / glm / kimi |
| `judge` | cheap tie-breaker | `base` / glm-flash |
| `hetero` | swarm diversity slot | rotate mimo-flash, glm-flash, qwen-flash, kimi |

Full mapping + cache discipline: `models.md`. Single-agent turns may simply ride the local model
router (`jevonian/auto`) when mounted — it tiers per turn.

## 2 · patterns (catalog)

| id | one line | load |
|---|---|---|
| `sandwich` | plan (deep) → execute (cheap ∥) → validate (independent) | `patterns/sandwich.md` |
| `adversarial` | different-family review pass; findings with failing cases | `patterns/adversarial.md` |
| `expand-contract` | breath-in wide/unjudged ↔ breath-out kill-with-reasons + ONE move | `patterns/expand-contract.md` |
| `megaprompt` | one context-complete file → deep model → decision-complete plan | `patterns/megaprompt.md` |
| `fan-out` | one question per fresh subagent → distill with cites | `patterns/fan-out.md` |
| `cheap-swarm` | N cheap workers (min 3 / mid 6 / max 12) on a cached preamble | `patterns/cheap-swarm.md` |
| `sr-and-jr` | junior drafts vs contract, senior repairs ≤3 rounds | `patterns/sr-and-jr.md` |
| `cascade` | cheapest attempt → verify → escalate one tier with evidence | `patterns/cascade.md` |
| `duel` | two family-diverse drafts, judge against a pre-written rubric | `patterns/duel.md` |

Each file carries **when not to** and a kill switch — read it before running the pattern.

## 3 · skills (executable recipes)

| skill | load when | load |
|---|---|---|
| `run-sandwich` | routed to sandwich | `skills/run-sandwich/SKILL.md` |
| `run-cheap-swarm` | routed to cheap-swarm | `skills/run-cheap-swarm/SKILL.md` |
| `run-sr-and-jr` | routed to sr-and-jr | `skills/run-sr-and-jr/SKILL.md` |
| `brainstorming`, `writing-plans`, `subagent-driven-development`, `systematic-debugging`, `verification-before-completion`, `dispatching-parallel-agents`, `writing-skills` | the situation they name | `vendor/superpowers/skills/<name>/SKILL.md` |

Superpowers skills speak Claude Code's tool names — map via `AGENTS.md`.

## 4 · routing

**Jev chooses the pattern:** `node router/choose.mjs "<task>"` — one Decisions-API call
(~$0.00003), prints `route: <pattern> · confidence …`. When Jev is unreachable, the rubric below
decides — and it is the sanity check on Jev's answer either way.

### rubric (in order)

1. **Can you write acceptance checks for it right now?** No, and the task is *what to do* →
   `expand-contract` (or `brainstorming` for a single design). No, and the task is *what is true* →
   `fan-out` first.
2. **Kickoff of a large effort?** → `megaprompt` (verified facts + decision agenda) → its plan goes
   to `sandwich` or `writing-plans`.
3. **Independent chunks?** 1–3 → `sandwich` execute stage. 4–6 → `cheap-swarm/min`. 7–15 → `mid`.
   16+ or wide sweep → `max` (+ tree-reduce).
4. **Difficulty unknown?** → `cascade` (cheapest-that-finishes, mechanical verifier required).
5. **One answer, high stakes?** → `duel`. **One artifact, correctness load-bearing?** → fold
   `adversarial` (as `sandwich` validate or standalone).
6. **Draft improves under critique?** → `sr-and-jr`.
7. **Default: `sandwich`.** When torn between two routes, take the one with the earlier kill switch.

## 5 · standing rules (always apply)

- Children are born from **one goal string**; fresh context by default, forked context only for a
  reviewer who knowingly needs the author's trail.
- **Keep the absorb path:** a stuck delegation route is absorbed inline, not waited on.
- **Evidence before claims;** a gate is only a gate once it has been seen to fail (`--prove` it).
- Kills and decisions are recorded with reasons; **every section ends in a decision or a kill**.
- The human's attention is the bottleneck: ask at most 3 human gates, and only for facts only the
  human holds. Give arguments, not options.
- Compile, don't RAG: distill findings with source+date; never paste raw reports into durable notes.
