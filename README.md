# Savitar Player: binary packs

This repository is the player's public download store: every binary Savitar Player
fetches at runtime is a release asset here, so a download needs no account and the
player never holds a token. The git tree is documentation only - the bytes are all
in the release.

The **name is historical**: the store began as the RIFE compute packs. It also
carries the speech and translation models, from the same release, because one URL
root means one place to bump when a pack changes and one place to verify.

## Release `pack-2026-11-25`

| asset | size | consumed by |
|---|---|---|
| `compute-pack-rocm10.2.zip` | 1,693,727,350 B (1.58 GiB) | AMD compute pack for frame generation (PyTorch + vsrife) - `ComputePackService` |
| `compute-pack-cuda13.zip` | 1,912,339,694 B (1.78 GiB) | NVIDIA compute pack, **not measured** - same code path |
| `compute-pack-rocm10.2.manifest.json`<br>`compute-pack-cuda13.manifest.json` | 567 B / 715 B | byte size, entry count and SHA-256 for the two archives |
| `subtitles-pack.zip` | 795,316,855 B (0.74 GiB) | automatic subtitles and translation - `SubtitlesPackService`; carries `ggml-base.en.bin` and the 202-language NLLB-200 int8 translator |

That is the whole store: five assets, 4.40 GiB. Verify before use - the SHA-256 of
each archive is in the release body, and the compute packs ship one beside them:

```powershell
Get-FileHash compute-pack-*.zip -Algorithm SHA256
```

GitHub caps a release asset at 2 GiB, which is why the per-vendor archives are one
file each and the packaging step refuses to build something larger rather than
splitting it.

## What is inside the compute packs

- `compute-pack/` - extract as-is. `torch`, `numpy`, `sympy`, `networkx`,
  `vsrife` with `models/flownet_v4.25.pkl`, the vendor runtime (the ROCm SDK
  wheels, or the CUDA/cuDNN runtime DLLs that come inside PyTorch's own wheel),
  plus the stdlib extension modules and `libcrypto`/`libssl` that a trimmed
  embedded CPython 3.12 lacks. No `vapoursynth`: the host application supplies
  its own, version-matched copy.
- `THIRD-PARTY-NOTICES.txt` - component table, versions, licences, and whether
  that pack has been measured.
- `LICENSES/` - the licence text of every bundled component.

## What is inside `subtitles-pack.zip`

- `ggml-base.en.bin` - the Whisper `base.en` recogniser (147,964,211 B), the one
  speech model the player ships. The four-tier ladder of 0.51-0.55 was retired in
  0.54/0.55.2 and its other three weights are no longer published.
- The NLLB-200 int8 translator: `model.bin`, `config.json`,
  `sentencepiece.bpe.model`, `shared_vocabulary.txt`, `special_tokens_map.json`,
  `tokenizer.json`, `tokenizer_config.json`.

## Requirements

Windows x86-64, CPython 3.12 for the compute packs. AMD: ROCm 10.2 support for the
GPU (tested on GFX1100). NVIDIA: a 580-series or newer driver and Turing (SM 7.5)
or newer silicon, which is what CUDA 13 requires.

## Status

- **AMD**: measured. 11.8 ms against 20.4 ms per output frame at 4K30 with a 0.65x
  render scale, -42 %, quality not worse.
- **NVIDIA**: **not measured.** The archive was built on a machine with no NVIDIA
  GPU: it imports cleanly and the 4.25 weights load into the packaged network,
  but no frame has ever been rendered through it. No speed claim is valid until
  someone reports a number.
- **Speech and translation**: measured on one machine (Ryzen 7 7700X, Radeon RX
  7900 XTX) - `base.en` at 10.5 % word error on LibriSpeech test-other, 0.7 s for
  a 135 s clip on the GPU. The numbers live in the player's own benchmarks.

## Licensing

This repository carries third-party weights and binaries only - no application
code of any kind. The NVIDIA runtime DLLs are unmodified copies from PyTorch's
official wheel, redistributed under the NVIDIA CUDA Toolkit EULA (Attachment A
lists the runtime DLLs as distributable) and the cuDNN Software License
Agreement; the AMD half carries the licence files shipped by AMD's own wheels.
`base.en` is MIT (OpenAI Whisper); the NLLB-200 weights are CC-BY-NC-4.0, which
is why the player offers them as a download rather than shipping them. Full texts
are in `LICENSES/` inside each archive. See [THIRD-PARTY.md](THIRD-PARTY.md).

## Retired assets (2026-11-28)

Five assets were deleted from `pack-2026-11-25`, 1,876,921,606 B in total, because
no 0.60.x build downloads them: `ggml-tiny.en.bin`, `ggml-small.en.bin`,
`ggml-large-v3-turbo-q5_0.bin` (the retired tier ladder) and `ggml-base.en.bin`
(the archive carries it), plus `translate-pack-nllb200-int8.zip` (folded into
`subtitles-pack.zip` in 0.53). An alpha from 0.51-0.55 would still ask for those
URLs; 0.53 and later use the pack above.
