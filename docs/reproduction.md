# Reproduction Notes

## Purpose

This document records the environment, setup steps, local patches, and evaluation commands used to run `nvidia/Cosmos-Reason2-2B` through `lmms-eval` with the `Qwen3-VL` interface.

The intent is to make the evaluation reproducible without requiring the full original workspace layout.

## Environment

| Item | Value |
| --- | --- |
| OS | Linux |
| GPU | `NVIDIA GeForce RTX 4090 24 GB` |
| Driver | `565.77` |
| CUDA reported by `nvidia-smi` | `12.7` |
| Python | `3.10.18` |
| Python executable in original run | `/root/miniconda3/envs/workenv/bin/python3` |

## Key packages

| Package | Version |
| --- | --- |
| `torch` | `2.8.0` |
| `torchvision` | `0.23.0` |
| `torchaudio` | `2.8.0` |
| `transformers` | `5.3.0` |
| `accelerate` | `1.13.0` |
| `datasets` | `4.8.2` |
| `qwen-vl-utils` | `0.0.14` |
| `decord` | `0.6.0` |
| `lmms_eval` | `0.7.1` |

## Prerequisites

- Access to `nvidia/Cosmos-Reason2-2B` on Hugging Face
- A working Hugging Face login on the evaluation machine
- Sufficient GPU memory for single-batch inference on a 2B Qwen3-VL-compatible model

## Setup

### 1. Clone `lmms-eval`

```bash
export LMMS_EVAL_DIR=/path/to/lmms-eval
git clone --depth 1 https://github.com/EvolvingLMMs-Lab/lmms-eval.git "$LMMS_EVAL_DIR"
git -C "$LMMS_EVAL_DIR" rev-parse HEAD
```

Expected reference commit for this project:

```bash
88b23e2bfa16a1edbc16e9e238ed82130b3a4f56
```

### 2. Install dependencies

```bash
python3 -m pip install --upgrade torch==2.8.0 torchvision==0.23.0 torchaudio==2.8.0
python3 -m pip install -e "$LMMS_EVAL_DIR[video-legacy]"
```

### 3. Verify Hugging Face authentication

```bash
hf auth whoami
```

### 4. Optional environment variables used in the original run

```bash
export HF_HOME=/root/.cache/huggingface
export HF_HUB_ENABLE_HF_TRANSFER=1
export WANDB_MODE=disabled
```

For `DocVQA`, the following allocator setting was also used:

```bash
export PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True
```

## Model load probe

The first validation step was a direct `transformers` load probe using the Qwen3-VL model class.

```bash
python3 scripts/load_vlm_probe.py \
  --model nvidia/Cosmos-Reason2-2B \
  --dtype float16 \
  --device-map auto \
  --attn-implementation sdpa
```

Expected outcome:

- `AutoProcessor.from_pretrained(...)` succeeds
- `Qwen3VLForConditionalGeneration.from_pretrained(...)` succeeds
- the script prints environment details and GPU allocation information

## Local `lmms-eval` patches

### Public dataset token patch

Apply:

```bash
export EVAL_PACK_DIR=/path/to/Cosmos-Reason2-2B-Eval-Pack
git -C "$LMMS_EVAL_DIR" apply "$EVAL_PACK_DIR/patches/lmms_eval_public_dataset_token_fix.patch"
```

This patch changes selected `dataset_kwargs.token` fields from `True` to `False` for public MMBench and DocVQA task definitions, avoiding unnecessary Hugging Face token failures on public evaluation data.

### Experimental OOM retry patch

Included for traceability only:

```bash
$EVAL_PACK_DIR/patches/lmms_eval_qwen3_vl_oom_retry_experiment.patch
```

This patch captures an explored fallback strategy for `DocVQA` OOM handling, but it was not the final stable method used for the official `DocVQA` score in this repository.

## Evaluation commands

All official scores in this repository were produced with `--model qwen3_vl` and:

```bash
--model_args pretrained=nvidia/Cosmos-Reason2-2B,device_map=auto,attn_implementation=sdpa
```

### MMBench full

```bash
HF_HOME=/root/.cache/huggingface HF_HUB_ENABLE_HF_TRANSFER=1 WANDB_MODE=disabled \
python3 -m lmms_eval eval \
  --model qwen3_vl \
  --model_args pretrained=nvidia/Cosmos-Reason2-2B,device_map=auto,attn_implementation=sdpa \
  --tasks mmbench_en_dev \
  --batch_size 1 \
  --output_path ./results/cosmos_reason2_mmbench_full \
  --log_samples \
  --verbosity INFO
```

### TextVQA full

```bash
HF_HOME=/root/.cache/huggingface HF_HUB_ENABLE_HF_TRANSFER=1 WANDB_MODE=disabled \
python3 -m lmms_eval eval \
  --model qwen3_vl \
  --model_args pretrained=nvidia/Cosmos-Reason2-2B,device_map=auto,attn_implementation=sdpa \
  --tasks textvqa_val \
  --batch_size 1 \
  --output_path ./results/cosmos_reason2_textvqa_full \
  --log_samples \
  --verbosity INFO
```

### DocVQA full

```bash
HF_HOME=/root/.cache/huggingface \
HF_HUB_ENABLE_HF_TRANSFER=1 \
WANDB_MODE=disabled \
PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True \
python3 -m lmms_eval eval \
  --model qwen3_vl \
  --model_args pretrained=nvidia/Cosmos-Reason2-2B,device_map=auto,attn_implementation=sdpa \
  --tasks docvqa_val \
  --batch_size 1 \
  --output_path ./results/cosmos_reason2_docvqa_full_bs1 \
  --log_samples \
  --verbosity INFO
```

## Benchmark-specific notes

### MMBench

- Full task used: `mmbench_en_dev`
- Final metric: `gpt_eval_score`
- Stable at `batch_size=1` on the test machine

### TextVQA

- Full task used: `textvqa_val`
- Final metric: `exact_match`
- Stable at `batch_size=1`

### DocVQA

- Full task used: `docvqa_val`
- Final metric: `ANLS`
- Full evaluation was memory-sensitive on a single 24 GB GPU
- `batch_size=1` was the final stable configuration

## Troubleshooting notes

- If `Cosmos-Reason2-2B` fails to load, check Hugging Face gated-model access first.
- If public dataset loading fails with a token-related error, verify that the public dataset patch has been applied.
- If `DocVQA` hits CUDA OOM at larger batch sizes, reduce to `batch_size=1`.

## Summary

- `Cosmos-Reason2-2B` successfully loads through the `Qwen3-VL` interface.
- `lmms-eval` works as the main evaluation harness for this model in this setup.
- `DocVQA` required the most conservative batching configuration, but completed successfully and produced the strongest score in this evaluation pack.
