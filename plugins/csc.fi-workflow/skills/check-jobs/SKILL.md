---
name: check-jobs
description: Check current and recent SLURM jobs for this project, including queue reasons, accounting, and failures. Use for job status, queue, experiment progress, or debugging a stopped run.
---

# Check Project Jobs

Read [Roihu requirements](../../rules/roihu.md), `AGENTS.md`, and the project's `CLAUDE.md` Cluster block. Load job IDs from the request and project logs, including unfinished jobs from older weeks.

## Query without losing failure information

1. Prefer explicit known IDs. For discovery, query the configured user, then filter by literal project prefix and verify account and working directory/run metadata. If scope is unresolved, ask for IDs; do not silently adopt all user jobs.
2. Use bounded SSH and keep each query's status and stderr separately. `squeue` provides live state, reason, and elapsed time. `sacct` provides recorded state, Submit/Start/End, and ExitCode. Use delimiter-separated fields, sufficiently wide names, and both array-form and raw job IDs; avoid parsing presentation columns. A typical accounting query for validated IDs is:
   ```bash
   sacct -X -j <comma_separated_job_ids> --starttime=<search_start> --noheader --parsable2 --format=JobID%64,JobIDRaw,Cluster,JobName%100,Account,Partition,State%30,ExitCode,Submit,Start,End,Elapsed
   ```
   Field meanings and query options follow the [official sacct reference](https://slurm.schedmd.com/sacct.html). `JobID` preserves `ArrayJobID_ArrayTaskID`; `JobIDRaw` records the underlying numeric identity. Choose a search start that includes the tracked submission dates. Preserve array task identities; expand relevant task records rather than treating an array parent as proof all tasks succeeded.
3. A nonzero query exit status means the check failed, not that the queue is empty. On partial failure, report the successful query's evidence and label the remaining state unknown. Preserve the last known good state and its timestamp.
4. A job absent from `squeue` is unresolved until accounting confirms its terminal state. Account for accounting delay and possible requeue transitions. `COMPLETED` with ExitCode `0:0` confirms scheduler success, not valid research outputs. For other states, retain the exact state and exit code; do not collapse timeout, OOM, cancellation, and node failure into success.

## Diagnose and report

For failed or stuck project jobs, inspect relevant `scontrol show job` metadata, accounting step records when needed, and a bounded tail of the actual stdout/stderr paths. Do not assume a default log name or enumerate unrelated project output. Label inferred causes as hypotheses until supported by logs.

Report job ID, name, state/reason, timing, and the next useful action. Display other projects only if the user requested an account-wide view. Status checking does not authorize cancellation, requeue, resubmission, or recurring monitoring. Use `/csc.fi-workflow:update-log` to persist supported project state and verified results when appropriate.
