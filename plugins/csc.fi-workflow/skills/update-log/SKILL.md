---
name: update-log
description: Reconcile this project's experiment logs with SLURM accounting and verified output files. Use for update log, record results, 更新log, or recording completed project jobs.
---

# Update Experiment Logs

Read [Roihu requirements](../../rules/roihu.md), [check-jobs](../check-jobs/SKILL.md), and the project's `AGENTS.md` and `CLAUDE.md`. Follow existing research-workflow file conventions:

- `experiments/LOG.md`: stage status and weekly index; no intermediate numbers.
- `experiments/weekly/week-YYYY-MM-DD.md`: job history, verified results, and notes.
- `experiments/PITFALLS.md`: operational lessons with evidence for the cause and fix.
- `experiments/PLAN.md`: do not revise research direction here; use `/research-workflow:iterate` when requested.

## Reconcile identities and dates

1. Read known project jobs, including unfinished entries in older weekly files. Query those IDs even when they were submitted before the current week. New discoveries require the configured literal prefix plus account/path or run-metadata evidence; never import all jobs belonging to the user.
2. Query live and accounting state as described in check-jobs. Preserve scheduler Submit/Start/End, State, and ExitCode. Failed queries and delayed accounting leave the last known state intact with an observation note; they are not terminal states.
3. Update an existing job entry in its original weekly file. Do not duplicate a job when it finishes in a later week; the new week's notes may link to that entry. For a missing entry, choose the week of actual submission. If the timestamp is unknown, retain an explicitly undated/provisional note until resolved.
4. Compute Monday in the configured timezone. For example, Wednesday **2026-10-07** belongs to `week-2026-10-05.md`. Use Python date arithmetic locally; do not assume macOS supports GNU `date -d`. State the timezone used and do not relabel remote timestamps without confirming their timezone.
5. Preserve cluster/host, Job ID (including array task identity), and Submit time. Update only changed fields. The code revision comes from the run manifest or runtime evidence, never today's local Git HEAD. Leave missing provenance unknown.

## Record evidence and stage status

6. Read actual output files before adding metric values to Verified Results. Include source path, run/job identity, data split, and verification date. Preserve metric definitions and label partial coverage. Do not copy remembered numbers or infer results from a successful scheduler exit.
7. A scheduler-completed job with unverified outputs means `completed; results pending verification`. Mark a LOG stage done only when its planned outputs and acceptance checks are verified. Track failed, cancelled, or incomplete runs separately from scientific conclusions. For arrays, assess task coverage before marking the stage complete.
8. Record relevant observations in Notes. Add a PITFALLS entry only for a supported, reusable lesson; keep an unconfirmed root cause labeled as a hypothesis. Preserve the existing table schema; place extra timing/resource information in notes if no column exists.
9. Add missing weekly index links and report the material changes and unresolved checks. An explicit request to update logs authorizes these evidence-based edits; a separate preview approval is unnecessary.

If research files are missing, use `/research-workflow:init` when initialization is within scope, or report the missing scaffold while preserving collected evidence. Do not change old results, research decisions, or historical cluster names merely because the project has migrated.
