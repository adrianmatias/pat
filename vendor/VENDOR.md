# vendored: obra/superpowers

Upstream: https://github.com/obra/superpowers — Jesse Vincent's skill library for coding agents.
Pinned at `8ca22db` ("Release v6.4.2", 2026-09-25). License: MIT, Copyright (c) 2025 Jesse Vincent
(see `superpowers/LICENSE`; the notice must travel with any copy — vendoring here preserves it).

Vendored as plain files (no nested git). To update:

```sh
git clone https://github.com/obra/superpowers.git /tmp/sp && cd /tmp/sp
git log -1 --format='%h %s'          # record the new pin here
rm -rf vendor/superpowers && mv /tmp/sp vendor/superpowers && rm -rf vendor/superpowers/.git
```

What this collection takes from it (see `router/index.md` → skills): `brainstorming`,
`writing-plans`, `subagent-driven-development`, `systematic-debugging`, `test-driven-development`,
`verification-before-completion`, `dispatching-parallel-agents`, `writing-skills` — loaded **on
demand** from `superpowers/skills/<name>/SKILL.md`. Their tool vocabulary is Claude Code's; this
collection's `AGENTS.md` maps it (Agent→subagent/subagent_fork, Skill→skill, todos→todo_write).

Note: superpowers ships an optional telemetry asset (brainstorming visual companion) with
`SUPERPOWERS_DISABLE_TELEMETRY` opt-out; unused here since we load skill files as text.
