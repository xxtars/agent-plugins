# CSC Roihu reference

Official documentation checked **2026-10-07**. Recheck time-sensitive settings when configuring or submitting. This is a shared reference, not a runnable configuration.

## Migration and architecture

- Use Roihu for new CSC work. Puhti and Mahti compute services retired on 2026-07-31 and 2026-08-31. Their login/storage access is planned to end on 2026-10-15. Migration is not automatic: verify data, account access, paths, and quotas on Roihu. Do not rewrite historical experiment records. [CSC migration FAQ](https://docs.csc.fi/support/faq/roihu/)
- Roihu CPU nodes are x86; GPU nodes use ARM Grace CPUs and NVIDIA GH200 GPUs. Normally submit GPU jobs through `roihu-gpu.csc.fi`, CPU jobs through `roihu-cpu.csc.fi`. An old x86 container or environment is not a compatible ARM runtime. [System description](https://docs.csc.fi/computing/systems-roihu/)
- Prefer submission from the matching login architecture. Cross-architecture submissions can inherit incompatible paths and modules; follow CSC's environment-export procedure when necessary. [Cross-architecture submission](https://docs.csc.fi/computing/running/submitting-jobs-across-architectures/)

## Authentication

- CSC uses signed SSH certificates with a 24-hour validity period. Keep the private key protected and use an agent for unattended access during that period; do not remove its passphrase. Renew through the documented interactive workflow when needed. Never copy private keys or tokens into project files. [SSH and certificates](https://docs.csc.fi/computing/connecting/ssh-keys/)
- Preserve the existing SSH alias spelling and inspect effective settings. Separate expired credentials, server rejection, network/sandbox failure, and maintenance. Valid dates alone do not prove authentication works. [Service notices](https://research.csc.fi/service-break/)

## Where commands may run

Follow CSC's [usage policy](https://docs.csc.fi/computing/usage-policy/#login-nodes),
checked 2026-10-08: login nodes support editing/compilation, batch management,
data movement and light pre/post-processing. CSC describes light processing as
one core, minutes to finish and peak memory below 1 GiB. Assess the actual command
and aggregate load; neither "read-only" nor "CPU-only" proves it is light.

- A bounded single-file streaming checksum, small metadata/configuration check,
  low-cost import or small preprocessing check may run on a login node if it fits
  those limits. Constrain threads/concurrency, set a reasonable timeout, and
  watch memory and shared-storage I/O. A multi-GB file need not consume multi-GB
  RAM. Do not require a GPU allocation just for hashing, or skip integrity checks.
  Reuse a recorded digest only for an unchanged, reliably identified immutable
  artifact; recalculate after changes or suspected corruption.
- Model weight loading/inference/training, full evaluations, bulk video decoding,
  whole-dataset scans, long/multicore/high-memory or sustained heavy I/O work
  belongs on compute nodes. If a light check grows beyond its estimate, end that
  check and move the work; do not silently leave it stressing a shared login node.
- CSC permits container builds on matching-architecture login or compute nodes.
  Use login nodes only for bounded, modest preparation/builds; large compression,
  compilation and ML builds with uncertain resource demand use compute allocations.
  See [build locations](https://docs.csc.fi/computing/containers/overview/#build-location).
- Short GPU tests use `gputest` within the budget/current limit (15 minutes checked
  2026-10-08); longer and production work uses normal partitions. Standalone CPU
  work uses compatible CPU resources, not unused GPUs. Do not assume an x86 CPU
  node can run an ARM image or split production into repeated test jobs.
- For compute jobs, verify running allocation, assigned nodes and actual host;
  save job ID, partition and hostname. `salloc` or `SLURM_JOB_ID` alone does not
  prove the current process runs there. Permitted login-node checks need their
  command/scope/outcome recorded, not an invented job ID.
- Placement does not grant write, submission, cancellation or budget permissions.
  Apply the current project roles/authorization separately. Heavy work cannot
  fall back to login nodes because a queue is busy.

## Resources and runtime

- Current GPU batch partitions include `gputest`, `gpumedium`, and `gpularge`. Verify current limits with official documentation and live `sinfo`/`scontrol` before selecting resources. A job's time and GPU count must fit the user's budget; do not assume interactive MIG resources are available. [Partitions](https://docs.csc.fi/computing/running/batch-job-partitions/)
- The documented GPU request is `--gres=gpu:gh200:N`, where `N` is GPUs **per node**. CPU-accessible unified memory is not all GPU HBM. Check actual usable GPU memory when sizing a model. [Job scripts](https://docs.csc.fi/computing/running/creating-job-scripts-roihu/)
- CSC provides local `$TMPDIR`; retain it and save outputs to persistent storage before job exit. Container build temporary files need local disk. Keep persistent caches separate and verify quotas. Choose the build location on the target architecture using the load criteria above. [Containers](https://docs.csc.fi/computing/containers/overview/)

For new ML environments, follow [container](../skills/container/SKILL.md) and its
[compatibility checks](ml-containers.md). Check current official framework images
before defaulting to a local module; preserve verified environments used by existing runs.

## Shared workflow contract

- Read project `AGENTS.md`, then the `## Cluster` block in `CLAUDE.md`. That block remains the single source of connection settings. Do not execute unresolved placeholders.
- Quote substituted values for both local and remote shells. Use bounded SSH connections with host-key verification. Retain stderr and exit status; an unsuccessful query is not an empty queue.
- Scope operations to known project job IDs. For discovery, match the configured job-name prefix as a **literal prefix**, then verify account and working directory or saved run metadata. Do not import every job owned by the user.
- Identify jobs by cluster/host and Job ID, retaining array task IDs. Keep Submit time where available to distinguish reused IDs. `sacct -X` omits job steps; inspect step failures separately when needed.
- Record timestamps with an explicit timezone (project setting, otherwise Europe/Helsinki). Local macOS lacks GNU `date -d`; use Python for calendar calculations or a verified remote implementation.
- Never infer successful completion from disappearance in `squeue`. Reconcile with `sacct` state and ExitCode. Missing or delayed accounting remains unknown until confirmed.
