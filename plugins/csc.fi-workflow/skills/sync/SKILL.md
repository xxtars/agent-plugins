---
name: sync
description: Sync this project's code to its configured SLURM cluster through Git. Use for sync code, push to cluster, or deployment of an authorized code change; preserve local and remote work and verify the remote revision.
---

# Sync Code to the Cluster

Read [Roihu requirements](../../rules/roihu.md), project `AGENTS.md`, and `CLAUDE.md` for the host, remote path, repository, and any distinct GitHub SSH aliases. Resolve missing operational settings before executing.

## Preflight before publishing

1. Inspect local status, branch, upstream, and relevant diff. Inspect the remote checkout's status, branch, and remotes through bounded SSH. Confirm both checkouts belong to the intended repository. Retain errors; do not create a guessed path or replace an unrelated checkout.
2. Preserve unrelated local edits, untracked files, and remote work. Stage explicit relevant paths only. A request to sync the current changes authorizes the necessary commit/push; a request only to inspect does not. Never include ignored data, secrets, caches, or generated outputs by force.
3. Check whether queued or running project jobs may read the remote checkout. Prefer a separate immutable run checkout or snapshot. If a shared checkout is in use, sync to a separate authorized location or defer updating it; do not change code underneath jobs.

## Transfer and verify

4. Create a scoped commit when needed, then push to the intended existing remote and branch. Do not force-push. A rejection or ambiguous push outcome requires inspection before retrying.
5. On a clean matching remote branch, fetch and use a fast-forward-only update (`git pull --ff-only`, or an equivalent verified fetch/fast-forward). A divergent or dirty checkout needs reconciliation; never automatically reset, discard, stash, or overwrite its work. Do not change a Git remote URL merely to work around an SSH failure.
6. Compare the full remote `HEAD` with the exact intended local commit. Report the actual revision and remote path. For a per-run snapshot, verify its revision separately and retain its path for submission.

Sync is complete only after the remote revision is verified. Do not submit jobs as a side effect unless the user's current instruction also authorizes submission. If networking is unavailable, local preparation can finish with sync explicitly pending.
