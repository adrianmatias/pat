# pat — agent contract

Portable orchestration rules for this collection. One entry, lazy depth. Other harnesses map their
own tools to it.

## entry (every task)

1. Read **`router/index.md`** — that is the whole routing surface (one screen).
2. Route the task: `node router/choose.mjs "<task>"` (Jev picks the pattern) or the index rubric.
   State the route in one line before working (`route: cheap-swarm/mid · skills: run-cheap-swarm`).
3. Load **only** what you routed: the pattern file and the named skill. Nothing else. Do not read
   the catalog "just in case" — lean context is the point.
4. Run the pattern. Respect its kill switches and "when not to".
5. Record what the pattern got wrong (right back to the source, not around it — root-cause always).

## tool vocabulary (superpowers → this harness)

`Agent` → `subagent` (fresh context) or `subagent_fork` (inherits trail — reviewers only);
`Skill` → `skill` / direct file read of `SKILL.md`; `todos` → `todo_write`; parallel dispatch →
`workflow` `parallel()`/`pipeline()`; per-dispatch model choice → `provider`/`model` options on
`agent()`; long-running objectives → `create_goal`/`update_goal`.

## invariants

`PRINCIPLES.md` is the steering layer over every pattern. Two are load-bearing here: **simplicity
leads** (a pattern is removed as soon as it stops earning its lines), and **root-cause always** (a
pattern that fails gets fixed or deleted — never a patch on top).

## mounting

`INSTALL.md` carries the install recipes (vault/project symlink, DSH preset, vendored, submodule),
the log contract to copy into a host, and the acceptance check. This file is the contract; keep it
the only standing context — everything else loads on demand.
