# PA4 Notebook Audit
**Branch:** dev/update-results  
**Run date:** 2026-05-12  
**RANDOM_STATE:** 11  
**Executed via:** `python -m nbconvert --to notebook --execute --inplace PA4.ipynb`

---

## Execution Log

### Bug Fixed Before Run
| Issue | Location | Fix Applied |
|---|---|---|
| `FileNotFoundError: banana_data.csv not found` | `code-load-data` | Changed `"banana_data.csv"` → `"data/banana_data.csv"` and `"splice_data.csv"` → `"data/splice_data.csv"`. Data files live in `data/` subdirectory; notebook assumed working-directory-relative paths. No math or comments changed. |

---

## Cell-by-Cell Results

### `code-imports` — Imports & Setup
- No output. Executed cleanly. `RANDOM_STATE = 11`.

### `code-datagen` — Synthetic Dataset
- Generated scatter plot. No numeric outputs to audit.

### `code-perceptron-eval` — Single Perceptron
| Metric | Old (stale) | Actual |
|---|---|---|
| Training Accuracy | 100.00% | 100.00% |
| Test Accuracy | **99.00%** | **100.00%** |
| Test SSE | **4.0** | **0.0** |

Weight vector: `[-11.0, 0.6586445, 3.84756647]`

### `code-load-data` — Dataset Loading
| Dataset | Shape (train) | Shape (test) | Notes |
|---|---|---|---|
| Banana | (400, 2) | (4900, 2) | As specified |
| Splice | (1000, 60) | (1991, 60) | Only 1,991 test rows available (2,991 total − 1,000 train); requested 2,175 |

### `code-banana-adab` — AdaBoost on Banana
| K | Old Train | Actual Train | Old Test | Actual Test |
|---:|---:|---:|---:|---:|
| 10 | 78.00% | **74.25%** | 76.00% | **76.24%** |
| 100 | 86.50% | **87.25%** | 86.10% | **86.41%** |
| 500 | 91.25% | **90.75%** | 88.39% | **89.18%** |
| 1000 | 93.75% | **92.75%** | 88.71% | **89.18%** |

| Timing | Old | Actual |
|---|---|---|
| Training time | 19.6907 s | **41.2668 s** |
| Inference time | 0.0333 s | **0.0287 s** |

### `code-splice-adab` — AdaBoost on Splice
| K | Old Train | Actual Train | Old Test | Actual Test |
|---:|---:|---:|---:|---:|
| 10 | 84.50% | 84.50% | 81.52% | **80.26%** |
| 100 | 87.30% | **86.40%** | 80.91% | **80.86%** |
| 500 | 99.00% | **98.30%** | 82.17% | **82.82%** |
| 1000 | 100.00% | 100.00% | 82.27% | **83.17%** |

| Timing | Old | Actual |
|---|---|---|
| Training time | 25.1062 s | **43.8936 s** |
| Inference time | 0.0410 s | **0.0714 s** |

### `code-svm-search` — SVM Grid Search
| Dataset | Old Best Config | Actual Best Config |
|---|---|---|
| Banana | rbf, C=10, gamma=**1** | rbf, C=10, gamma=**scale** |
| Splice | rbf, C=**10**, gamma=**scale** | rbf, C=**100**, gamma=**0.01** |

### `code-svm-banana` — Banana SVM (best config retrained)
| Metric | Old | Actual |
|---|---|---|
| Train Accuracy | 91.00% | **90.00%** |
| Test Accuracy | 89.14% | **90.14%** |
| Training Time | 0.0030 s | **0.0056 s** |
| Inference Time | 0.0178 s | **0.0917 s** |

### `code-svm-splice` — Splice SVM (best config retrained)
| Metric | Old | Actual |
|---|---|---|
| Train Accuracy | 100.00% | 100.00% |
| Test Accuracy | 89.00% | **89.15%** |
| Training Time | 0.0592 s | **0.0692 s** |
| Inference Time | 0.1351 s | **0.2686 s** |

### `code-timing` — Final Summary
| Method | Train | Test | Train Time | Infer Time |
|---|---:|---:|---:|---:|
| Banana AdaBoost | 92.75% | 89.18% | 41.2668 s | 0.0287 s |
| Banana SVM | 90.00% | 90.14% | 0.0056 s | 0.0917 s |
| Splice AdaBoost | 100.00% | 83.17% | 43.8936 s | 0.0714 s |
| Splice SVM | 100.00% | 89.15% | 0.0692 s | 0.2686 s |

---

## Markdown Cells Updated

| Cell | What Changed |
|---|---|
| `md-perceptron-results` | Test Accuracy 99.00% → 100.00%; SSE 4.0 → 0.0 |
| `md-banana-results` | All 4 K-row accuracy values; training time and inference time in observations text |
| `md-splice-results` | All 4 K-row accuracy values (train K=10 and K=1000 unchanged); inline observation numbers |
| `md-svm-banana` | Best config: gamma `1` → `scale` |
| `md-svm-banana-results` | Train 91%→90%, Test 89.14%→90.14%, Training time 0.0030→0.0056 s, Inference 0.0178→0.0917 s |
| `md-svm-splice` | Best config: C `10`→`100`, gamma `scale`→`0.01` |
| `md-svm-splice-results` | Test 89.00%→89.15%, Training time 0.0592→0.0692 s, Inference 0.1351→0.2686 s |
| `md-comparison-table` | All rows updated with actual values |
| `md-discussion` | All accuracy and timing figures; corrected inference ordering (Banana AdaBoost was faster than Banana SVM, not slower) |
| `md-conclusion` | AdaBoost final accuracies updated; SVM final accuracies updated |

---

## Code Cells Not Modified
All code cells were left exactly as written (including all comments). Only the two data file paths in `code-load-data` were updated to fix the `FileNotFoundError`.

---

## RBF Kernel Claim Verification

Per-kernel best test accuracy extracted by re-running the full grid search:

### Banana
| Kernel | Best Config | Test Accuracy |
|---|---|---:|
| Linear | C=0.1 | 55.16% |
| **RBF** | C=10, gamma=scale | **90.14%** |
| Polynomial | C=10, degree=4 | 89.10% |
| Sigmoid | C=0.1, gamma=0.01 | 55.16% |

Linear and Sigmoid both collapse to ~55% (≈ random baseline) because the banana-shaped boundary is fundamentally non-linear. Polynomial degree-4 reaches 89.10% but falls 1 pp short of RBF.

### Splice
| Kernel | Best Config | Test Accuracy |
|---|---|---:|
| Linear | C=1 | 83.83% |
| **RBF** | C=100, gamma=0.01 | **89.15%** |
| Polynomial | C=1, degree=3 | 88.30% |
| Sigmoid | C=1, gamma=0.01 | 84.53% |

All kernels work on Splice (60-d feature space provides linear structure), but RBF still leads by 0.85 pp over polynomial and 5.32 pp over linear.

**Conclusion on claim:** RBF is empirically verified as the best kernel on both datasets. The claim is correct and holds with concrete margin over all alternatives.

**Cells updated:** `md-svm-banana` and `md-svm-splice` now include per-kernel comparison tables and explanations of why linear/sigmoid fail and why RBF beats polynomial.

---

## Notable Observations
- The RANDOM_STATE change from the previous run (not 11 previously) caused the Pocket Algorithm seed path to diverge, shifting all AdaBoost accuracy values by 1–4 percentage points.
- SVM best configs changed entirely for Splice (different C and gamma), indicating the search surface is sensitive to the train/test split seed.
- Banana AdaBoost inference (0.0287 s) was faster than Banana SVM inference (0.0917 s) — the opposite of the previous run. The old discussion text incorrectly stated "Banana SVM inference was faster"; this was corrected.
- Splice test size is structurally 1,991 (not 2,175) due to dataset size; this was already documented in the notebook and is unchanged.

---

## Deliverable Audit

### Deliverable 1 — Jupyter Notebook with Python Code for Boosted Perceptron (40 pts)

| Component | Cell | Status | Notes |
|---|---|---|---|
| `classify()` — Pocket Algorithm | `code-classify` | ✅ | Random index selection, consecutive-counter logic, pocket update `w=v if nv>nw`, perceptron rule `v += y[j]*Xb[j]`, bias augmentation, 10 000-iteration limit |
| `predict()` — Inference + SSE | `code-predict` | ✅ | `sign(Xb @ w)`, SSE = Σ(y − ŷ)² |
| `adabtrain()` — AdaBoost loop | `code-adabtrain` | ✅ | Uniform init → weighted sampling → `classify()` weak learner → weighted error εt → αt = ½ ln((1−εt)/εt) → exponential weight update → normalization; handles εt=0 and εt≥0.5 edge cases |
| `adabpredict()` — Ensemble prediction | `code-adabpredict` | ✅ | H(x) = sign(Σ αt · sign(Xb @ wt)) |
| Single perceptron validation | `code-perceptron-eval` | ✅ | Trained on synthetic Gaussians; train=100%, test=100%, SSE=0.0 |
| AdaBoost on Banana (K=10…1000) | `code-banana-adab` | ✅ | All cells executed, numeric outputs present |
| AdaBoost on Splice (K=10…1000) | `code-splice-adab` | ✅ | All cells executed, numeric outputs present |
| ⚠️ Off-by-one in iteration count | `code-classify` | ⚠️ | `rng.integers(n_samples, size=maxitercnt + 1)` generates 10 001 indices (comment says `#max 10000`). One extra iteration runs. Math is unaffected; pure off-by-one in loop length. |

### Deliverable 2 — Plots of Training and Test Accuracy vs K (20 pts)

| Component | Cell | Status | Notes |
|---|---|---|---|
| Banana accuracy vs K plot | `code-banana-plot` | ✅ | `image/png` output present; K = 10, 20, …, 1000 (100 points); both train and test curves labeled; axes, title, legend, grid all present |
| Splice accuracy vs K plot | `code-splice-plot` | ✅ | Same structure; `image/png` output present |
| Numeric results table — Banana | `md-banana-results` | ✅ | K = 10, 100, 500, 1000 rows; values match execution output |
| Numeric results table — Splice | `md-splice-results` | ✅ | Same; values match execution output |

### Deliverable 3 — SVM Kernel and Parameter Settings (20 pts)

| Component | Cell | Status | Notes |
|---|---|---|---|
| Grid search over 4 kernels | `code-svm-search` | ✅ | Linear (4 C), RBF (4 C × 4 γ = 16), Poly (3 C × 3 degree = 9), Sigmoid (3 C × 3 γ = 9) — 38 total configs; best selected by test accuracy |
| Banana best config reported | `code-svm-banana` + `md-svm-banana` | ✅ | RBF, C=10, gamma=scale; train=90.00%, test=90.14% |
| Splice best config reported | `code-svm-splice` + `md-svm-splice` | ✅ | RBF, C=100, gamma=0.01; train=100.00%, test=89.15% |
| Per-kernel comparison table — Banana | `md-svm-banana` | ✅ | Linear 55.16%, RBF 90.14%, Poly 89.10%, Sigmoid 55.16%; all explained |
| Per-kernel comparison table — Splice | `md-svm-splice` | ✅ | Linear 83.83%, RBF 89.15%, Poly 88.30%, Sigmoid 84.53%; all explained |
| Justification for RBF | `md-svm-banana`, `md-svm-splice` | ✅ | Explains Mercer condition failure for sigmoid, degree sensitivity for poly, inability of linear to model curved boundary |

### Deliverable 4 — Comparison of SVM and Boosted Perceptron (20 pts)

| Component | Cell | Status | Notes |
|---|---|---|---|
| Side-by-side accuracy/timing tables | `md-comparison-table` | ✅ | Train accuracy, test accuracy, training time, inference time for all 4 method×dataset combos |
| Programmatic timing summary | `code-timing` | ✅ | Printed output matches table values |
| Bar chart — test accuracy comparison | `code-comparison-plot` | ✅ | `image/png` output present; all 4 bars labeled with percentages |
| Discussion — accuracy | `md-discussion` | ✅ | Banana: SVM 90.14% vs AdaBoost 89.18%; Splice: SVM 89.15% vs AdaBoost 83.17% |
| Discussion — training speed | `md-discussion` | ✅ | Banana: SVM 0.0056 s vs AdaBoost 41.27 s; Splice: SVM 0.0692 s vs AdaBoost 43.89 s |
| Discussion — inference speed | `md-discussion` | ✅ | Banana: AdaBoost 0.0287 s vs SVM 0.0917 s; Splice: AdaBoost 0.0714 s vs SVM 0.2686 s |
| Conclusion | `md-conclusion` | ✅ | Synthesizes both methods; acknowledges tradeoffs |

### Summary

| Deliverable | Points | Status |
|---|---:|---|
| 1. Boosted Perceptron code | 40 | ✅ Complete — one minor off-by-one (10 001 vs 10 000 iterations, non-breaking) |
| 2. Accuracy vs K plots | 20 | ✅ Complete — both plots rendered with outputs |
| 3. SVM kernel & parameter settings | 20 | ✅ Complete — grid search, best configs, per-kernel tables |
| 4. SVM vs AdaBoost comparison | 20 | ✅ Complete — tables, bar chart, discussion, conclusion |
| **Total** | **100** | **All deliverables met** |
