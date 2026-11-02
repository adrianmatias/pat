# pat

A pattern book for agentic engineering: ten orchestration shapes for AI coding agents, a one-screen
router that picks one per task, the skills that run them, and model roles instead of model names.

- **Route a task:** `node router/choose.mjs "<task>"` → `route: <pattern> · confidence …` (needs
  `OPENROUTER_API_KEY`; without it, use the rubric in [`router/index.md`](router/index.md)).
- **Then load only what you routed:** `patterns/<pattern>.md` and its skill under `skills/`.
- **Patterns:** `direct` · `sandwich` · `adversarial` · `expand-contract` · `megaprompt` ·
  `cheap-swarm` · `sr-and-jr` · `cascade` · `duel` · `fan-out` (one-liners below).
- **Invariants:** [`PRINCIPLES.md`](PRINCIPLES.md), the steering layer every pattern obeys — the long
  form is at <https://taoaieng.io/principles/>. Install recipes: [`INSTALL.md`](INSTALL.md).

## why

Most agent waste is one of two kinds: **context** loaded that never gets used, or **money** spent on
a frontier model doing slice work. One small routing screen decides the *pattern* (how work is
shaped), the *model roles* (who plays which part), and the *skills* (which executable recipe to
load) — everything else stays on disk.

## layout

```
INSTALL.md         how to mount pat into a project or vault (4 recipes + acceptance check)
router/index.md    the router: one screen — catalog one-liners, routing rubric, roles, load contract
router/choose.mjs  picks the pattern for a task (one Decisions-API call; the rubric is the fallback)
patterns/          portable pattern designs (human-readable, harness-agnostic)
skills/            executable recipes, SKILL.md format, loaded only when routed
models.md          model roles → concrete ids (machine-local), cache discipline
PRINCIPLES.md      the invariants every pattern obeys (distilled from practice)
AGENTS.md          agent entry contract (keep as the only standing context)
vendor/superpowers vendored skill library (pinned 8ca22db, MIT)
```

## the catalog

| pattern | one line | typical roles |
|---|---|---|
| `direct` | no orchestration: same session, same model, no subagents, no artifacts | none |
| `sandwich` | plan (deep) → execute (cheap, parallel) → validate (independent) | deep + base + review |
| `adversarial` | one review pass by a different model family, findings with failing cases | review ≠ author's family |
| `expand-contract` | breath-in wide/unjudged ↔ breath-out kill-with-reasons, contract ONE move | base × N + judge |
| `megaprompt` | one verified context-complete file → deep model → decision-complete plan | deep single turn |
| `cheap-swarm` | N cheap workers (min 3 / mid 6 / max 12) over identical cached preamble + one slice each | base × N (+ hetero) |
| `sr-and-jr` | junior drafts against a contract, senior repairs it in ≤3 bounded rounds | base loop + deep touch |
| `cascade` | cheapest attempt → mechanical verify → one-tier escalation with failure evidence | base → code → deep |
| `duel` | two drafts from different families, cheap judge against a pre-written rubric | base + hetero + judge |
| `fan-out` | one question per fresh subagent → distill reports with cites | base × N + parent |

## design notes

- Patterns are portable prose; skills are host recipes; the router is the only always-loaded page.
  Three load levels, one screen of standing context.
- Every pattern carries **when not to** and a kill switch. A pattern that fails gets fixed or
  deleted — see `PRINCIPLES.md`.
- Model roles live in `models.md`; adjust the right column to your providers. Prices and catalogs
  drift; this repo owns *roles and shapes*, your router owns prices.
- Distilled from practice on ML/data systems and stitched together with
  [obra/superpowers](https://github.com/obra/superpowers) (vendored, MIT). Self-contained: nothing
  outside this repo is required.

## license

MIT (see [`LICENSE`](LICENSE)). Vendored `vendor/superpowers` is MIT, Copyright (c) 2025 Jesse Vincent.
