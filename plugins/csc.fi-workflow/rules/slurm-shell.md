# SLURM Shell Script Conventions

Read [Roihu requirements](roihu.md) when targeting CSC. These conventions apply to new or revised scripts; preserve project-specific behavior unless a change is needed.

## Exit status and cleanup

- Choose shell options deliberately. Strict mode is allowed, but guard expected failures such as a readiness probe or cleanup of an already exited process. Without strict mode, explicitly check required setup and workload commands.
- Propagate workload failure to the batch script's final exit status. A successful final `echo` or cleanup must not turn failed training into a SLURM `COMPLETED` job.
- Use `exec srun ...` when the workload is the final command and no cleanup is needed. Otherwise capture status explicitly, including when strict mode is enabled:
  ```bash
  if srun python train.py; then
      workload_status=0
  else
      workload_status=$?
  fi
  # Perform project-specific cleanup; preserve workload_status on failure.
  exit "$workload_status"
  ```
- Track and `wait` for background process IDs. Readiness checks need a deadline, a process-liveness check, and a useful failure message.
- Handle cancellation and termination without masking failure. Cleanup may stop only processes belonging to this run. Save required outputs before temporary storage is removed.
- Quote paths and variables. Keep credentials out of scripts, command logs, Git, and experiment notes.

## Runtime and storage

- For existing runs, reuse the project's tested runtime. For a new or revised ML container, follow [container](../skills/container/SKILL.md): check current official framework images before selecting a base. A CSC-supported environment remains a candidate when it fits the workload. Apptainer is useful for reproducibility; a compatible module or virtual environment is also valid.
- Match the image, binaries, Python wheels, and extensions to the target CPU architecture. Validate GPU passthrough and framework compatibility in a short compute job.
- Use `apptainer exec --nv` when the chosen GPU image requires NVIDIA passthrough. Bind only required data, output, cache, and temporary paths; do not assume a home directory layout.
- Keep CSC's provided `$TMPDIR` on local disk. Resolve it at runtime; do not replace it with a fixed `/dev/shm` or Lustre path. Put persistent model caches and results in verified project storage.
- Build containers on a compatible architecture inside an authorized compute allocation, following the strict compute-location default in the Roihu reference. Check space before building. Imports, processor tests and data scans also belong in compute job steps; do not put them in login-side submission wrappers. Record the actual execution host and compare it with the running job allocation before starting the workload; `SLURM_JOB_ID` alone is insufficient.

## Code and run identity

- Follow [sync](../skills/sync/SKILL.md) before [submit](../skills/submit/SKILL.md). Record the remote code revision and run configuration actually used.
- Prefer an immutable per-run checkout or snapshot. Do not update a shared checkout while queued or running jobs may still read it.
- Respect `.gitignore`; stage explicit relevant paths. Do not force-add data, outputs, credentials, or caches.

## Optional script structure

Use a batch submission wrapper only when needed. Keep resource directives in `sbatch_*.sh`, with workload logic in a separate script when that improves reuse. A small experiment may use one clear script.
