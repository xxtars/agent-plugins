---
name: submit
description: Submit an authorized SLURM job and record its actual remote revision, resources, and job identity in the experiment log. Use for submit job, sbatch, 提交实验, or immediately before a cluster job submission.
---

# Submit and Record a Job

Read [Roihu requirements](../../rules/roihu.md), [shell conventions](../../rules/slurm-shell.md), project `AGENTS.md`, `CLAUDE.md`, and the relevant experiment plan. Reuse the user's existing authorization and resource budget; do not ask for the same approval again. A request only to plan or inspect is not a submission request.

## Prepare a reproducible run

1. Resolve and verify host, account, remote path, data paths, runtime architecture, and requested partition/resources. Check current partition limits. For Roihu GPU work, verify ARM compatibility; do not reuse a Mahti x86 environment by assumption. Use a short smoke test before expensive unvalidated work, within the authorized budget. Short GPU tests use `gputest`; longer or production workloads use normal partitions. Permitted light preparation/checksums may run on login nodes within CSC limits; model tests and heavy preparation require compute allocations.
2. Inspect the exact batch script and its arguments. Confirm GPU count per node, nodes, CPU/memory request, time limit, output paths, dependencies, and array size/concurrency. Check the total planned resource use, not just one array task. Check that model and resource-heavy computation occurs inside the batch job or allocated step; login-side wrappers may perform bounded light preparation and integrity checks. Record real job ID, partition and execution hostname after allocation; an environment variable alone is not node evidence. Propagate workload failures as described in the shell conventions.
3. Record the full **remote** commit and remote worktree status before submission. Prefer a clean immutable per-run checkout. If authorized uncommitted changes are required, save a bounded patch/content manifest with the run, excluding secrets, and record its checksum. Do not substitute the local `HEAD` for evidence of remote code.
4. Create a unique run ID and durable run manifest containing code/snapshot path, exact command/config, model revision, data split/version, seeds, container/runtime identity, resources, and intended outputs. Unknown scientific fields remain explicitly unknown. Preserve the snapshot until queued/running jobs no longer use it; SLURM saving the batch script does not freeze other source files.

## Submit once and capture the outcome

5. From the verified remote run directory, invoke `sbatch --parsable` with the prepared arguments. Capture stdout, stderr, and exit status. Parse the successful response as a numeric job ID, optionally followed by `;cluster`; keep the cluster identity.
6. If the connection drops or the outcome is ambiguous, search `squeue`/`sacct` and submission records using the unique run ID/name, working directory, and time window **before retrying**. Do not create duplicate jobs to resolve uncertainty. If reconciliation remains inconclusive, record `submission outcome unknown` and leave retry pending.
7. Query scheduler metadata for the accepted job. Record actual Submit time, account, partition, resource request, paths, and initial observed state. If metadata is delayed, record accepted submission with state/time awaiting confirmation rather than inventing `PENDING` or `RUNNING`. For arrays, retain the parent and intended task range, then track task outcomes.

## Record locally

Use the project's existing research-workflow files and schema. If scaffolding is missing, use the installed research-workflow template or retain a durable submission note; missing documentation must not cause a second submission.

Add one Job History entry per accepted job to the week containing its actual submission date (Monday boundary, project timezone). Until scheduler time is available, label the client submission timestamp as provisional. Preserve the table's columns:

| Job Name | Job ID | Pipeline | Dataset | Partition | Submitted | Started | Finished | Status | Commit |
|----------|--------|----------|---------|-----------|-----------|---------|----------|--------|--------|
| `<name>` | `<job_id>` | `<stage>` | `<dataset or unknown>` | `<partition>` | `<time, timezone>` | | | `<observed state>` | `<remote revision>` |

Link the run manifest in nearby notes; retain the full commit and cluster identity there if the table uses a short hash. Update the corresponding LOG stage to pending/running only when supported by the observation. Record a rejected submission or uncertain outcome in Notes, with no fabricated Job ID and no claim a job ran.

Report accepted job IDs, resources/time limit, remote revision/run path, and log location. If submitting a batch, record every accepted job and any rejected/uncertain requests separately. Do not automatically resubmit a failed job or enlarge the approved budget.
