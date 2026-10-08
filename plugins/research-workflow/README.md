# research-workflow

Keep a research question, its experimental design, and the evidence from runs in
connected project files. Initialize the records from an existing discussion,
then revise the plan when verified results or an explicit direction change call
for it.

## Skills

| Skill | Use it to |
| --- | --- |
| [init](skills/init/SKILL.md) | Create missing research records or complete an existing setup while preserving its history |
| [iterate](skills/iterate/SKILL.md) | Reconcile the plan with verified results, checked sources, or a requested change of direction |

## Project files

    AGENTS.md
    experiments/
    ├── PLAN.md
    ├── LOG.md
    ├── weekly/
    │   └── week-YYYY-MM-DD.md
    └── PITFALLS.md

AGENTS.md is the project entry point. PLAN.md contains four layers:

| Layer | Purpose |
| --- | --- |
| Story | The question, claim, significance, and current interpretation |
| Design | Methods, comparisons, metrics, and planned experiments |
| Execution status | Pointers to operational records |
| Iteration log | Dated changes to the plan and the evidence for them |

LOG.md holds stage status and a weekly index. Weekly records separate job
history, verified results, and notes. PITFALLS.md records operational problems
and their fixes. An optional SOURCES.md keeps external evidence that materially
informs the plan.

## Typical use

1. Ask the agent to initialize the project from the available discussion and files.
2. Record jobs and verify their output before adding performance claims.
3. Ask it to reconcile PLAN.md when results or the research direction change.

Proposed methods remain provisional until adopted. A user decision establishes
intent; it does not establish an experimental result. Existing records are
preserved when initialization is run again.

## Related workflows

csc.fi-workflow uses these conventions for execution records. overleaf-workflow
manages manuscript files separately. Updating PLAN.md does not run experiments,
change a manuscript, or populate an agent's persistent memory.

See [file conventions](rules/research-files.md) and
[iteration guidance](rules/iteration-workflow.md) for the detailed rules.
Installation options are described in the [repository README](../../README.md).
