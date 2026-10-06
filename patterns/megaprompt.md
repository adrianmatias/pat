# megaprompt

**Problem.** Chatty planning sessions leak decisions into conversation; the model interviews you,
offers option menus, and the actual context never arrives whole.

**Shape.** Compose **one entire file** and paste it as the *first user message* to a deep planning
model. Do not chat around it first. **Demand a decision-complete plan — not an interview and not an
options menu.** Write it to be attacked rather than admired.

## anatomy (of a complete super-prompt)

1. **The claim under test** + the constraints the plan must respect (including repo doctrine the
   task seems to violate — the plan must resolve the conflict, not dodge it).
2. **Operator + budget as a rate, not a lump sum** (the operator's attention per month), and the
   execution model — the sandwich, not a fresh invention.
3. **Verified facts** with sources/hashes: *"verified — do not re-research"*.
4. **Ground truth**: the measured state, adversarially verified where numbers matter.
5. **The structural decision + the unknowns** — each unknown gets a forced default verdict; *an
   unresolved unknown is not a licence for a conditional plan*.
6. **Decision agenda** (D1…Dn): *"answer every one, in this order… give the argument, not the
   options"*; every section ends in a **decision or a kill**; at most 3 human gates, and only for
   facts only the human holds.
7. **Acceptance tests + operational kill criteria** ("If the exam lands first, X stops") —
   kill criteria must be operational, not sentimental.
8. **Required output + quality bar**: *"If a section could be a McKinsey slide, rewrite it until a
   coding agent knows which file to create tomorrow."*

**Rules.**
- Facts are pre-verified so the planning tokens buy decisions, not research.
- The forced-verdict rule is the whole point: no conditional plans, no menu back to the human.
- Feed the output to `sandwich` execution or `writing-plans` decomposition next.

**When.** Kicks off a large effort: project plans, migrations, architecture. Once per effort, not
per turn.

**When not.** Small tasks (overhead exceeds the task), exploration (`expand-contract` first),
anything whose facts you haven't verified yet (verify first — unverified facts in a megaprompt get
plausible lies baked into the plan).
