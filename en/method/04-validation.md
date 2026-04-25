# 04 — Validation Protocol

> Bad validation creates false confidence — worse than no validation. This file specifies how to validate properly.

## Core principles

1. **Split data once, as early as possible.** Test set must not be "seen" during research — only at the final verdict.
2. **Clear counterfactual.** "Better than what?" There must be a specific baseline, not "vs gut expectation".
3. **Effect size + uncertainty, not just p-value.** "Statistically significant" ≠ "practically valuable".
4. **Robustness > Beauty.** A modest but robust result is more trustworthy than an impressive but fragile one.

---

## 1. Data splitting

### For i.i.d. data (cross-sectional)

```
Full dataset
├── 60% Train      → fit model, try features
├── 20% Validation → choose hyperparameter, compare models
└── 20% Test       → run ONLY ONCE at the end, no return for fine-tuning
```

### For time-series data

**No random shuffle**. Must respect chronological order:

```
Time →
[─── Train ───][── Val ──][── Test ──]
```

Or walk-forward (better for long horizon):

```
Fold 1: [Train: t1..t100] → [Val: t101..t110]
Fold 2: [Train: t1..t110] → [Val: t111..t120]
Fold 3: [Train: t1..t120] → [Val: t121..t130]
...
```

### Hard rules

- **Test set touched once.** If you ran the test twice, your "test set" has effectively become "validation set" — you need a new test set.
- **Preprocessing fit on train only.** Scaler, imputer, PCA, encoder all fit on train, transform on val/test. Violating this is direct leakage.
- **Features using future data = leakage.** Even "normalize by overall mean" is leakage — overall mean contains val/test information.

---

## 2. Mandatory baseline

A standalone number means nothing. Always compare against:

| Baseline | When to use |
|---|---|
| **Random** | Every predictive task — result must beat random meaningfully |
| **Naive: predict the most common value** | Classification/regression with a popular baseline |
| **Naive: predict = previous value** | Time-series — "tomorrow = today" is often hard to beat |
| **Simple model** (logistic/linear, mean baseline) | Every complex model must beat the simple model |
| **Status quo** | Alternative strategies must beat current practice |
| **Human expert** | When applied in a domain with experts |

If your strategy can't beat Random or Naive — you have nothing.

---

## 3. Multiple-Comparison Correction

Testing N hypotheses at α=0.05, you expect 0.05×N "significant" hits purely by chance.

### Bonferroni (conservative, simple)
α_corrected = 0.05 / N

If testing 20 strategies, each strategy needs p < 0.0025 to achieve family significance 0.05.

### Benjamini-Hochberg (FDR control, less conservative)
Sort p-values ascending: p₁ ≤ p₂ ≤ ... ≤ pₙ. Find largest k such that pₖ ≤ (k/N) × α. Reject hypotheses 1..k.

### Practical rules

- **Honestly count** how many tests you ran, including "unpublished" ones.
- Hyperparameter tuning on validation **also counts** as multiple testing — that's why you need a separate test set.
- Subgroup analysis ("works for group X") always needs correction unless pre-specified.

---

## 4. Permutation / Shuffle Test

The strongest test against "model sees pattern in noise":

1. With real data, run the model → get metric M.
2. Shuffle labels (or target) randomly. Run the model again → get metric M'.
3. Repeat 1000 times. Distribution of M' is the null distribution (the model "should not see anything").
4. If M is in the extreme tail of the M' distribution → real signal.
5. If M is within the M' distribution → the model is eating noise.

Especially powerful when you can't trust parametric tests (because assumptions don't fit).

---

## 5. Effect Size + Confidence Interval

**P-value measures whether you're sure there's an effect. Effect size measures whether the effect is worth caring about.**

A blood pressure drug lowering 0.1 mmHg with p = 0.001 is clinically useless despite being "highly significant".

Always report:
- Effect size (Cohen's d, odds ratio, lift, %, or original units).
- 95% confidence interval (CI).
- Practical significance threshold (defined in Phase 1).

### CI rules

- Wide CI → need more sample.
- CI containing 0 (for effect, not ratio) or 1 (for ratio) → don't reject null at this level.
- Always report CI, even when p < 0.05. CI tells precision; p only says yes/no.

---

## 6. Backtesting (specific to time-series / strategies)

Apply when you test a strategy (investment, recommendation, pricing) on historical data.

### Pre-trust checklist

- [ ] **Lookahead bias**: every decision at t uses only data ≤ t-1 (including features, normalization, hyperparams).
- [ ] **Survivorship bias**: dataset includes "dead" entities (delisted, churned, removed)? If not, results are overstated.
- [ ] **Selection bias**: how did you choose the universe (stocks, products, users)? Choosing based on traits correlated with outcome → bias.
- [ ] **Realistic friction**: transaction costs, slippage, market impact, tax, latency. Small edges often vanish after friction.
- [ ] **Capacity**: does the strategy work at real scale? Or only with $1k while failing at $10M?
- [ ] **Out-of-sample**: results on data after the model was "frozen" similar to in-sample?
- [ ] **Robustness**: small changes (start date, hyperparam, universe) shift results a lot? If yes → overfit.
- [ ] **Multiple comparison**: how many strategies did you try before this one?
- [ ] **Regime dependence**: does the strategy depend on a specific regime (bull market, low volatility)? Test on other regimes.

### One-line sanity check

Run the strategy on **shuffled data**. If it still produces equivalent profit → the methodology has a bug, not an edge.

---

## 7. Calibration (for probabilistic models)

Model says "70% probability X" — among predictions of 70%, do ~70% actually happen?

**Reliability diagram**: split predicted probability into bins (0-10%, 10-20%, ..., 90-100%). Plot predicted vs. observed frequency. Ideal line is y=x.

A model can have good AUC but bad calibration — still classifies correctly, but "70%" is actually 90%. When you need to act on probability (Kelly criterion, expected value), calibration matters more than AUC.

---

## 8. Robustness Checks

Before shipping, run:

- **Sensitivity to start/end date** (for time-series): shift window ±10% — does the result hold?
- **Sensitivity to hyperparameter**: ±20% on each hyperparam, results change small?
- **Bootstrapped CI**: resample data 1000 times, what does the metric distribution look like?
- **Subset performance**: does the strategy work on every subset or only some?
- **Temporal stability**: split into time periods, results within each period?

If results are sensitive to every small tweak — not robust, don't ship.

---

## 9. When sample is small

Many real-world problems have small sample (n < 30, sometimes n = 1 yourself). Validation is still possible:

- **Bayesian thinking**: prior + likelihood → posterior. When data is limited, prior is strong.
- **Wide confidence interval**: accept wide CI, make decisions under wide CI.
- **Large effect size required**: small sample only detects large effects. If the effect is small, small sample tells you nothing.
- **N-of-1 design**: for self-experiments. Alternating periods (A-B-A-B), randomize order, blind if possible.
- **Honest about uncertainty**: "I tried it for 2 weeks and felt better" is not evidence — it's only a hint that this is worth testing more rigorously.

---

## 10. Reporting results

Honest reporting includes:

- **Effect size + CI** (primary)
- **P-value** (secondary, if appropriate)
- **Number of tests run + correction**
- **Baselines comparison**
- **Robustness check results**
- **Failed strategies / dead-ends** (especially important)
- **Limitations** (sample size, generalizability, assumptions)

Don't "trim" the report: don't hide failed strategies, don't round-up p-values, don't only show the best time window.

If you report fully and the result is still convincing — trustworthy. If you have to hide things to be convincing — not trustworthy.
