# Third-party components

Nothing in this repository is original code. Each archive is a redistribution of
upstream wheels plus one network checkpoint, unchanged.

## Bundled

| component | version | licence | upstream |
|---|---|---|---|
| PyTorch | 2.15.0+rocm10.2.0 / 2.9.1+cu130 | BSD-3-Clause | pytorch.org |
| AMD ROCm runtime (`_rocm_sdk_core`, `_rocm_sdk_libraries`, `amd_torch_device_*`) | 10.2.0 | MIT and per-component texts (`amd_comgr`, `hipcc`) | stable.repo.amd.com/rocm/whl-next |
| NVIDIA CUDA 13.0 runtime libraries + cuDNN | as shipped in `torch-2.9.1+cu130` | NVIDIA CUDA Toolkit EULA, cuDNN SLA | nvidia.com |
| vsRIFE | 5.7.0 (AMD) / 5.6.0 (NVIDIA) | MIT, Copyright (c) 2021 HolyWu | github.com/HolyWu/vs-rife |
| RIFE 4.25 network `flownet_v4.25.pkl` | 4.25 | MIT (weights, data file) | github.com/hzwer/Practical-RIFE |
| NumPy | 2.5.5 | BSD-3-Clause | numpy.org |
| SymPy | 1.14.0 | BSD-3-Clause | sympy.org |
| NetworkX | 3.7 | BSD-3-Clause | networkx.org |
| requests, urllib3, certifi, idna, charset-normalizer, tqdm, colorama, filelock, fsspec, Jinja2, MarkupSafe, typing_extensions, mpmath | as pinned in each manifest-era wheel set | Apache-2.0 / MIT / BSD-3-Clause / MPL-2.0 per component | PyPI |

Every licence text that a wheel ships is copied into `LICENSES/` inside the
archive, in the layout pip installs it.

## Redistribution basis

- **NVIDIA CUDA and cuDNN.** The runtime DLLs are unmodified files from
  PyTorch's official Windows wheel. NVIDIA's CUDA Toolkit EULA permits
  distributing the files listed in its Attachment A (CUDA runtime, cuBLAS,
  cuBLASLt, cuFFT, cuRAND, cuSPARSE, cuSOLVER, NVRTC) with an application that
  adds material functionality and accesses them only through that application;
  the cuDNN SLA permits distributing its runtime `.dll`/`.so` files. Both texts
  are in `LICENSES/` (`nvidia-cuda-toolkit-eula.txt`,
  `nvidia-cudnn-sla.txt`).
- **AMD ROCm.** The runtime is redistributed under the licences shipped by AMD's
  wheels, reproduced in `LICENSES/`.
- **Everything else** keeps its own licence text, also under `LICENSES/`.

## Removal

If you hold rights to any file here and want it removed, open an issue and it
will be taken down.
