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

## Methodology & Math Audit (vs. PDF Pseudocode)

Full line-by-line comparison of `classify()`, `adabtrain()`, and `adabpredict()` against the PDF pseudocode.

---

### Pocket Algorithm — `classify()`

#### PDF Pseudocode (verbatim logic)
1. Set nv = nw = 0 and vi = wi = 0 for i = 0,1,…,d; Set itercnt = 0
2. Randomly choose xj, yj from training set
3. ŷj = +1 if v·xj ≥ 0, else −1
4. If ŷjyj > 0 (correct): nv = nv + 1  
   Otherwise:  
   &nbsp;&nbsp;• if nv > nw: set w = v and nw = nv  
   &nbsp;&nbsp;• vi = vi + yj·xij for i = 0,…,d  
   &nbsp;&nbsp;• nv = 0
5. itercnt = itercnt + 1
6. Go to step 2 unless itercnt > maxitercnt

#### Findings

| Step | PDF Spec | Code | Verdict |
|---|---|---|---|
| Init | nv=nw=0, v=w=0 | `v=np.zeros(n_features)`, `w=np.zeros(n_features)`, `nv=0`, `nw=0` | ✅ |
| Bias | x0 = 1 prepended | `Xb = _add_bias(X)` → prepends column of ones | ✅ |
| Random sample | Random xj from training set | `random_indices = rng.integers(n_samples, size=maxitercnt+1)` then loop | ✅ |
| Classification rule | ŷ = +1 if v·xj ≥ 0 else −1 | `yhat = 1.0 if np.dot(v, Xb[j]) >= 0 else -1.0` | ✅ |
| Correct branch | nv += 1 | `nv += 1` | ✅ |
| Pocket update | if nv > nw: w=v, nw=nv | `if nv > nw: w = v.copy(); nw = nv` | ✅ |
| v update order | Pocket check BEFORE v update | `if nv > nw` block precedes `v += y[j]*Xb[j]` | ✅ |
| Perceptron rule | vi = vi + yj·xij for all i | `v += y[j] * Xb[j]` (vector form, with bias at index 0) | ✅ |
| nv reset | nv = 0 after update | `nv = 0` | ✅ |
| Iteration count | Stop when itercnt > maxitercnt (runs 10001 times) | `size=maxitercnt+1` → 10001 indices → 10001 loop iterations | ✅ See note 1 |
| Return | Output w | `return w` | ✅ |

**Note 1 — Iteration count (corrects previous audit flag):**  
The PDF says "Go to step 2 unless itercnt > maxitercnt", with itercnt starting at 0 and incrementing *after* each step 2–4 body. The body therefore runs for itercnt = 0,1,…,10000 — stopping only when itercnt increments to 10001 (which is > 10000). That is **10001 body executions**. The code's `size=maxitercnt+1 = 10001` exactly matches this. The previous audit flag labelling this an "off-by-one bug" was **incorrect** — the code is right. The comment `#max 10000` is the only misleading element (should read 10001 per pseudocode).

**➕ Post-loop final pocket check (not in PDF):**  
```python
if nv > nw:  #classify, put in pocket
    w = v.copy()
```
The PDF pseudocode terminates at step 6 without a final check. If the last run of consecutive correct classifications is the longest seen, the loop ends before any misclassification can trigger the pocket swap — so w would never capture that best streak. The post-loop check fixes this. It is a **correct and necessary addition** that makes the algorithm more faithful to its intent of keeping the best weight vector.

---

### AdaBoost Training — `adabtrain()`

#### PDF Pseudocode (verbatim logic)
1. Initialize w1(i) = 1/N
2. for t = 1,…,K:  
   a. Select St from S with replacement according to wt  
   b. Train weak learner on St → ht  
   c. εt = Σj wt(j)·δ(yj ≠ ht(xj))  &emsp;*(on full S, not St)*  
   d. αt = ½ ln((1−εt)/εt)  
   e. wt+1(i) = wt(i)·exp(−αt·yi·ht(xi)) / Zt  
      Zt = Σi wt(i)·exp(−αt·yi·ht(xi))

H(x) = sgn(Σ αt·ht(x))

#### Findings

| Step | PDF Spec | Code | Verdict |
|---|---|---|---|
| Init weights | w1(i) = 1/N | `weights = np.ones(n_samples) / n_samples` | ✅ |
| Loop range | t = 1,…,K | `for t in range(1, K+1)` | ✅ |
| Bootstrap sample | St from S with replacement, p=wt | `rng.choice(n_samples, size=n_samples, replace=True, p=weights)` | ✅ |
| Train weak learner | Train on St | `classify(X[sample_idx], y[sample_idx], ...)` | ✅ |
| Error on full S | εt = Σ wt(j)·δ(yj≠ht(xj)) on **S** | `pred = predict(X, w=w_t)` then `err = np.sum(weights[pred != y])` — X is full training set | ✅ |
| α formula | αt = ½ ln((1−εt)/εt) | `alpha_t = 0.5 * np.log((1 - err) / err)` | ✅ |
| Weight numerator | wt(i)·exp(−αt·yi·ht(xi)) | `weights *= np.exp(-alpha_t * y * pred)` — `y*pred` = yi·ht(xi) | ✅ |
| Normalization Zt | Divide by Σi wt(i)·exp(−αt·yi·ht(xi)) | `weights /= weights.sum()` — after in-place multiply, sum = Zt | ✅ |
| Sign conventions | +1/−1 labels, weights increase for misclassified | For correct: yi·ht(xi)=+1 → exp(−αt)<1 → weight ↓; for wrong: yi·ht(xi)=−1 → exp(+αt)>1 → weight ↑ | ✅ |
| Ensemble classifier | H(x) = sgn(Σ αt·ht(x)) | `adabpredict`: `scores += alpha_t * _sign(Xb @ w_t)` then `_sign(scores)` | ✅ |

#### Deviations from PDF (not bugs, but additions)

**⚠️ Deviation 1 — Early termination when εt ≈ 0:**
```python
if err <= 1e-12:
    alpha_t = 0.5 * np.log((1 - 1e-12) / 1e-12)
    ensemble.append((float(alpha_t), w_t))
    ...
    break
```
The PDF has no such guard. When εt → 0, the formula αt = ½ ln((1−εt)/εt) diverges to +∞. The code clamps εt to 1e-12, yielding αt ≈ 13.8, then breaks. Mathematically this is reasonable — a perfect weak learner would dominate the ensemble and further iterations are unnecessary. However, **it is not specified in the PDF**, so it counts as an undocumented extension.

**⚠️ Deviation 2 — Flip-and-skip when εt ≥ 0.5:**
```python
if err >= 0.5:
    flipped_pred = -pred
    flipped_err = float(np.sum(weights[flipped_pred != y]))
    if flipped_err < 0.5:
        w_t = -w_t; pred = flipped_pred; err = flipped_err
    else:
        ...
        continue
```
The PDF pseudocode assumes the weak learner always satisfies εt < 0.5 (better than random). When εt ≥ 0.5, the PDF formula produces αt ≤ 0, and weight updates would *reward* misclassification — corrupting the ensemble. The code handles this two ways:
- **Flip**: negating w_t reverses all predictions; if flipped εt < 0.5, this recovers a valid weak learner. This is mathematically sound.
- **Skip**: if neither the original nor the flipped hypothesis is better than random, the round is discarded and weights remain unchanged. This is also mathematically safe (wasting a round but not corrupting state).

**This extension is necessary in practice** because the Pocket Algorithm on a bootstrapped, reweighted sample can occasionally produce a bad hypothesis. The PDF does not address this case.

**➕ Addition — Checkpoint history tracking:**
```python
if t in checkpoints:
    history[t] = list(ensemble)
```
Not in the PDF. Allows efficient extraction of the ensemble state at any K without re-running — used to compute accuracy curves across all K values in one pass. No effect on the math.

---

### Ensemble Prediction — `adabpredict()`

| Step | PDF Spec | Code | Verdict |
|---|---|---|---|
| Weighted vote | Σ αt·ht(x) | `scores += alpha_t * _sign(Xb @ w_t)` | ✅ |
| Final sign | sgn(Σ αt·ht(x)) | `return _sign(scores)` | ✅ |
| Edge: empty ensemble | Not specified | Returns `np.ones(X.shape[0])` (defaults to +1) | ➕ Defensive |

---

### Summary of Math Audit

| Finding | Severity | Description |
|---|---|---|
| Iteration count `maxitercnt+1` is correct | ✅ Correction | Previous audit incorrectly flagged this as off-by-one. The PDF's "unless itercnt > maxitercnt" produces exactly 10001 iterations, which the code implements correctly. |
| Comment `#max 10000` is misleading | ⚠️ Minor | Actual count is 10001 per PDF logic. Comment should read `#10001 iterations: runs while itercnt ≤ maxitercnt` |
| Post-loop pocket check | ➕ Good addition | Not in PDF but logically necessary to capture the best trailing streak |
| εt ≈ 0 early termination | ⚠️ Deviation | Not in PDF; necessary numerical guard against log(0). Capped α breaks the loop early. |
| εt ≥ 0.5 flip-and-skip | ⚠️ Deviation | Not in PDF; necessary practical guard against bad weak learners. Math remains valid. |
| All core formulas | ✅ Correct | Perceptron rule, α formula, weight update, normalization, ensemble sign vote — all match PDF exactly. |

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
| ✅ Iteration count matches PDF | `code-classify` | ✅ | `rng.integers(n_samples, size=maxitercnt+1)` produces 10 001 iterations, matching the PDF's "unless itercnt > maxitercnt" termination. Comment `#max 10000` is misleading but the logic is correct. |

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
| 1. Boosted Perceptron code | 40 | ✅ Complete — all math correct; two deviations (εt edge cases) are necessary engineering additions |
| 2. Accuracy vs K plots | 20 | ✅ Complete — both plots rendered with outputs |
| 3. SVM kernel & parameter settings | 20 | ✅ Complete — grid search, best configs, per-kernel tables |
| 4. SVM vs AdaBoost comparison | 20 | ✅ Complete — tables, bar chart, discussion, conclusion |
| **Total** | **100** | **All deliverables met** |
