---
name: watch
description: Monitor explicitly scoped SLURM jobs over time and notify on meaningful changes. Use when the user asks to watch jobs, monitor experiments, or notify when jobs finish; default interval is one hour.
---

# Watch Project Jobs

Read [Roihu requirements](../../rules/roihu.md), [check-jobs](../check-jobs/SKILL.md), and the project's `AGENTS.md` and `CLAUDE.md`. Monitoring requires a user request to keep checking; a one-time status question uses check-jobs.

## Select targets and scheduler

1. Reuse requested job IDs and project settings. Otherwise discover and verify this project's current jobs using the literal prefix plus account/path evidence. Save the resulting explicit IDs as the initial target set. A missing prefix requires explicit IDs; never fall back to every user job.
2. Default to watching the fixed target set through completion. Add future jobs only when the user requested ongoing project monitoring. If there are no initial targets, report that fact; do not claim experiments completed or silently create an indefinite monitor.
3. Use the client's supported scheduler. In Codex, discover and use `automation_update` to create/update a heartbeat on the current thread; inspect existing automations to avoid duplicates. Include the project path, host, target identities, polling interval, persistent state location, and completion conditions in its human-readable prompt. Preserve any user limits or time windows. Do not switch to a standalone cron job unless requested.
4. Default interval is one hour; use a requested shorter interval when supported, avoiding tight login-node loops. Use the scheduler for recurring work. A foreground check must be bounded, with individual waits at most 60 seconds. Do not launch a detached shell loop and promise it will survive the client session. If scheduling is unavailable, state that and offer a one-time check or a supported alternative.

## Preserve state across checks

Store non-secret monitor state under `experiments/.monitor/` (or the scheduler's supported persistent state store). Keep machine state out of research result tables; ignore it in Git when appropriate. Record:

- Cluster/host, target Job IDs including array tasks, known Submit times, and fixed/dynamic discovery mode.
- Last successful observation and its timestamp; tracked IDs remain even after they leave the live queue.
- Each job's last state/reason/ExitCode, accounting reconciliation status, and last notified event.
- Query errors separately from the successful snapshot; never replace that snapshot with an empty value after failure.

Inspect and update the same state on each run; do not run overlapping watchers for the same target set. If state is missing/corrupt, reconstruct from project logs and scheduler evidence rather than assuming completion.

## State transitions

| Observation | Required action |
|-------------|-----------------|
| SSH or scheduler query fails | Keep last good state; mark the observation unavailable. Distinguish authentication, network, maintenance, and sandbox errors. |
| Job is in `squeue` | Update live state/reason; retain its identity for later accounting. |
| Job disappears from `squeue` | Query `sacct`; mark awaiting accounting until a terminal state is confirmed. |
| Accounting is absent/delayed or a requeue is unresolved | Continue checking within the user's monitoring window; never infer success from absence. |
| Accounting confirms terminal state | Record exact state/ExitCode and reconcile project logs; verify outputs before reporting scientific results. |
| All fixed targets are confirmed terminal | Send one completion summary, then disable/pause this monitor using the scheduler's supported update operation. |

Array parent completion alone is insufficient when task results remain unknown. Preserve partial success/failure and outstanding task IDs. An empty first snapshot is not evidence that any job ran.

## Notify and recover

- Notify on meaningful state/reason changes, newly confirmed failures/completion, authorized newly discovered jobs, or required user action. Elapsed time and time remaining may appear in a status view but must not trigger notifications by themselves.
- Stay quiet on unchanged or non-actionable state. Deduplicate recurring errors. An expired certificate requires user renewal; notify once and pause or back off as supported rather than retrying rapidly or attempting unattended browser/MFA authentication. Resume only after renewed access is established within the user's authorization.
- Use the current client's normal notification channel. Telegram/email/other external messages require explicit authorization for that channel and destination and a supported configured tool. Do not read bot token files or select the first allowlisted recipient automatically.
- A monitoring request does not authorize cancelling, requeuing, resubmitting, increasing resources, or changing the experiment plan. Keep confirmed status changes in the existing logs through update-log; do not append an identical note on every poll.

Report the actual installed schedule and target scope after scheduler confirmation. Do not claim monitoring is active when only its instructions or a foreground query were prepared.
