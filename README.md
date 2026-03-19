# Cosmos-Reason2-2B Evaluation Pack

This repository provides a compact, reproducible evaluation record for `nvidia/Cosmos-Reason2-2B` on `TextVQA`, `DocVQA`, and `MMBench` using `lmms-eval` with the `Qwen3-VL` model interface.

The goal of this repo is simple: make it easy to understand how the model was evaluated, what patches were needed, what hardware was used, and what scores were obtained.

## Highlights

- Model: `nvidia/Cosmos-Reason2-2B`
- Eval framework: `lmms-eval`
- Backend path: `qwen3_vl`
- Hardware: 1x NVIDIA RTX 4090 24 GB
- Main finding: `Cosmos-Reason2-2B` loads successfully through the `Qwen3-VL` interface and performs especially strongly on `DocVQA`

## Final results

| Benchmark | Task | Metric | Exact score | Table value |
| --- | --- | --- | ---: | ---: |
| TextVQA | `textvqa_val` | `exact_match` | `0.7598200000000049` | `75.98` |
| DocVQA | `docvqa_val` | `ANLS` | `0.9117385941613753` | `91.17` |
| MMBench | `mmbench_en_dev` | `gpt_eval_score` | `74.65635738831615` | `74.7` |

## Quick takeaways

- `DocVQA` is the standout result for this model in this evaluation setup.
- `TextVQA` is strong and competitive, but not a dramatic outlier relative to other capable VLMs.
- `MMBench` is also strong, placing the model in a competitive top tier rather than far above the field.

## Repository contents

- `docs/reproduction.md`
  Environment details, installation steps, and evaluation commands.
- `docs/results.md`
  A concise summary of the benchmark outcomes.
- `docs/scores.json`
  Machine-readable score summary.
- `scripts/load_vlm_probe.py`
  Minimal `transformers`-based probe script for testing Qwen3-VL-compatible model loading.
- `patches/lmms_eval_public_dataset_token_fix.patch`
  Patch used to avoid unnecessary Hugging Face token enforcement on public MMBench and DocVQA dataset paths.
- `patches/lmms_eval_qwen3_vl_oom_retry_experiment.patch`
  Experimental OOM fallback patch kept for traceability.

## Reproduction

Clone the repo:

```bash
git clone https://github.com/AuricTW/Cosmos-Reason2-2B-Eval-Pack.git
cd Cosmos-Reason2-2B-Eval-Pack
```

Then start with:

- `docs/reproduction.md` for the full environment and command history
- `docs/results.md` for the benchmark summary

## Notes and limitations

- The official `DocVQA` score in this repo was produced with `batch_size=1` for stability on a single 24 GB GPU.
- Higher batch sizes were explored during debugging, but were not the final stable setting for full `DocVQA`.
- The OOM retry patch is included as an experiment log, not as the final method used for the reported `DocVQA` score.
- This repository intentionally excludes raw sample dumps, full logs, caches, and cloned third-party source trees to stay lightweight and easier to share.
