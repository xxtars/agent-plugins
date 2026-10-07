---
name: configure
description: Configure or diagnose this project's CSC Roihu or SLURM connection, SSH certificate, account, paths, and runtime architecture. Use for cluster setup, migration, or connection failures. Do NOT use for Overleaf.
---

# Configure SLURM Workflow

Read [Roihu requirements](../../rules/roihu.md), project `AGENTS.md`, and the existing `## Cluster` section in `CLAUDE.md`. Preserve valid settings and reuse session facts. Apply authorized updates without asking for the same permission again.

## Establish the configuration

1. Collect only values needed for the requested action:
   - SSH alias/host, preserving spelling; GPU work normally uses `roihu-gpu.csc.fi`.
   - SLURM user and account, verified for Roihu rather than copied from a retired cluster.
   - Remote project path and, when applicable, separate immutable run directory.
   - Literal project job-name prefix, or explicit job IDs for monitoring.
   - Target architecture and the runtime, data, and resource settings needed for this workload.
2. Reuse existing scripts and verified configuration before asking questions. A local draft may mark missing values `unconfirmed`; remote operations must not interpolate them.
3. Validate SSH through a bounded, noninteractive read-only command:
   ```bash
   ssh -o BatchMode=yes -o ConnectTimeout=10 -o ServerAliveInterval=10 -o ServerAliveCountMax=2 <ssh_host> 'whoami && hostname && uname -m'
   ```
   Retain stderr and the exit status. Preserve host-key verification. If host trust needs user action, report that instead of bypassing it.
4. On authentication failure, inspect the selected host's effective `ssh -G` configuration, certificate validity with `ssh-keygen -L -f <certificate_path>`, and public-key fingerprint if needed. Do not dump private keys or unrelated SSH configuration. Check [CSC SSH guidance](https://docs.csc.fi/computing/connecting/ssh-keys/) and [service notices](https://research.csc.fi/service-break/) before recommending renewal. Distinguish expiry, rejection despite a valid certificate, network/DNS, sandbox restrictions, and login-shell errors. Renewal may require the user's browser/MFA interaction.
5. Validate the known remote path's existence and write access, account membership, data availability, and target architecture. Use read-only checks first. A maintenance outage leaves these checks pending; it does not justify inventing a path or declaring migration complete. Do not run ML workloads on login nodes.

## Save one configuration block

Update `CLAUDE.md` in place. Do not duplicate connection settings in `AGENTS.md` or a second config file. The example below is a schema, not an executable command:

```markdown
## Cluster
- SSH host: `<configured_alias>`
- Remote path: `<verified_project_path>`
- SLURM user: `<user>`
- SLURM account: `<verified_account>`
- Job name prefix: `<literal_prefix>`
- Target: `Roihu GPU / aarch64` or `Roihu CPU / x86_64`
- Timezone: `Europe/Helsinki`
- Remote validation: `<date and outcome, or pending reason>`
- All project commands run from the configured remote project root
```

Omit optional unknown entries, or explicitly label them unresolved. Existing `Usage: ssh ...` configuration remains supported; avoid contradictory duplicate values.

For container/model work, add verified dataset, container, persistent cache, output, and run-snapshot paths under `### CSC Paths`. Record image architecture and version/digest. Resolve `$TMPDIR` at runtime, not as a fixed per-project path. If local and cluster GitHub SSH aliases differ, preserve the existing `### GitHub SSH` entries and verify their repository identity.

Only add vLLM deployment settings when relevant to this project. Readiness checks need a deadline and a server-liveness check; a failed probe must not wait forever.

## Finish

Report which settings were saved and which checks remain unresolved. Use `/research-workflow:init` only if research scaffolding is requested and missing. There is no `/csc.fi-workflow:init` skill. Configuration or diagnosis alone does not authorize submitting a job, migrating data, or starting recurring monitoring.
