# install pat into a project or vault

Four recipes, cheapest first. Every one ends in the same acceptance check. Nothing here is
harness-specific except recipe 2.

## prerequisites

- `git` — to obtain pat and to keep it current.
- `node` — **only** for `router/choose.mjs` (the Jev fast path). No other part of pat executes.
- `OPENROUTER_API_KEY` — **only** for that same script, read from the environment or from
  `~/.dsh/.credentials.yaml`. With no key it exits 3 and `router/index.md`'s rubric routes instead.
  **Routing never hard-depends on the key.**

## 1 · vault or project by symlink (recommended)

One canonical clone, mounted where it is used. No copies, no drift.

```bash
git clone git@github.com:adrianmatias/pat.git ~/pro/pat      # the canonical clone
ln -s ../../pro/pat /path/to/vault/pat                      # relative: adjust ../ for your depth
```

Then record it: `cd /path/to/vault && git add pat`. Git stores a symlink (mode `120000`), not the
249 files.

- **Use a relative link** if the vault is versioned — an absolute one is stored verbatim and breaks
  on any machine whose layout differs.
- Mount targets must be **disjoint**: never link a vault into itself, or pat into another mount of
  pat. Obsidian's own rule, and it is the one that bites.
- Obsidian **does not watch** the target: edits made to pat through git or an agent do not refresh
  the vault's search index or graph until a reload/reindex. That is the price of not duplicating.
- pat carries **no `.obsidian/`** (gitignored): the host vault owns editor config, so mounting
  cannot leak or fight device state.

## 2 · DSH agent preset by symlinked skills

A preset mounts `skills/` from its own directory, so link the three recipes in and they stay live —
editing pat updates the preset with no recopy:

```bash
ln -s ~/pro/pat/skills/run-sandwich      ~/.dsh/.agent-presets/<preset>/skills/run-sandwich
ln -s ~/pro/pat/skills/run-cheap-swarm   ~/.dsh/.agent-presets/<preset>/skills/run-cheap-swarm
ln -s ~/pro/pat/skills/run-sr-and-jr     ~/.dsh/.agent-presets/<preset>/skills/run-sr-and-jr
```

Point the preset's own context at `pat/AGENTS.md` (the entry contract) and keep that line the only
standing context — patterns, skills and `models.md` load on demand.

## 3 · generic harness by vendored files

Copy `AGENTS.md` into the harness's standing context and `skills/*` into its skills directory. The
`SKILL.md` format is agentskills.io-shaped; the `workflow` JS inside needs that harness's subagent
tool, and `AGENTS.md` maps Claude Code's names (`Agent` → `subagent`, `Task` → `workflow`, …).

This duplicates: re-copy after a pull, or accept the drift knowingly.

## 4 · sibling project by submodule

The repo stays independently clonable and the pin is recorded in the parent:

```bash
git submodule add git@github.com:adrianmatias/pat.git pat
```

Prefer this when the parent pins versions of several collections at once. It costs a
`git submodule update --init` for anyone who clones the parent.

## the log contract (do this at install, not later)

pat assumes each host keeps an append-only trail; that is what survives a lost session or a harness
swap. Give the host a `log/` with `mat.md` (the human's prompts; the task is the latest prompt at the
bottom) and `agt.md` (the agent's replies, one entry per round, `H1 = date`), then copy the `## log`
block from pat's own [`AGENTS.md`](AGENTS.md) into the host's `AGENTS.md`. Keep it to those few
lines: the host's `AGENTS.md` is the only always-loaded context.

pat's own [`log/`](log/) is this contract applied to pat — read it as the worked example.

## acceptance check (run it, do not assume it)

```bash
node ~/pro/pat/router/choose.mjs "a small doc fix"     # expect: route: <pattern> · confidence …
head -3 /path/to/vault/pat/router/index.md             # expect: the router screen
ls /path/to/vault/pat/patterns | wc -l                 # expect: 9
```

Routing answered, router readable through the host's own path, patterns present. If the first line
says no key, recipe 1–4 still hold — only the fast path is missing.

## keeping it current

```bash
git -C ~/pro/pat pull
```

Everything mounted follows, because nothing was copied. Vendored installs (recipe 3) are the
exception and must be re-copied by hand.
