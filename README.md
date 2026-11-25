# RIFE Torch compute packs

Optional PyTorch compute packs for RIFE frame interpolation: a vendor GPU runtime
plus `torch`, `numpy`, `vsrife` and the RIFE 4.25 network, packaged as one
`compute-pack/` folder of Python packages that can be put on `sys.path`.

## Downloads

Two archives in the [latest release](../../releases/latest), one per vendor:

| archive | vendor | runtime | size | status |
|---|---|---|---|---|
| `compute-pack-rocm10.2.zip` | AMD | ROCm 10.2.0 / torch 2.15.0+rocm10.2.0 / vsrife 5.7.0 | 1,693,727,350 B | measured, -42 % frame time |
| `compute-pack-cuda13.zip` | NVIDIA | CUDA 13.0 / torch 2.9.1+cu130 / vsrife 5.6.0 | 1,912,339,694 B | **not measured** |

Every archive ships with a `*.manifest.json` carrying its byte size, entry count
and SHA-256. Verify the download before use:

```powershell
Get-FileHash compute-pack-*.zip -Algorithm SHA256
```

There is exactly one file per vendor: GitHub caps a release asset at 2 GiB, and
the packaging step refuses to build something larger rather than splitting it.

## What is inside

- `compute-pack/` — extract as-is. `torch`, `numpy`, `sympy`, `networkx`,
  `vsrife` with `models/flownet_v4.25.pkl`, the vendor runtime (the ROCm SDK
  wheels, or the CUDA/cuDNN runtime DLLs that come inside PyTorch's own wheel),
  plus the stdlib extension modules and `libcrypto`/`libssl` that a trimmed
  embedded CPython 3.12 lacks. No `vapoursynth`: the host application supplies
  its own, version-matched copy.
- `THIRD-PARTY-NOTICES.txt` — component table, versions, licences, and whether
  that pack has been measured.
- `LICENSES/` — the licence text of every bundled component.

## Requirements

Windows x86-64, CPython 3.12. AMD: ROCm 10.2 support for the GPU (tested on
GFX1100). NVIDIA: a 580-series or newer driver and Turing (SM 7.5) or newer
silicon, which is what CUDA 13 requires.

## Status

- **AMD**: measured. 11.8 ms against 20.4 ms per output frame at 4K30 with a 0.65x
  render scale, -42 %, quality not worse.
- **NVIDIA**: **not measured.** The archive was built on a machine with no NVIDIA
  GPU: it imports cleanly and the 4.25 weights load into the packaged network,
  but no frame has ever been rendered through it. No speed claim is valid until
  someone reports a number.

## Licensing

This repository carries third-party binaries only — no application code of any
kind. The NVIDIA runtime DLLs are unmodified copies from PyTorch's official
wheel, redistributed under the NVIDIA CUDA Toolkit EULA (Attachment A lists the
runtime DLLs as distributable) and the cuDNN Software License Agreement; the AMD
half carries the licence files shipped by AMD's own wheels. Full texts are in
`LICENSES/` inside each archive. See [THIRD-PARTY.md](THIRD-PARTY.md).
