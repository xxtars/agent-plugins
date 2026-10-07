---
name: init
description: Initialize or complete an ML research project's AGENTS.md and experiment records (PLAN, LOG, PITFALLS, weekly log). Use when starting a research project or setting up experiment tracking, including initialization from an existing discussion.
---

# Initialize research workflow

Read [the file conventions](../../rules/research-files.md). Templates are in [../../templates/](../../templates/), relative to this skill, not the user's project.

## Establish the starting point

- Inspect project and ancestor instructions, existing research files, and repository status before editing.
- Use the user's current discussion and supplied material. Separate **confirmed user intent**, **source-verified facts**, **proposed choices**, and **open questions**. A model mentioned in a question is not an adopted model; a proposed research claim is not a result.
- If the user asks to initialize from the discussion, populate supported context immediately. Ask only for information needed for the next dependent action; unresolved datasets, accounts, paths, or budgets do not block local documentation.
- If no research context exists, retain focused prompts for the missing content rather than inventing a direction.

## Initialize the files

1. Create or extend root `AGENTS.md` with project scope, links to research records, evidence rules, and applicable user instructions. Keep project-specific models and hypotheses in PLAN, not in this reusable skill. Preserve existing instruction files. If Claude compatibility is useful, create a small `CLAUDE.md` that imports `AGENTS.md` with `@AGENTS.md`; preserve any existing Cluster section there as the single source of cluster configuration.
2. Create `experiments/PLAN.md` from [the PLAN template](../../templates/PLAN.md):
   - **L1 Story**: research question or hypothesis, motivation, confirmed constraints, candidate choices, unresolved decisions.
   - **L2 Design**: proposed comparisons, evaluation protocol, and evidence needed to support or reject the hypothesis. Mark agent-proposed design as provisional.
   - **L3 Execution Status**: links to LOG and weekly records; no measured results.
   - **L4 Iteration Log**: initial scope and subsequent changes, with evidence. For initial scope, a dated summary of explicit user intent in weekly Notes is valid evidence; it is not an experimental result.
3. Create `experiments/LOG.md` from [the LOG template](../../templates/LOG.md). It may contain real setup progress and planned stages, but no invented completed runs or measurements.
4. Create `experiments/PITFALLS.md` from [the pitfalls template](../../templates/PITFALLS.md). Include previously observed issues only when supported by evidence; distinguish suspected causes and pending recovery from confirmed fixes.
5. Create `experiments/weekly/week-YYYY-MM-DD.md` from [the weekly template](../../templates/weekly/week-TEMPLATE.md), using Monday of the current week in the user's timezone. Keep Job History and Verified Results empty when no runs exist. Notes may record the discussion, provenance, actual setup work, and next actions.
6. If external models or papers informed the plan, add a concise `experiments/SOURCES.md` only when useful. Include primary URLs, inspection dates, supported facts, and unresolved claims. Pin code/model/data revisions when selected for a run; don't invent revision hashes.

## Preserve existing work

Rerunning initialization must preserve existing entries and results. Create missing files; make small, authorized merges; append dated research changes to L4. Do not replace a populated file with a template. Ask about a real conflict or destructive replacement only if the user's request does not resolve it.

Check generated relative links: do not copy plugin installation paths into the project's PLAN. Reuse existing valid project links and avoid duplicate rule stores.

## Cluster handoff and completion

If a cluster is relevant, reuse known settings from project files and session evidence. Mark missing settings explicitly; do not guess a SLURM account, remote path, GPU budget, or successful login. Local initialization can finish while remote access is unavailable. Use `/csc.fi-workflow:configure` when available and when configuration work is needed.

Initialization alone does not start training, download model weights, create remote resources, or schedule a watcher. Finish by reporting files created/updated, the provisional research direction, and the next unresolved dependency. Use `/research-workflow:iterate` when evidence or an explicit user decision changes Story or Design.
