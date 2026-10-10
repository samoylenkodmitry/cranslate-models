# Cranslate models

**Cranslate** is an offline speech translator for phones and watches by
[@samoylenkodmitry](https://github.com/samoylenkodmitry). It hears what someone
says, writes it down and translates it on the device; nothing leaves the
phone.

This repository is not the app. It hosts the two models the app downloads on
its first start, as assets of the [`models-v1`](../../releases/tag/models-v1)
release.

## Files (release `models-v1`)

| File | Task | Size | SHA-256 |
| --- | --- | --- | --- |
| `canary-1b-v2-Q8_0.gguf` | Speech recognition | 1,144,290,016 bytes | `224f83d1bc487b3303b495a7d6874912fdece93de19d1a04b550829c30a5d289` |
| `Index-Translate-2B-Q4_K_M-scripts.gguf` | Translation | 1,312,164,416 bytes | `0cdb03ebe5cfdf6ac2fdae94de97d13df519d0f95b7304f070ea7d39c7e4e5fb` |

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
