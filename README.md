# Cosmos-Reason2-2B Evaluation Pack

This folder is a sanitized export of the local evaluation work for `nvidia/Cosmos-Reason2-2B`.

It is meant to be safe to upload as a GitHub repository. It includes:

- reproduction notes
- environment and command summaries
- a small model-loading probe script
- local `lmms-eval` patch files
- final benchmark score summaries

It intentionally does **not** include:

- raw benchmark `samples.jsonl`
- full terminal logs
- Hugging Face cache contents
- the full cloned `lmms-eval` repo
- benchmark dataset artifacts

## Final scores

| Benchmark | Metric | Exact | Table value |
| --- | --- | ---: | ---: |
| TextVQA | exact_match | `0.7598200000000049` | `75.98` |
| DocVQA | ANLS | `0.9117385941613753` | `91.17` |
| MMBench (`mmbench_en_dev`) | gpt_eval_score | `74.65635738831615` | `74.7` |

## Repo layout

- `docs/reproduction.md` for environment and commands
- `docs/results.md` for the benchmark summary

## Notes

- The final `DocVQA` score was obtained with `batch_size=1` for stability on a 24 GB GPU.
- The OOM retry patch is included for traceability, but the official `DocVQA` score in this repo comes from the stable `batch_size=1` run rather than from the experimental fallback path.
- This is a public evaluation pack, not a full artifact dump.
