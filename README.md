# Cranslate models

**Cranslate** is an offline speech translator for phones and watches by
[@samoylenkodmitry](https://github.com/samoylenkodmitry). It hears what someone
says, writes it down and translates it on the device; nothing leaves the
phone.

This repository is not the app. It hosts the models and fonts the app
downloads: the speech and translation models every user needs, as assets of
the [`models-v1`](../../releases/tag/models-v1) release, and the speech
models and fonts for Thai and Chinese, as assets of the
[`models-v2`](../../releases/tag/models-v2) release. The app downloads a file
only when the languages a person picks need it.

## Files (release `models-v1`)

| File | Task | Size | SHA-256 |
| --- | --- | --- | --- |
| `canary-1b-v2-Q8_0.gguf` | Speech recognition | 1,144,290,016 bytes | `224f83d1bc487b3303b495a7d6874912fdece93de19d1a04b550829c30a5d289` |
| `Index-Translate-2B-Q4_K_M-scripts.gguf` | Translation | 1,312,164,416 bytes | `0cdb03ebe5cfdf6ac2fdae94de97d13df519d0f95b7304f070ea7d39c7e4e5fb` |

`SHA256SUMS` in the release lists the same checksums.

## Files (release `models-v2`)

| File | Task | Size | SHA-256 |
| --- | --- | --- | --- |
| `typhoon-asr-realtime-Q8_0.gguf` | Speech recognition, Thai | 136,802,048 bytes | `bceea6516e7cf6b133bf5d9a62c091ed31d8885ae1d5244586186470f5c93878` |
| `qwen3-asr-0.6b-Q8_0.gguf` | Speech recognition, Chinese | 850,423,456 bytes | `f081b2d5e23bd669d92cc331d722a8a0681943b8e6f34b48996fd5c319b5acd8` |
| `NotoSansThai-VF.ttf` | Font, Thai | 218,652 bytes | `5a1c559bb539583c8a1fd99d1c5b9491e5e14478c9cd2bd0970d5c3096cc9ef8` |
| `NotoSansCJKsc-VF.ttf` | Font, Chinese | 35,624,588 bytes | `4869e537a467e6453558619719e0f8661e7ec0b2dec3d1b576fe0ab102aa28ce` |

`SHA256SUMS` in the release lists the same checksums.

## Sources and licenses

**Canary 1B v2** by NVIDIA, licensed under
[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
([licenses/CC-BY-4.0.txt](licenses/CC-BY-4.0.txt)).
Model: [nvidia/canary-1b-v2](https://huggingface.co/nvidia/canary-1b-v2).
The GGUF file is the Q8_0 conversion by
[handy-computer/canary-1b-v2-gguf](https://huggingface.co/handy-computer/canary-1b-v2-gguf),
hosted here unchanged.

**Index-Translate 2B** by IndexTeam, licensed under the
[Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0)
([licenses/Apache-2.0.txt](licenses/Apache-2.0.txt)).
Model: [IndexTeam/Index-Translate-2B](https://huggingface.co/IndexTeam/Index-Translate-2B),
GGUF file `Index-Translate-2B.Q4_K_M.gguf` from
[IndexTeam/Index-Translate-2B-GGUF](https://huggingface.co/IndexTeam/Index-Translate-2B-GGUF)
(SHA-256 `044b313d29342bd3b2c77cbb64023ca0d209bd9b3247763f9d162767ef2d746a`).

### Changes to Index-Translate 2B

The tokenizer vocabulary is reordered: first the tokens that Latin-script
text can use, then the Cyrillic ones, then the rest. Each token keeps its
text and gets a new id, and the rows of every matrix with one row per token
move with their tokens; no weight value changes. Two metadata keys,
`cranslate.vocab.latin` and `cranslate.vocab.cyrillic`, record where the
Latin and the Cyrillic parts end. With this order the app computes the output
only for the part of the vocabulary the target language uses, which wrote
each token 14% faster on a Cortex-A76 phone core. The app's own preparation
code (`order_vocabulary_by_script`) wrote the file from the GGUF file above.

**Typhoon ASR Real-time** by SCB 10X (Typhoon), licensed under
[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
([licenses/CC-BY-4.0.txt](licenses/CC-BY-4.0.txt)).
Model: [typhoon-ai/typhoon-asr-realtime](https://huggingface.co/typhoon-ai/typhoon-asr-realtime),
file `typhoon-asr-realtime.nemo`.

### Changes to Typhoon ASR Real-time

The NeMo checkpoint is converted to GGUF with the Parakeet converter of
[transcribe.cpp](https://github.com/handy-computer/transcribe.cpp) 0.4.0
(`scripts/convert-parakeet.py`), which writes the same tensors in the GGUF
format, and then quantized to Q8_0 with its `transcribe-quantize`. The
converter read the checkpoint's tensors only (`weights_only=True`) and treated
the model's empty list of durations as a plain RNN-T head. No weight is
trained or changed beyond the 8-bit quantization.

**Qwen3-ASR 0.6B** by the Qwen team, Alibaba Cloud, licensed under the
[Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0)
([licenses/Apache-2.0.txt](licenses/Apache-2.0.txt)).
Model: [Qwen/Qwen3-ASR-0.6B](https://huggingface.co/Qwen/Qwen3-ASR-0.6B).
The GGUF file is the Q8_0 conversion by
[handy-computer/Qwen3-ASR-0.6B-gguf](https://huggingface.co/handy-computer/Qwen3-ASR-0.6B-gguf),
hosted here unchanged.

**Noto Sans Thai** by the Noto Project Authors, licensed under the
[SIL Open Font License 1.1](https://openfontlicense.org)
([licenses/OFL-1.1-NotoSansThai.txt](licenses/OFL-1.1-NotoSansThai.txt)).
File `NotoSansThai[wdth,wght].ttf` from
[google/fonts](https://github.com/google/fonts/tree/main/ofl/notosansthai),
renamed and otherwise unchanged.

**Noto Sans CJK SC** by Google and Adobe, licensed under the
[SIL Open Font License 1.1](https://openfontlicense.org)
([licenses/OFL-1.1-NotoSansCJK.txt](licenses/OFL-1.1-NotoSansCJK.txt)).
File `Sans/Variable/TTF/NotoSansCJKsc-VF.ttf` from
[notofonts/noto-cjk](https://github.com/notofonts/noto-cjk) at commit
`f8d157532fbfaeda587e826d4cd5b21a49186f7c`, saved again by fontTools as the
font pack of [cranpose-fonts](https://crates.io/crates/cranpose-fonts) is; its
characters and weight axis do not change.
