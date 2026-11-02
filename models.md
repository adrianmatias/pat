# model roles

Roles travel with the pattern collection; concrete ids are machine-local facts. Update the
right column per machine, keep the role names stable — patterns reference **roles only**.

## the roster (decided 2026-10-27)

Seven models earn a place. Each one earns it by being **first at something**, not by being good at
everything: a roster of near-equals is a roster that gets picked by vibe. Prices and context windows are
the local catalog snapshot at `~/.pi/agent/models-store.json` on 2026-10-27 (USD per million tokens,
`in / out`); they drift, the roles do not.

| id | ctx | in / out | what it is first at | role |
|---|---|---|---|---|
| `openrouter/deepseek/deepseek-v4.1-flash` | 1.0 M | 0.006 / 19.19 | cheapest input of any frontier-ish model; cached-prefix workhorse | `base` / `main` |
| `openrouter/xiaomi/mimo-v2.6-pro` | 1.05 M | 0.37 / 0.74 | best iq per dollar at 1 M context — the default `deep` | `deep` / `code` when dense |
| `openrouter/openai/gpt-6.1-sol` | 1.05 M | 1 / 5 | best agentic judgement per dollar; the strongest tool-loop | `agent` / `deep` when iq/cost decides |
| `openrouter/moonshotai/kimi-k3.1-pro` | 1.05 M | 2 / 10 | always-thinking synthesis; the high-stakes adversarial read | `deep` (long synthesis) / `review` (high stakes) |
| `openrouter/z-ai/glm-5.3` | 1.05 M | 0.07 / 1.40 | cheap input, strong adversarial findings; different lab from the base | `review` |
| `openrouter/z-ai/glm-5.3-flash` | 1.05 M | 0.038 / 0.085 | near-free verdicts; the cheap review pass and the tie-breaker | `judge` / `review` (cheap) / `hetero` |
| `openrouter/google/gemini-4-argon` | 1.05 M | 1 / 5 | the only non-OpenAI, non-Chinese-lab frontier model on this key | `duel` draft B / `review` (hetero) |

**Retired on 2026-10-27:** `z-ai/glm-4.7` (202 k context, 0.40 / 1.75). It was still pinned as the
reviewer model in `~/.pi/agent/agents/reviewer-glm.md` while `glm-5.3-flash` gives 5× the context for
a tenth of the input price and a twentieth of the output price. Nothing in `pat` ever named it; the
stale pick lived in a harness config, which is exactly where stale picks hide.

**Not on the roster, with reasons:** `deepseek-v4-pro` (0.88 / 1.76 at 1 M — dominated by
mimo-v2.6-pro on price and by gpt-6.1-sol on judgement, and the base family already covers deepseek);
`kimi-k2.7-code` (0.56 / 3.10 at 262 k — a coding specialist priced like a generalist, in a slot that
wants diversity, not code); `qwen3.8-flash` (0.15 / 0.47 — fine, but mimo-v2.6-flash is cheaper and
glm-5.3-flash cheaper still; it returns only if a swarm needs a fourth family).

**One number to verify before a big swarm:** the catalog prices `deepseek-v4.1-flash` output at
19.19/M while its input is 0.006 — output-heavy loops on `base` are the one place that ratio could hurt.
If a swarm's output volume is large, `mimo-v2.6-flash` (0.10 / 0.28) is the cheaper workhorse and buys a
different family for free.

## role table

| role | job | never | pick today (this machine) |
|---|---|---|---|
| `base` / `main` | cheap agentic loop, swarm workhorse | — | **deepseek 4.1 flash**: `deepseek-official/deepseek-flash`, `openrouter/deepseek/deepseek-v4.1-flash`; `mimo-v2.6-flash` when output-heavy |
| `deep` | plans, architecture, long synthesis | auto-fallback to it | `openrouter/xiaomi/mimo-v2.6-pro` (default) · `openrouter/openai/gpt-6.1-sol` (iq/cost) · `openrouter/moonshotai/kimi-k3.1-pro` (1 M ctx synthesis) |
| `agent` | hard agentic turns, tool-heavy | — | `openrouter/openai/gpt-6.1-sol` (iq/cost) · `mimo-v2.6-pro` · `kimi-k3.1-pro` |
| `code` | bounded implementation chunks | — | `base` (deepseek 4.1) first; `mimo-v2.6-pro` if dense |
| `review` | adversarial pass | **same model family as the author** | author=deepseek → `glm-5.3` / `glm-5.3-flash` (cheap pass) · `mimo-v2.6-pro` · `gemini-4-argon` (non-Chinese-lab check) · `kimi-k3.1-pro` (high stakes) |
| `judge` | cheap tie-breaker / scorer | the models being judged | `glm-5.3-flash`, else `base` |
| `hetero` | swarm diversity slot | repeating one family | rotate `glm-5.3-flash`, `mimo-v2.6-flash`, `gemini-4-argon` |

Latest generation: `kimi-k3.1-pro` (always-thinking, 1 M context) is the premium pick for `review` and
long synthesis; `mimo-v2.6-pro` is the default `deep` because it is five times cheaper and close enough
that only high-stakes plans earn the premium. `gemini-4-argon` is on the roster for **family diversity** —
today's cheap review picks (mimo, glm, kimi) are three Chinese labs, which is diverse by vendor and not
by training culture.

## no subscription routes

Tokens come from API keys only: `deepseek-official` and `openrouter`. There is no Anthropic
subscription route, and no reason to build on one: `claude` is not an author family, not a reviewer,
and not a role pick. A model that requires a subscription is not a candidate here — if a role needs
one, the role is wrong, not the key. A harness profile may still list an `anthropic` provider;
removing its display name does not disable the route, so check what the harness actually dispatches.

Why deepseek 4.1 is `main`: speed, price, capability, and **prompt-cache friendliness** — it is the
default workhorse for loops and swarms. (A standing preference, not a requirement: the roles are the
contract, the ids are today's pick.)

## routing through the local tier

Where a local model router is mounted, single-agent turns can just use it — it tiers per turn
(plan/execute/utility) over the same providers. Patterns that pin roles (swarms, review pairs) stay
explicit: their economics depend on the exact mix.

## cache discipline (why swarms are cheap)

DeepSeek-family prompt caching keys on the prompt prefix. Build swarm workers as
**identical preamble + one bounded slice each**: the preamble is written once and cache-read by
every worker after the first. Keep the preamble byte-stable across workers and turns (same field
order, no timestamps, no per-worker wording drift) or the cache miss is yours to pay.

## price truth

Prices drift — don't duplicate them in patterns. The local catalog snapshot
(`~/.pi/agent/models-store.json`, refreshed by the harness) and the providers' pricing pages own them;
any number quoted in this file carries the date it was read. This file owns *roles*.
