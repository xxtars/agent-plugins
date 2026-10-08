# csc.fi-workflow

Manage a project's CSC cluster workflow, from connection setup through verified
experiment records. The skills use SSH, Git, SLURM, and the tools supplied by the
host agent.

## Skills

| Skill | Use it to |
| --- | --- |
| [configure](skills/configure/SKILL.md) | Check access, accounts, paths, runtime, and architecture |
| [container](skills/container/SKILL.md) | Select current framework images, extend compatible ML environments, and validate inference/training |
| [sync](skills/sync/SKILL.md) | Transfer scoped code changes and verify the remote revision |
| [submit](skills/submit/SKILL.md) | Submit an authorized run and record its resources and identity |
| [check-jobs](skills/check-jobs/SKILL.md) | Inspect current and recent project jobs |
| [watch](skills/watch/SKILL.md) | Schedule scoped monitoring and notify on meaningful changes |
| [update-log](skills/update-log/SKILL.md) | Reconcile accounting and record results verified from output files |

## Before running jobs

Configure a working SSH connection, a valid project account, the remote project
path, runtime and data paths, and a project-specific job prefix or explicit job
IDs. Keep these values in the project using the plugin, not in this repository.

The current skills read a single Cluster block in the project's CLAUDE.md as
well as AGENTS.md. That filename is retained for existing project configurations;
the workflow can be run by any compatible agent. Keep one configuration source
instead of duplicating it between instruction files.

Roihu is the default for new setups. Review [cluster requirements](rules/roihu.md)
and [shell conventions](rules/slurm-shell.md), then verify current operational
limits with CSC before submitting. Historical Mahti or Puhti configuration is
not evidence that the same account, path, or runtime is valid on Roihu.

## Typical workflow

1. Validate the connection, account, data, and runtime.
2. For a new ML environment, check the latest stable official vLLM image first and relevant training-framework images such as ms-swift; verify compatibility, then extend and test the chosen image. Explain and obtain approval before a from-scratch core stack build.
3. Synchronize the intended code and verify its remote revision.
4. Submit within the authorized resource budget and record the job identity.
5. Check status, or request recurring monitoring when needed.
6. Reconcile scheduler accounting, inspect outputs, and update the records.

Queries stay scoped to the project's jobs. A failed SSH call is an unavailable
observation. A job leaving the queue requires an accounting check. Scheduler
success and verified scientific results are recorded separately.

## Monitoring and records

Recurring monitoring requires a scheduler provided by the host client. It checks
a fixed target set by default, reports meaningful changes, and ends when the
scoped jobs are confirmed terminal. External notification channels require a
specified destination and explicit authorization.

Use [research-workflow](../research-workflow/) for PLAN.md, LOG.md, weekly records,
and PITFALLS.md. The cluster plugin updates execution records without silently
changing the research plan.

See the [repository README](../../README.md) for installation and
[LICENSE](LICENSE) for the MIT terms.
