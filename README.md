# Hallucination Benchmarks: Construct Comparison, Model Evaluation and Label Audit

Code and results for my thesis *Do Hallucination Benchmarks Measure the Same Thing? A Construct Comparison, Model Evaluation and Label Audit of TruthfulQA, HaluEval and FActScore (IU International University of Applied Sciences*, 2026).

The thesis examines whether three widely used hallucination benchmarks, **TruthfulQA**, **HaluEval** and **FActScore**, measure the same construct, whether model rankings agree across them, and how reliable the TruthfulQA reference labels are.

## What this repository contains

| Part | Description | Location |
|---|---|---|
| Model evaluation | Colab notebook that runs Gemma-3-12B, Phi-4 and gpt-oss-20B on subsamples of TruthfulQA, HaluEval and OpenFActScore | `[notebook file]` |
| Results | Per-benchmark exports: a metadata JSON, a summary CSV and per-item CSVs for each run | `[results folder]` |
| Label audit | The 60-item TruthfulQA MC1 audit sheet with annotator selections, typology codes and rationales | `[audit file]` |
| HaluEval subsample | The exact items used, so the runs are reproducible | `[subsample file]` |

### Evaluation samples

- **TruthfulQA:** 300 of 817 questions, stratified by category (multiple-choice MC1 and MC2, log-probability scoring).
- **HaluEval:** 300 instances each for QA, Dialogue and Summarization (150 source items, each shown with a correct and a hallucinated response) and 100 General items; scored by generate-and-parse and by next-token log-probability.
- **OpenFActScore:** 25 entities (five per popularity tier), long-form biographies scored by a judge model.

## Setup used

- **Hardware:** a single NVIDIA A100 (40 GB) on Google Colab Pro.
- **Subject models:** `google/gemma-3-12b-it`, `microsoft/phi-4`, `openai/gpt-oss-20b`.
- **Judge model (OpenFActScore):** `meta-llama/Llama-3.1-8B-Instruct`.
- **Precision:** Gemma-3-12B and Phi-4 run in 4-bit NF4 quantisation. gpt-oss-20B runs in bfloat16 with CPU offload, because its native MXFP4 format is not supported on this GPU.
- **Decoding and prompting:** seed 42, greedy decoding, chat-template prompting. HaluEval generation uses a limit of 80 new tokens (200 for gpt-oss-20B, with reasoning effort set to "low").

## Reproducing the runs

1. Open the notebook in Google Colab with a GPU runtime.
2. Add your Hugging Face token as a Colab secret named `HF_TOKEN` and enable notebook access. Some model checkpoints require accepting their licences on Hugging Face first.
3. Mount Google Drive when prompted. Results and the model cache are stored there.
4. Run the cells in order: setup, TruthfulQA, HaluEval, then OpenFActScore. Long runs save checkpoints and resume after an interruption.

The HaluEval subsample is cached after the first run. Keep the cached file so that repeated runs use identical items.

## Output files

Each benchmark export contains:

- `metadata_<timestamp>.json`: seed, sample sizes, scoring method, decoding settings, quantisation per model and exact checkpoints.
- `summary_<timestamp>.csv`: aggregate scores (for HaluEval, one row per model, domain and scoring method, with the number of abstentions).
- `per_item_*.csv`: one row per question or instance with the outcome.

## Known limitations

- Small subsamples and only three models, so results are descriptive and do not generalise to other models or to the full benchmarks.
- Next-token log-probability scoring is not valid for gpt-oss-20B, because the model begins a reasoning channel before its final answer. Its log-probability scores are therefore not reported as results.
- Some HaluEval generations contained no parseable Yes/No answer within the token limit (abstentions). Accuracy is reported over valid outputs and with abstentions counted as errors.
- In the label audit, a disagreement with a dataset label is an annotator judgement, not a confirmed labelling error.

## Citation

```
Sabiha Dudhia. (2026). Do Hallucination Benchmarks Measure the Same Thing? A Construct Comparison, Model Evaluation and Label Audit of TruthfulQA, HaluEval and FActScore. Bachelor's Thesis, International University of Applied Sciences.
```

## Licence
The benchmark datasets (TruthfulQA, HaluEval, FActScore) and model weights remain under their original licences.
