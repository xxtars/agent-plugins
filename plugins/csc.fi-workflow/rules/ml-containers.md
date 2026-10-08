# ML container compatibility evidence

Consult current primary sources when making a selection; this file intentionally
does not freeze a "latest" release or a particular project's environment.

## Evidence to check before extension

| Layer | Evidence that changes the decision |
| --- | --- |
| Workload | Model revision/config, upstream requirements and loader, modalities, precision, custom heads/losses, training method |
| Platform | Registry manifest has the target Linux CPU architecture; GPU compute capability is supported by the actual wheels/kernels |
| CUDA | Runtime version in the image and minimum compatible host driver, including any documented compatibility-library requirements; `nvidia-smi`'s CUDA banner is not the installed toolkit version |
| Binary stack | Python ABI, PyTorch/CUDA build, torchvision, Triton, FlashAttention/other required extensions; installing an ARM Python package does not establish that its compiled kernels work |
| Model support | Exact model architecture and processor supported by the selected Transformers/vLLM/framework version, plus the required task/API, not merely the model family name |
| Training | PEFT, optimizer/distributed libraries as required, trainable modules, custom loss integration; do not assume ms-swift's generic SFT reproduces a project's slot/probability loss |
| Apptainer | Non-root operation, `--nv`, required binds and writable caches, supported build mode, unpacked disk needs; Docker defaults may assume writable root/home or an API-server entry point |

Use the registry's multi-platform manifest (for example Docker buildx or skopeo
inspection, or the registry API). Filter out attestation descriptors with unknown
platforms. Inspect the selected image configuration for CUDA/build metadata, then
inspect installed package metadata in the container. Official release notes or
Dockerfiles can narrow candidates but do not replace testing the delivered image.

For added dependencies, inspect a resolver dry run with appropriate constraints
when supported. Compare it to the base inventory, and check dependency consistency
after installation. Record inherited conflicts separately from new ones and assess
their effect on the requested paths. Never label an unresolved import or binary
conflict as harmless solely because a package reports a version.

## Practical decisions

- Newest stable vLLM is ARM-compatible and missing only project Python code:
  extend it and retain the upstream stack.
- A training-specific image supplies the required working stack more directly:
  use it after checking its architecture and model/loss support; explain why it
  fits better than the preferred vLLM image.
- Newest image needs an unavailable driver or breaks the model API: check a
  suitable published version and state the constraint. Do not silently upgrade
  the host driver or choose a nightly because its timestamp is newer.
- Inference and training require incompatible binary stacks: describe the
  conflict and use separate derived images if that fits the authorized task.
  Do not force both into one environment by repeatedly replacing core packages.
- No published framework base fits: return the concrete fallback decision from
  the container skill and obtain the required approval before building the core
  stack. A missing registry login or failed download alone does not meet this test.

## Primary sources

- [vLLM official image tags](https://hub.docker.com/r/vllm/vllm-openai/tags),
  [Docker usage](https://docs.vllm.ai/en/latest/deployment/docker/), and
  [supported models](https://docs.vllm.ai/en/latest/models/supported_models/).
- [ms-swift installation and published images](https://swift.readthedocs.io/zh-cn/latest/GetStarted/SWIFT-installation.html).
- [CSC container builds, storage, and fakeroot](https://docs.csc.fi/computing/containers/overview/).
- [NVIDIA CUDA compatibility](https://docs.nvidia.com/deploy/cuda-compatibility/).

Record the sources, lookup date, selected tag/digest, and actual test evidence in
the consuming project's environment record. Keep project paths and identities out
of this public plugin.
