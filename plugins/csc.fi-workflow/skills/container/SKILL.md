---
name: container
description: Select, extend, and validate ML containers for CSC Roihu, starting with current official vLLM or training-framework images. Use for model environments, Apptainer/SIF builds, or inference and training dependency compatibility.
---

# Prepare an ML Container

Read the project's AGENTS.md and CLAUDE.md Cluster configuration, then
[Roihu requirements](../../rules/roihu.md) and the relevant parts of
[ML compatibility checks](../../rules/ml-containers.md). This skill handles
environment preparation; it does not select a new research method or authorize
full training, public serving, or recurring monitoring.

## Start from the workload

Identify the model/checkpoint and revision, its official inference and training
code, required modalities, precision, custom operators/losses, and the requested
execution paths. A vLLM base image can host a separate Transformers/PyTorch
training path; using that base does not require rewriting the workload for
vLLM serving or ms-swift. Install a training framework only when it serves the
requested workload.

Read the actual target architecture, GPU and driver requirements, Apptainer
constraints, storage configuration, and authorized test budget. Keep uncertain
values explicit. A login node without NVIDIA devices cannot validate the compute
node's GPU driver or kernels.

## Prefer an upstream framework image

1. Check the [official vLLM tags](https://hub.docker.com/r/vllm/vllm-openai/tags)
   and [releases](https://github.com/vllm-project/vllm/releases) at task time.
   Prefer the newest stable official image compatible with the workload and
   cluster. Distinguish stable releases from nightly, RC, and mutable `latest`;
   record the lookup date. Do not silently substitute the installed CSC module
   or an old cached image for this check.
2. Check [ms-swift's official images and installation guidance](https://swift.readthedocs.io/zh-cn/latest/GetStarted/SWIFT-installation.html)
   when its training capabilities are relevant or vLLM images are unsuitable.
   Other established framework images, including CSC's maintained images, are
   candidates when their capabilities better match the task. Verify registry
   provenance and platform manifests; an image tag listing CUDA and PyTorch is
   not evidence of ARM support.
3. Compare only credible candidates: exact tag/digest and platform, core package
   versions, model/API support, required additions, and known conflicts. Mark
   documentation evidence separately from runtime verification. If the newest
   release is incompatible, explain the concrete mismatch and check the closest
   suitable published release or another established image. Do not scan an
   exhaustive image catalog once a suitable base is established.
4. For a compatible base, preserve its working CUDA/PyTorch/vLLM stack and add the
   smallest necessary project layer. Check dependency resolution before changing
   core packages. Avoid broad upgrades and optional full-feature bundles that
   replace a tested binary stack. `--no-deps` is appropriate only after required
   dependencies are verified; it must not hide conflicts.
5. If no suitable published framework image remains, prepare a concrete fallback:
   candidates rejected and reasons, proposed CUDA/PyTorch base, packages requiring
   source builds, expected time/storage/resources, and remaining uncertainty.
   **Explain this and ask the user before starting a from-scratch core ML stack
   build**, unless they already explicitly approved that fallback. Registry
   authentication, network, or quota failures are access issues, not proof of
   incompatibility. Converting a compatible OCI image to SIF and adding project
   code/dependencies is normal extension, not a from-scratch build.

## Build and retain provenance

- Resolve a mutable tag to an immutable digest before pulling. Record the image
  index digest if present and the selected architecture's manifest digest.
  Record a local SIF's checksum too; SIF and OCI checksums are different identities.
- Save the recipe, pinned additions/source revisions, and build command in the
  project repository. Capture package versions, resolver changes, build logs,
  and baseline versus final dependency-check results. Keep credentials out.
- Read the project's configured shared SIF directory. Use versioned,
  architecture-qualified filenames there; do not overwrite a working image.
  Keep model weights, datasets, caches, and outputs outside the image. Store
  project-specific accounts and paths only in the project configuration.
- Follow CSC's supported Apptainer build procedure on the target architecture.
  Preserve local `$TMPDIR`, check its capacity/quota, and use it for temporary
  layers and sandboxes. Bound CPU/memory use; heavy compilation belongs in an
  authorized compute allocation. Namespace restrictions alone do not imply
  container building is impossible; check CSC's documented fakeroot support.
- Build to a distinct candidate filename, validate it, and publish the final
  filename atomically. Retain the previous working image and failed-build logs;
  do not promote a failed candidate as ready.

## Validate the paths that matter

First inspect versions, imports, dependency conflicts, CLI entry points, and
model configuration and processor setup without running the model. Then use [submit](../submit/SKILL.md) within
the existing authorization for a bounded GPU test: CUDA visibility and a small
operation, actual model input/forward output, and the intended framework path.
For multimodal models include a real image tensor. For training, check finite
loss, finite nonzero gradients in intended trainable parameters, and one
optimizer update. Verify label/slot mappings when custom decision heads are used.

Validate vLLM execution separately when that execution path is required; an
import does not prove model serving works, and serving success does not prove
training works. Do not replace a custom decision/probability API with ordinary
text generation and call it equivalent. Test data must be authorized; synthetic
fixtures can check mechanics, but say that they do not measure task performance.

Separate build, import, inference, and training status. Record the exact artifact,
model revision, test code/config/seed, real job ID, outputs, and limitations in the
project. If tests fail, diagnose before retrying; do not automatically enlarge
resources or resubmit. Report the working scope and any unresolved execution
path instead of declaring the entire environment validated.
