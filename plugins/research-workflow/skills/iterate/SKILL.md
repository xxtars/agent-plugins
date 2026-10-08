---
name: iterate
description: Reconcile an ML research plan with verified results, checked sources, or an explicit user change of direction. Update PLAN Story/Design and append an evidence-linked iteration record, preserving unresolved proposals as provisional.
---

# Iterate PLAN based on recent results

Research is a loop: execution results feed back into Story (L1) and Design (L2). This skill surfaces that feedback explicitly and records it in PLAN.md's Iteration Log (L4), so direction changes have an audit trail instead of silently happening in the user's head.

See [the iteration conventions](../../rules/iteration-workflow.md) for triggers and evidence.

## Prerequisites

- Project has `experiments/PLAN.md` with the four-layer structure (run `/research-workflow:init` if not)
- Experimental conclusions require Verified Results. Initial or revised user direction and checked source corrections can be recorded before experiments exist; label their evidence type.

## Steps

### 1. Read current state

- `experiments/PLAN.md` — current Story (L1), Design (L2), and existing L4 entries
- `experiments/LOG.md` — stage status
- Most recent 1–2 weekly logs under `experiments/weekly/` — focus on **Verified Results** and **Notes** sections
- `experiments/PITFALLS.md` — recent entries

### 2. Identify triggers

Walk through the evidence and look for any of the following (see `rules/iteration-workflow.md` for the full list):

- A verified result **contradicts** a claim in Story
- A baseline or ablation **outperforms** the proposed method
- A planned experiment was **abandoned** without explanation in L4
- A **new experiment** was added that isn't in L2 Design
- The **metric** effectively changed
- A PITFALLS entry invalidates an assumption baked into Story or Design
- An explicit user instruction changes scope, or a checked primary source corrects a design assumption

If a trigger already has a matching L4 entry, don't propose a duplicate.

### 3. Propose candidate entries

Present a numbered table with columns: `#`, `Type`, `Target`, `Proposal`.

- **Type**: `L1 edit` / `L2 edit` / `L4 entry`
- **Target**: specific PLAN.md section; flag needed changes in other records separately without writing them from this skill
- **Proposal**: concrete text to be written — not "update the metric row", but the actual one- or two-line edit

Every proposal must cite evidence: a verified result path, a specific weekly section, a PITFALLS entry, a checked primary source, or a dated user instruction. User intent supports scope, not empirical claims. If the user instruction has no existing file reference, quote it briefly with its date in the L4 evidence field.

### 4. Resolve authorization

Apply explicit user-requested edits within their stated scope without asking for the same approval again. Keep agent-proposed alternatives provisional. For a substantive direction change not already authorized, present the concrete edits for approval:

Accept any of:
- `all` → apply every proposal
- `N, M, ...` → approve the listed numbers
- `edit N: <text>` → replace proposal N before applying
- `skip N` → drop proposal N
- `quit` → apply nothing

Include the corresponding factual L4 entry with every authorized L1/L2 edit. The audit entry is part of recording the same change, not a separate direction decision.

### 5. Write approved changes

- **L4 entries**: append to `## 4. Iteration Log` in PLAN.md. Use the standard format (Trigger / Change / Evidence).
- **L1/L2 edits**: in-place edit of the relevant PLAN.md section. Preserve surrounding structure.
- **Stay in PLAN.md**: don't write to weekly logs, LOG.md, or PITFALLS.md from this skill — those are execution/operational layers, not plan layer. Mixing layers here erodes the distinction this plugin exists to maintain.

### 6. Report

List what was written to PLAN.md. Suggest:
- `git diff experiments/PLAN.md` for review
- Paper alignment check: Story edits likely need Overleaf abstract/intro update, Design edits likely need experiments.tex update (not done by this skill)

## Principles

**Respect the user's scope.** Existing explicit authorization permits the corresponding edit; it does not turn an unrelated agent proposal into an adopted research direction. Record the evidence and append the L4 audit entry for every Story/Design change.

**Every proposal cites evidence.** Triggers without pointers are opinions, and opinions don't belong in L4 — the value of L4 is that a future reader can verify *why* the plan changed. "Metric inconsistency spotted last week" with no file reference is a dead entry.

**Stay in the plan layer.** The execution layer (weekly/LOG/PITFALLS) has its own curators — the cluster plugin, the user, PITFALLS triage. Editing those from iterate would make two skills fight over the same files.

**No duplicate L4 entries.** If the trigger already landed in L4, propose nothing new for it. Duplicate entries rot the audit trail and make "what actually changed?" harder to answer.

**If nothing triggers, exit quietly.** Forcing an L4 entry every run trains the user to ignore L4. A clean "no changes needed — latest results are consistent with current Story/Design" is a better signal than a manufactured update.

## What this skill does not do

- Run experiments (that's the cluster plugin)
- Verify numbers from output files (that's `update-log` in the cluster plugin)
- Sync paper with PLAN (that's for overleaf-workflow, future `sync-narrative` skill)
- Manage agent memory or automatically summarize a session
