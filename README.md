# pat

A pattern book for **agentic work** — orchestration shapes for AI coding agents — with a
**load-on-demand router** for models, patterns and skills. Built for lean, efficient, powerful
agentic engineering: route first, load only what you run.

3-char named per the house obsession (`dsp`, `opo`, `mat`, `agt`, `log`, `raw`, `pro`…). Born from the practices documented across `~/pro` projects (dsp, opo, …) and stitched together with
[obra/superpowers](https://github.com/obra/superpowers) (vendored, MIT).

## why

Most agent waste is one of two kinds: **context** loaded that never gets used, or **money** spent on
a frontier model doing slice work. This collection fixes both: one small routing screen decides the
*pattern* (how work is shaped), the *model roles* (who plays which part), and the *skills* (what
executable recipe to load) — everything else stays on disk.

## layout

```
INSTALL.md         how to mount pat into a project or vault (4 recipes + acceptance check)
router/index.md    the router: one screen — catalog one-liners, routing rubric, roles, load contract
router/choose.mjs  Jev picks the pattern for a task (Decisions API; rubric is the offline fallback)
patterns/          portable pattern designs (human-readable, harness-agnostic)
skills/            executable recipes, SKILL.md format, loaded only when routed
models.md          model roles → concrete ids (machine-local), cache discipline
PRINCIPLES.md      the invariants every pattern obeys (extracted from ~/pro)
AGENTS.md          agent entry contract (keep as the only standing context)
vendor/superpowers vendored skill library (pinned 8ca22db, MIT)
```

## the catalog

| pattern | one line | typical roles |
|---|---|---|
| `sandwich` | plan (deep) → execute (cheap, parallel) → validate (independent) | deep + base + review |
| `adversarial` | one review pass by a different model family, findings with failing cases | review ≠ author's family |
| `expand-contract` | breath-in wide/unjudged ↔ breath-out kill-with-reasons, contract ONE move | base × N + judge |
| `megaprompt` | one verified context-complete file → deep model → decision-complete plan | deep single turn |
| `cheap-swarm` | N cheap workers (min 3 / mid 6 / max 12) over identical cached preamble + one slice each | base × N (+ hetero) |
| `sr-and-jr` | junior drafts against a contract, senior repairs it in ≤3 bounded rounds | base loop + deep touch |
| `cascade` | cheapest attempt → mechanical verify → one-tier escalation with failure evidence | base → code → deep |
| `duel` | two drafts from different families, cheap judge against a pre-written rubric | base + hetero + judge |
| `fan-out` | one question per fresh subagent → distill reports with cites | base × N + parent |

## quickstart

1. Route the task: `node router/choose.mjs "<task>"` (Jev decides), or read `router/index.md` and
   use the rubric. Load only what you route. (`AGENTS.md` says the same.)
2. Install it into a project or vault — **[`INSTALL.md`](INSTALL.md)**: four recipes (vault symlink,
   DSH preset, vendored, submodule), the `.obsidian/` doctrine, the `log/mat.md` + `log/agt.md`
   contract, and the acceptance check to run afterwards. Short version: one canonical clone, symlink
   mounts, nothing copied.
3. Model roles live in `models.md` — adjust the right column to your providers (deepseek 4.1 is the
   `base`/`main` workhorse: speed, price, capability, cache-hit). If you run a local model router
   (`jevonian/auto`), single-agent turns can just use it.

## design notes

- Patterns are portable prose; skills are host recipes; the router is the only always-loaded page.
  Three load levels, one screen of standing context.
- Every pattern carries **when not to** and a kill switch. A pattern that fails gets fixed or
  deleted — see `PRINCIPLES.md`.
- Prices and catalogs drift; this repo owns *roles and shapes*, your router owns prices.

## license

MIT (see `LICENSE`). Vendored `vendor/superpowers` is MIT, Copyright (c) 2025 Jesse Vincent.
