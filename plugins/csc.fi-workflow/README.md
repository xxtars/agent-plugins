# csc.fi-workflow

CSC Roihu workflow for ML experiments: configure access, sync code, submit jobs, monitor status, and record verified results. The skills use the host client's tools and the file conventions supplied by `research-workflow`.

## Roihu migration

Roihu is the default for new work. Mahti/Puhti settings remain relevant only to migration and historical records. Accounts, datasets, paths, containers, and quotas must be checked on the new system; replacing a hostname is not a complete migration.

GPU nodes use ARM CPUs and GH200 GPUs, while CPU nodes are x86. Match the runtime and login endpoint to the intended workload. Keep CSC's local `$TMPDIR` and renew SSH certificates through the supported workflow. Current details and official links are in [Roihu requirements](rules/roihu.md), checked 2026-10-07.

## Skills

| Skill | Purpose |
|-------|---------|
| [configure](skills/configure/SKILL.md) | Configure or diagnose SSH, account, paths, architecture, and runtime |
| [sync](skills/sync/SKILL.md) | Transfer scoped code changes and verify the actual remote revision |
| [submit](skills/submit/SKILL.md) | Submit once within the approved budget and save run/job provenance |
| [check-jobs](skills/check-jobs/SKILL.md) | Query project jobs and distinguish failure, completion, and unknown state |
| [watch](skills/watch/SKILL.md) | Schedule scoped checks and notify on meaningful changes |
| [update-log](skills/update-log/SKILL.md) | Reconcile job history and record results verified from output files |

Enable `csc.fi-workflow` from this marketplace in the supported client. Invoke a skill by its plugin name where available, or ask for the corresponding task in plain language. Recurring monitoring requires a scheduler supported by the client; Codex uses thread heartbeats.

## Project settings

Keep one `## Cluster` block in the project's `CLAUDE.md`; read project `AGENTS.md` as well. Required operational settings are the SSH host, remote project path, SLURM user/account, and a project job-name prefix or explicit job IDs. Record architecture, runtime/data paths, and the timezone when needed. Configure can prepare a local draft while Roihu is unavailable, with unresolved values clearly marked.

Existing `Usage: ssh ...` settings are supported. Do not duplicate the Cluster block in another file or assume an old Mahti account/path is valid on Roihu. Read [shell conventions](rules/slurm-shell.md) before changing job scripts. Skills link these references explicitly; do not rely on a client automatically loading every rule file.

## Normal workflow

1. Configure and validate access, data, and a compatible runtime.
2. Sync code and verify the remote revision; isolate queued/running jobs from later checkout changes.
3. Submit an authorized run with a unique ID, manifest, and fixed resource budget.
4. Check status, or schedule monitoring if requested.
5. Reconcile accounting, verify outputs, and update the experiment logs.

The workflow scopes queries to this project's jobs. An SSH failure is an unavailable observation; a job disappearing from the queue needs accounting confirmation. Scheduler success and verified research results are recorded separately.

Monitoring defaults to one-hour checks of a fixed target set and stops when all targets are confirmed terminal. Elapsed-time changes alone do not produce notifications. External notification channels require an explicit user request and destination.

## Research files

Use `research-workflow` to define `experiments/PLAN.md`, `LOG.md`, `weekly/`, and `PITFALLS.md`. This plugin updates operational records; it does not silently change the research plan. Job entries stay in their submission week, even when they finish later. Historical cluster names and results are preserved.

## License

MIT. See [LICENSE](LICENSE).
