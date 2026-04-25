# Research Charter — [Project Name]

> Fill in this template BEFORE starting research. Purpose: pre-commit to prevent HARKing and optional stopping. When done, save in a timestamped location (git commit, or dated file).
>
> **Filed at**: `research/charter.md` — one charter per project. Subsequent versions append to the change log at the bottom; do not rewrite history.

**Date created**: YYYY-MM-DD  
**Author**: ...  
**Version**: 1 (any later changes are logged at the bottom, history is not rewritten)

---

## 1. Research question

> A single sentence, specific enough to be wrong. Avoid vague words ("better", "effective") — define "better" by a specific metric.

```
[Write the question here]
```

## 2. Type of claim

Check the type you'll make (affects burden of proof):

- [ ] **Descriptive**: only describes observation already made. Low burden.
- [ ] **Inferential**: generalizes outside the sample. Medium burden.
- [ ] **Prescriptive**: recommends action. Highest burden.

## 3. Motive — Self-acknowledgment

> Honestly note: which result do you **want**? Why?

```
[I want the answer to be ... because ...]
```

> Awareness of motive helps detect confirmation bias along the way.

## 4. Hypothesis and null hypothesis

### H0 (null)
```
[Null hypothesis — usually "no effect / edge / difference"]
```

### H1
```
[Main hypothesis you want to test]
```

### Alternative explanations to rule out
- [ ] Alternative explanation 1: ...
- [ ] Alternative explanation 2: ...
- [ ] Potential confounder: ...

## 5. Implicit assumptions (Assumption Inventory)

> List all assumptions. Each wrong assumption = a path for the result to become invalid.

- [ ] Sample represents: ...
- [ ] Data is i.i.d.? Reason to believe: ...
- [ ] Data is stationary? Reason to believe: ...
- [ ] Distribution: ...
- [ ] Independence of events: ...
- [ ] Other: ...

## 6. Data

- **Source**: ...
- **Sample size**: ...
- **Time of collection**: ...
- **Anyone/anything excluded from sample?** ... (watch for survivorship bias)
- **How to split train/val/test**: ...
- **Will the test set "sleep" until the end?** [ ] Yes

## 7. Method

- **Main method/model**: ...
- **Baseline comparison**: ... (random / naive / status quo / expert)
- **Statistical test used**: ...
- **Multiple-comparison correction**: ...
- **Total number of hypotheses to test**: N = ...

## 8. Success criteria (PRE-COMMIT)

> This is a commitment. If the post-research result fails these criteria, declare it failed and pivot.

- **Minimum effect size worth caring about**: ...
- **Statistical significance threshold (post-correction)**: α = ...
- **Maximum CI width acceptable**: ...
- **Robustness checks must pass**: ...
- **Out-of-sample performance must be ≥ ... vs. baseline**

## 9. Stop criteria (Kill Criteria)

> When do I ABANDON the hypothesis and stop trying to salvage?

- **Maximum time for this research**: ...
- **Maximum sample size before abandoning**: ...
- **If deadline reached without success criteria** → declare not met, do not extend.

## 10. Plan if not met (REFRAME)

> Pre-commit pivot direction — force yourself to think before emotions heat up.

- [ ] **Direction A — Structured exploration**: switch to understanding the system. Specific output: ...
- [ ] **Direction B — Coverage**: switch to diversification. New metric: ...
- [ ] **Direction C — Entertainment**: acknowledge as non-edge activity. Budget: ...
- [ ] **Archive**: lose gracefully, write postmortem, move on.

## 11. Risk if wrong

> If the conclusion is wrong and gets applied, what are the consequences?

- **For me**: ...
- **For others**: ...
- **Risk level**: [ ] Low [ ] Medium [ ] High
- **Mitigation**: ...

## 12. Independent reviewer

- **Person/agent reviewing**: ...
- **Mandate**: find errors (not validate).
- **Has no incentive to share my conclusion?** ...

## 13. Reproducibility

- [ ] Code under version control
- [ ] Random seed fixed (per phase)
- [ ] Data snapshotted
- [ ] Hyperparam, threshold logged
- [ ] Could anyone else re-run from raw data?

---

## Change Log

| Date | Version | Change | Reason |
|---|---|---|---|
| YYYY-MM-DD | 1 | Charter created | - |

> **RULE**: Editing the charter after seeing validation results = HARKing. If you need to change methodology mid-research, log it with reason **before** seeing the new results.
