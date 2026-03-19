# Benchmark Report

## Overview

This report summarizes the benchmark results obtained for `nvidia/Cosmos-Reason2-2B` using `lmms-eval` with the `qwen3_vl` backend path.

The evaluation was conducted on a single NVIDIA RTX 4090 24 GB GPU. The goal was to verify that `Cosmos-Reason2-2B` can be loaded through the `Qwen3-VL` interface and to measure its performance on three commonly referenced vision-language benchmarks:

- `TextVQA`
- `DocVQA`
- `MMBench`

## Evaluation setup

| Item | Value |
| --- | --- |
| Model | `nvidia/Cosmos-Reason2-2B` |
| Evaluator | `lmms_eval` |
| Backend path | `qwen3_vl` |
| GPU | `NVIDIA GeForce RTX 4090 24 GB` |
| Python | `3.10.18` |
| `lmms-eval` commit | `88b23e2` |

Additional command details, environment notes, and local patch information are documented in [reproduction.md](/root/my_project/cosmos_reason2_eval_github/docs/reproduction.md).

## Final results

| Benchmark | Task | Metric | Exact score | Table value |
| --- | --- | --- | ---: | ---: |
| TextVQA | `textvqa_val` | `exact_match` | `0.7598200000000049` | `75.98` |
| DocVQA | `docvqa_val` | `ANLS` | `0.9117385941613753` | `91.17` |
| MMBench | `mmbench_en_dev` | `gpt_eval_score` | `74.65635738831615` | `74.7` |

## Per-benchmark summary

### TextVQA

`Cosmos-Reason2-2B` achieved `75.98` on `textvqa_val`. In this evaluation context, the result is strong and competitive with other capable small-to-mid-size VLMs.

### DocVQA

`Cosmos-Reason2-2B` achieved `91.17` ANLS on `docvqa_val`. This is the strongest result among the three evaluated benchmarks and indicates particularly strong performance on document-style visual question answering.

For full `DocVQA`, the stable configuration on this hardware was `batch_size=1`.

### MMBench

`Cosmos-Reason2-2B` achieved `74.7` on `mmbench_en_dev`. This places the model in a competitive range on general multimodal reasoning and perception, though the result is not as unusually strong as the `DocVQA` score.

## Main observations

- The model loads successfully through the `Qwen3-VL` interface in both direct `transformers` probing and `lmms-eval`.
- The strongest relative result in this run is on `DocVQA`, suggesting that the model is especially effective on document-centric visual QA.
- `TextVQA` and `MMBench` are also strong, but the overall pattern is better described as "very competitive across tasks, with a particular strength on document understanding" rather than "uniformly dominant on every benchmark."

## Scope and limitations

- These results reflect a specific evaluation setup based on `lmms-eval`, the `qwen3_vl` backend path, and a single RTX 4090 24 GB GPU.
- Some local task configuration patches were applied for public dataset access during evaluation. Those patches are documented in this repository.
- This repository does not include full raw logs or raw `samples.jsonl`, so it should be treated as a reproducible summary pack rather than a full artifact dump.

## Machine-readable summary

The corresponding machine-readable score summary is available in [scores.json](/root/my_project/cosmos_reason2_eval_github/docs/scores.json).
