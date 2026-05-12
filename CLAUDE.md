# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A Jupyter notebook assignment (AI201) that implements and compares two binary classifiers from scratch:
- **AdaBoost + Pocket Algorithm** (custom numpy implementation)
- **SVM** (scikit-learn `SVC`)

Evaluated on the **Banana** and **Splice** datasets.

## Running the Notebook

```bash
# Execute all cells and overwrite outputs in-place
jupyter nbconvert --to notebook --execute --inplace PA4.ipynb

# Run and export to HTML
jupyter nbconvert --to html --execute PA4.ipynb
```

Data files must be present at `data/banana_data.csv` and `data/splice_data.csv`. The notebook loads them from the working directory — run from the repo root.

## Architecture

All implementation lives in a single file: **`PA4.ipynb`**. Cell execution order matters; cells must run top-to-bottom because later cells depend on variables defined in earlier ones.

Key cell groups and their dependencies:

| Cell ID | Purpose | Exports |
|---|---|---|
| `code-imports` | Imports + `RANDOM_STATE = 11` | `np`, `SVC`, `RANDOM_STATE` |
| `code-classify` | Pocket Algorithm | `classify()`, `_add_bias()`, `_sign()`, `accuracy()` |
| `code-predict` | Inference + SSE | `predict()` |
| `code-adabtrain` | AdaBoost training loop | `adabtrain()` |
| `code-adabpredict` | Ensemble inference | `adabpredict()` |
| `code-load-data` | Load CSVs, split, standardize | `X_*_train/test`, `y_*_train/test` |
| `code-banana-adab` / `code-splice-adab` | Full AdaBoost sweep K=10..1000 | `*_adaboost` dicts |
| `code-svm-search` | Grid search over SVM configs | `*_svm_rows`, `*_svm_best` |
| `code-svm-banana` / `code-svm-splice` | Retrain best SVM | `*_svm_train_acc`, etc. |
| `code-timing` | Aggregate timing summary | `timing_summary` |

## Key Implementation Details

- **`classify(X, y, maxitercnt, seed)`** — Pocket Algorithm. Augments X with bias column internally. Returns weight vector `w` of length `d+1`.
- **`predict(X, y, w)`** — applies `_sign(X_biased @ w)`. Returns `(predictions, SSE)` when `y` is provided, else just predictions.
- **`adabtrain(X, y, K, weak_maxiter, seed, checkpoints)`** — returns `(ensemble, history)` where `ensemble` is a list of `(alpha_t, w_t)` tuples and `history[K]` snapshots the ensemble at checkpoint K values.
- **`adabpredict(X, ensemble)`** — weighted sign vote: `sign(Σ α_t · sign(X_b @ w_t))`.
- **`evaluate_adaboost_dataset()`** — trains with K=1000 checkpointing at every 10, then accumulates scores incrementally for efficiency.
- Data is **standardized** (mean/std from train set applied to both splits). Labels are `{-1, +1}`.

## Data

| File | Rows | Format |
|---|---|---|
| `data/banana_data.csv` | 5300 | col0=label, col1-2=features |
| `data/splice_data.csv` | 2991 | col0=label, col1-N=features |
| `data/*.libsvm` | same data | LIBSVM format (not used by notebook) |

Splits: Banana 400 train / 4900 test; Splice 1000 train / up to 2175 test (capped by available rows).
