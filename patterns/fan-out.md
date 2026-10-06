# fan-out

**Problem.** Research-heavy questions answered serially burn wall time and stuff one context with
sources it will never use again.

**Shape.** One question per fresh-context subagent, in parallel; the parent **distills** their
reports into the durable knowledge layer itself:

1. Split the research into independent questions (a question is a unit if it can be answered
   without its siblings).
2. Delegate: **background subagents, one per question** — fresh context, `base`/`research` role
   (cheap; these children never inherit conversation — *each delegation is born from a single
   goal string*).
3. Distill: the parent folds the reports into the durable notes with **sources and dates** —
   *never paste raw reports into durable notes*. Compile, don't RAG.

**Rules.**
- **Cite or omit:** every claim a user could act on carries source + date; fluency without a cite
  is a bug. A number typed into prose is a bug even when it is right — *compute, don't compose*.
- Raw reports are disposable; the compiled digest is the artifact — an immutable source layer, a
  compiled digest on top of it, and chronological logs beside both.
- **Keep the absorb path always:** if the delegation route fails silently, absorb the scope inline
  and keep going — a stuck delegation route is a harness fact, not a plan change.

**When.** Market/tech research, comparative surveys, triage of many clippings, any "find out X, Y,
Z" with separable questions.

**When not.** Questions that need dialogue with the unknown — run `expand-contract` first to find
the questions, then fan out.

**Cost profile.** N cheap research turns + 1 distillation pass; the parent's context stays clean
because sources arrive summarized with provenance.
