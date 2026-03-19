# Cosmos-Reason2-2B Evaluation Pack

This repository documents a reproducible evaluation workflow for `nvidia/Cosmos-Reason2-2B` using `lmms-eval` and the `Qwen3-VL` model interface.

The focus of this project is practical reproducibility: it records the environment, patches, model-loading path, benchmark commands, and final scores needed to understand and reproduce the evaluation without shipping a large artifact dump.

## Overview

- Model: `nvidia/Cosmos-Reason2-2B`
- Evaluation framework: `lmms-eval`
- Backend path: `qwen3_vl`
- Hardware used: 1x NVIDIA RTX 4090 24 GB
- Main outcome: the model loads successfully through the `Qwen3-VL` interface and performs especially strongly on `DocVQA`

## Final results

| Benchmark | Task | Metric | Exact score | Table value |
| --- | --- | --- | ---: | ---: |
| TextVQA | `textvqa_val` | `exact_match` | `0.7598200000000049` | `75.98` |
| DocVQA | `docvqa_val` | `ANLS` | `0.9117385941613753` | `91.17` |
| MMBench | `mmbench_en_dev` | `gpt_eval_score` | `74.65635738831615` | `74.7` |

## Key observations

- `DocVQA` is the standout result in this evaluation setup.
- `TextVQA` is strong and competitive, but not an extreme outlier relative to other capable VLMs.
- `MMBench` is also strong, with the model landing in a competitive top tier rather than dominating every category.
- Within `MMBench`, the model is particularly strong on scene-level and identity-focused recognition, and weaker on spatial and forward-prediction-heavy subcategories.

## Repository guide

- [docs/results.md](docs/results.md)
  Formal benchmark report summarizing setup, scores, interpretation, and limitations.
- [docs/reproduction.md](docs/reproduction.md)
  Technical appendix with environment details, installation steps, patches, and evaluation commands.
- [docs/mmbench_breakdown.md](docs/mmbench_breakdown.md)
  Category-level `MMBench` analysis and interpretation.
- [docs/scores.json](docs/scores.json)
  Machine-readable score summary for downstream reuse.
- [scripts/load_vlm_probe.py](scripts/load_vlm_probe.py)
  Minimal `transformers`-based probe script for testing Qwen3-VL-compatible model loading.
- [patches/lmms_eval_public_dataset_token_fix.patch](patches/lmms_eval_public_dataset_token_fix.patch)
  Local `lmms-eval` task patch for public MMBench and DocVQA dataset access.
- [patches/lmms_eval_qwen3_vl_oom_retry_experiment.patch](patches/lmms_eval_qwen3_vl_oom_retry_experiment.patch)
  Experimental OOM fallback patch kept for traceability.

## Reproduction quick start

```bash
git clone https://github.com/AuricTW/Cosmos-Reason2-2B-Eval-Pack.git
cd Cosmos-Reason2-2B-Eval-Pack
```

Recommended reading order:

1. Start with [docs/results.md](docs/results.md) for the outcome and interpretation.
2. Use [docs/reproduction.md](docs/reproduction.md) for environment setup and rerun commands.
3. Refer to [docs/mmbench_breakdown.md](docs/mmbench_breakdown.md) for category-level analysis.

## Scope and limitations

- This repository is a reproducible evaluation pack, not a full raw artifact dump.
- Raw `samples.jsonl`, full terminal logs, local caches, and cloned third-party source trees are intentionally omitted to keep the repo lightweight and easier to share.
- The official `DocVQA` score was produced with `batch_size=1` for stability on a single 24 GB GPU.
- Higher batch sizes were explored during debugging, but were not the final stable setting for full `DocVQA`.
- The OOM retry patch is included as an experiment log, not as the final mechanism used for the reported `DocVQA` number.
