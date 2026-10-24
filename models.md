# model roles

Roles travel with the pattern collection; concrete ids are machine-local facts. Update the
right column per machine, keep the role names stable — patterns reference **roles only**.

## role table

| role | job | never | pick today (this machine) |
|---|---|---|---|
| `base` / `main` | cheap agentic loop, swarm workhorse | — | **deepseek 4.1 family**: `deepseek-official/deepseek-flash`, `openrouter/deepseek/deepseek-v4.1-flash` |
| `deep` | plans, architecture, long synthesis | auto-fallback to it | `deepseek-official/deepseek-v4-pro`, `openrouter/openai/gpt-6.1-sol` (iq/cost), `openrouter/moonshotai/kimi-k3.1-pro` (1M ctx), `openrouter/xiaomi/mimo-v2.6-pro` |
| `agent` | hard agentic turns, tool-heavy | — | `openrouter/openai/gpt-6.1-sol` (iq/cost), `openrouter/xiaomi/mimo-v2.6-pro`, `openrouter/moonshotai/kimi-k3.1-pro` |
| `code` | bounded implementation chunks | — | `base` (deepseek 4.1) first; `openrouter/xiaomi/mimo-v2.6-pro` if dense |
| `review` | adversarial pass | **same model family as the author** | author=deepseek → `openrouter/xiaomi/mimo-v2.6-*`, `openrouter/z-ai/glm-5.3*`, `openrouter/moonshotai/kimi-k3.1-pro` |
| `judge` | cheap tie-breaker / scorer | the models being judged | `base` (deepseek 4.1) or `openrouter/z-ai/glm-5.3-flash` |
| `hetero` | swarm diversity slot | repeating one family | rotate `mimo-v2.6-flash`, `glm-5.3-flash`, `qwen3.8-flash`, `kimi-k2.7-code` |

Latest generation: `openrouter/moonshotai/kimi-k3.1-pro` (always-thinking, 1M context) is the current
pick for `deep`, `agent` and `review`. The `hetero` rotation stays a generation behind on purpose —
it buys diversity, not peak iq.

## no subscription routes

Tokens come from API keys only: `deepseek-official` and `openrouter`. There is no OAuth subscription route to plan with:
`claude` is not an author family, not a reviewer, and not a role pick. A model that requires a
subscription is not a candidate here — if a role needs one, the role is wrong, not the key.

Why deepseek 4.1 is `main`: speed, price, capability, and **prompt-cache friendliness** — it is the
default workhorse for loops and swarms. (A standing preference, not a requirement: the roles are the
contract, the ids are today's pick.)

## routing through the jev router

Where a local model router (Jevonian, `jev-local/jevonian/auto`) is mounted, single-agent turns can
just use it — it tiers per turn (plan/execute/utility) over the same providers. Patterns that pin
roles (swarms, review pairs) stay explicit: their economics depend on the exact mix.

## cache discipline (why swarms are cheap)

DeepSeek-family prompt caching keys on the prompt prefix. Build swarm workers as
**identical preamble + one bounded slice each**: the preamble is written once and cache-read by
every worker after the first. Keep the preamble byte-stable across workers and turns (same field
order, no timestamps, no per-worker wording drift) or the cache miss is yours to pay.

## price truth

Prices drift — don't duplicate them here. `jevonian report` / the provider pricing pages own them.
This file owns *roles*.
