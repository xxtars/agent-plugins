# Research File Conventions

A research project has four layers that feed each other in a loop. These files make the loop explicit.

```
experiments/
├── PLAN.md            ← Story, Design, Execution pointer, Iteration log
├── LOG.md             ← Execution state: stage status + weekly index
├── weekly/
│   └── week-YYYY-MM-DD.md   ← Execution detail: jobs, results, notes
└── PITFALLS.md        ← Operational lessons sedimented from execution
```

## The four layers (PLAN.md)

| # | Layer | What it holds | Aligned with |
|---|-------|---------------|--------------|
| L1 | **Story** | Core claim, why it matters, current stance on what's settled vs debatable | Paper abstract + introduction |
| L2 | **Design** | Pipeline, experiment matrix, metrics, baselines, ablations | Paper experiments + appendix tables |
| L3 | **Execution Status** | Pointer to LOG.md and latest weekly — **no duplicated numbers** | LOG.md |
| L4 | **Iteration Log** | Every time results force a Story/Design change, append one entry with Trigger / Change / Evidence | — |

**Key rule**: PLAN.md is the hub for hypotheses, decisions, and design. Measured results live in weekly logs with links to their original outputs and are mirrored into the paper once verified. Planned hyperparameters and metric definitions may live in PLAN; they are not measured results.

## Initialization and evidence status

Root `AGENTS.md` is the project entry point for agents. Keep it concise and link to the research files. Where Claude compatibility is needed, `CLAUDE.md` may import it with `@AGENTS.md`; keep an existing Cluster section in CLAUDE as a single configuration source.

Initialize from the user's discussion when requested. Distinguish confirmed user intent, source-verified facts, proposed methods, and unresolved questions. Do not convert an assistant recommendation into a user decision or a hypothesis into a demonstrated contribution. Dated user-direction notes can support initial scope or a scope change; they cannot support empirical claims.

Rerunning init creates missing files and preserves existing history. A source ledger such as `SOURCES.md` is optional; use primary links and inspection dates when external facts influence design.

## Layer responsibilities

### L1 Story
One paragraph for each of:
- **Core claim or hypothesis** — what you will test; mark it unverified until evidence supports it
- **Why it matters** — the gap this closes
- **Current stance** — what's settled, what's open

Changes to Story should trigger an L4 entry and (usually) a paper abstract/intro update.

### L2 Design
- **Pipeline** — data → training → evaluation
- **Experiment matrix** — which combinations matter, which ablations
- **Metrics & baselines** — what you report, what you compare against
- **Evaluation protocol** — dataset/split provenance, comparable training conditions, validation-only selection and calibration, held-out testing, and the evidence that would reject the hypothesis

Changes to Design should trigger an L4 entry and (usually) a paper experiments.tex / appendix update.

### L3 Execution Status (pointer only)
A short block like:

```markdown
## 3. Execution Status
See [LOG.md](LOG.md) for stage progress. Latest weekly: [weekly/week-YYYY-MM-DD.md](weekly/week-YYYY-MM-DD.md).
```

Do **not** copy numbers from weekly logs into PLAN.md — they rot.

### L4 Iteration Log
Append-only, reverse-chronological or chronological (pick one, be consistent). Each entry:

```markdown
### YYYY-MM-DD — Short title
- **Trigger**: <what result caused the update>
- **Change**: <what moved in Story (L1) or Design (L2)>
- **Evidence**: <pointer to verified results, PITFALLS, primary source, or dated user-direction note; label the evidence type>
```

This is the audit trail of why the research shape changed. Without L4, "why did we stop pursuing X?" becomes unanswerable a month later.

## LOG.md conventions

- **Stage table**: stage / status / brief notes. Status ∈ {pending, running, done, blocked}
- **Weekly index**: one row per weekly log with a one-line summary
- **No intermediate numbers** in LOG.md. Only milestone results. Numbers here go stale and mislead future sessions.

## Weekly log format (three sections)

### 1. Job History (update immediately)
Table of every run: name, id, submission info, status, commit hash.
Link each run to its exact command/configuration, code revision (and dirty diff if applicable), model revision, dataset/split version, seed, environment/container, and output paths. These can be in a run manifest linked below the table rather than extra table columns. Record the code actually run on the execution host, not an unrelated local commit. Unavailable metadata is explicitly unknown.

### 2. Verified Results (write only after reading actual output)
- MUST read the actual output file/logs before writing any number
- Tag with source and date: `"verified from <path>, <date>"`
- Link the run record and state the evaluation split and metric definition; retain predictions when needed to reproduce aggregate metrics.
- For in-flight jobs: mark "partial"

### 3. Notes
Observations, decisions, analysis, and dated user-direction summaries. **No unverified performance numbers**; those go in section 2 only after checking outputs. External model claims belong in source notes, not the project's Verified Results.

## PITFALLS.md

One entry per pitfall, using this structure:

```markdown
## Descriptive title
- **Symptom**: what you observed
- **Root cause**: confirmed cause, or explicitly labelled hypothesis
- **Fix**: action taken and verification, or pending next step
- **Date**: YYYY-MM-DD
```

When a pitfall also invalidates an assumption baked into Story or Design, add an L4 Iteration Log entry in PLAN.md pointing to the pitfall.
