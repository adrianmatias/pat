# principles

The steering layer every pattern obeys. Distilled from practice on real ML/data systems, where the
working rule was "objective > foundation > code-standard": a measurable target, the invariants that
protect it, then the code rules. Patterns may specialize these but never contradict them.

## load-bearing invariants

- **Simplicity leads.** Compression is the core of intelligence; fight complexity aggressively.
  A pattern must earn its lines — delete it when it stops paying rent. No features on features.
- **Root-cause always.** No patches on superficial fixes. A pattern that fails here gets fixed or
  removed — never wrapped in another pattern.
- **Walking skeleton.** Prefer one thin end-to-end live path over deep offline machinery. Prove the
  route before scaling the swarm.
- **Bayesian, not point estimates.** Don't discard uncertainty: a 7/10 draft is not a 70% chance
  of done. Heterogeneous mixes and adversarial passes exist because single estimates lie.
- **Sovereign tech.** Open-source first, local-first, no vendor lock-in. The router, the ledger and
  the patterns live on your machine; only the tokens travel.
- **Generalization over memorization; calibration is not a patch.** Tools and prompts must fit the
  inference they'll actually see — a pattern tuned on one repo is a hypothesis, not a law.

## orchestration discipline

- **Decide before you parallelize.** A plan is decision-complete or it is not a plan (sandwich
  stage 1). Do not swarm a single design decision; do not ticket a young interconnected system.
- **Swarm only independent chunks.** No shared mutable state between workers; merge is where
  conflicts die.
- **Contracts before loops.** Every iterative pattern (sr-and-jr, cascade) runs against a written
  contract/verifier, not vibes.
- **Reviewers never share the author's family.** Different families, different blind spots.
- **TDD is not a leash.** For highly capable agents, strict red-green loops are micromanagement: drive work with a written acceptance contract, verify with evidence, treat tests as disposable development aids. Keep strict TDD for weak models, high-stakes cores, or when the human asks.
- **Evidence before claims.** Validation returns failing cases, not adjectives. (Verification
  before completion.)
- **Refactor first, then change** — never both in one diff. A move plus a behaviour change cannot
  be reviewed from its test delta.
- **Steering ladder:** `objective > foundation > code-standard > behaviour tests >
  performance-analysis > implementation`. Conflicts escalate upward; lower tiers obey higher ones.
- **A gate is only a gate once it has been seen to fail.** Plant a defect, watch the gate go red
  (`--prove`), or admit it is decoration.
- **Compute, don't compose.** A quantified claim enters work through a tool that recomputes it; a
  number typed into prose is a bug even when it is right. **Cite or omit:** source + date for
  anything a user could act on.
- **Human attention is the bottleneck** — decisions and kills over options; at most 3 human gates,
  only for facts only the human holds.
- **Keep the absorb path.** A failing delegation route is absorbed inline and the work continues.

## provenance

Distilled from practice on real ML/data systems, then reinforced by `vendor/superpowers`
(evidence-before-claims, TDD, systematic debugging) and a conjecture-register discipline: state →
test → destroy or promote; a conjecture not on the register is a vibe. The invariants above are the
collection's own; where they and any host project disagree, the host's written contract wins for
that host and this file is amended — never forked silently.
