# 02 — The 5-Phase Workflow

> This process is intentionally slower than "just dive into code". That is a feature, not a bug. Most time you save by skipping the process gets lost many times over chasing an edge that doesn't exist.

## Overview

```
[1. FRAME]  →  [2. HYPOTHESIZE]  →  [3. VALIDATE]  →  [4. ADVERSARIAL REVIEW]  →  [5. SHIP or REFRAME]
                          ↑                                          │
                          └──────  loop on reviewer reject  ←────────┘
```

Each phase has clear **input**, **action**, **output**, and **stop criteria**.

---

## Phase 1 — FRAME

**Goal**: Before writing a line of code, know exactly what question you are asking and what answer you will accept.

**Action**:
1. Write the research question as **a single sentence**, specific enough to be wrong.
   - ✗ "I want to find out if the lottery has a pattern"
   - ✓ "In the last 500 draws of a 5-out-of-35 lottery game, do numbers that appeared in the past 30 days have a higher probability of appearing next at 95% confidence vs. the uniform distribution?"

2. Classify the kind of claim you'll make:
   - **Descriptive**: "X was observed" — low risk.
   - **Inferential**: "X generalizes to Y" — medium risk.
   - **Prescriptive**: "Because of X, do Y" — high risk, highest burden of proof.

3. List **implicit assumptions** (assumption inventory). At minimum, answer:
   - Is your sample representative of the population you care about?
   - Is the data i.i.d.? Stationary?
   - What distribution assumption (normal, fat-tail, bounded)?
   - Are relationships truly independent, or only uncorrelated?

4. Define **success criteria** and **stop criteria** BEFORE looking at the data:
   - Minimum effect size worth caring about?
   - Required sample size?
   - When do I declare "no edge" and quit?

**Output**: `charter.md` file (see [templates/charter.md](../templates/charter.md)) — your pre-commit.

**Stop criterion for the phase**: If you cannot answer "if I am wrong, how do I know?" — go back and redefine the question.

---

## Phase 2 — HYPOTHESIZE

**Goal**: Enumerate competing hypotheses, **including the null hypothesis**.

**Action**:
1. List **all** plausible hypotheses — not only your favorite.
   - H0 (null): no edge / effect beyond chance.
   - H1, H2, …: alternative explanatory hypotheses.

2. For each hypothesis, write a hypothesis card (see [templates/hypothesis.md](../templates/hypothesis.md)) with:
   - Precise statement.
   - Predicted observable if hypothesis is true.
   - Predicted observable if hypothesis is FALSE.
   - Specific test, specific statistic.

3. **Limit how many hypotheses you test simultaneously.** Each hypothesis you try is a "die roll for noise". If you test 20 hypotheses at α=0.05, you expect 1 to be "significant" purely by chance.

4. **Pre-register**: commit to which hypothesis you will test, with which test, before looking at validation data. This can be as simple as a timestamped git commit.

**Output**: 1-N hypothesis cards, each falsifiable.

**Stop criterion**: Each card must answer "what result will make me give up this hypothesis?". If no answer, the hypothesis is unscientific — discard or rewrite.

---

## Phase 3 — VALIDATE

**Goal**: Test the hypothesis seriously, not friendly.

**Action**: Apply the full protocol in [04-validation.md](04-validation.md). Critical points summary:

1. **Train/Validation/Test split**: A dataset is touched once, at the end.
2. **Time-aware splits** for time-series data — no random shuffle.
3. **Lookahead bias check**: future data must not leak into past features.
4. **Multiple-comparison correction**: if testing N hypotheses, use Bonferroni or Benjamini-Hochberg.
5. **Effect size + confidence interval**, not just p-value.
6. **Permutation / shuffle test** as baseline: how does your result compare against shuffled labels?
7. **Out-of-sample**: if validation and test set differ a lot, **trust the test set**, not validation.

**Output**: A report containing:
- Hypothesis test → reject / fail-to-reject.
- Effect size + CI.
- Comparison with random baseline.
- Diagnostics (residual, calibration, robustness checks).

**Stop criterion**: If results do not satisfy the **success criteria pre-committed in Phase 1** — move to Phase 5 (REFRAME), not toward fine-tuning to salvage.

---

## Phase 4 — ADVERSARIAL REVIEW

**Goal**: Find errors you cannot see.

**Action**:
1. Have a person (or an AI agent with adversarial prompt — see [agents/rigor-reviewer.md](../agents/rigor-reviewer.md)) review independently.
2. The reviewer must:
   - **Have no incentive** to share your result (not a colleague on the same project).
   - **Have full access** to data, code, methodology.
   - **Have a mandate** to find errors, not validate.

3. If the reviewer raises an error, three good responses:
   - **Fix** and return to Phase 3 with the fix.
   - **Counter-argue with data** that the issue isn't actually an issue (must argue from evidence, not feeling).
   - **Accept** that the conclusion is weaker than you thought → reduce scope of claim.

4. If the reviewer passes, **still** run these sanity checks:
   - If you shuffle labels randomly, does the model still "see" pattern? (If yes → methodology bug)
   - On a totally different subset (different geography, different year, different demographic), is it robust?
   - Does the edge survive **real costs**: transaction fees, taxes, slippage, friction?

**Output**: Review report (see template in [agents/rigor-reviewer.md](../agents/rigor-reviewer.md)). Note clearly which concerns are resolved vs. unresolved.

**Stop criterion**: All critical errors (per reviewer's definition) are fixed or accepted with explicit caveats.

---

## Phase 5 — SHIP or REFRAME

After Phase 4, you are in one of three states:

### A. Edge confirmed → SHIP

- Deploy the strategy, **at small scale first** (paper trading / pilot).
- Monitor live performance: does the edge survive, or was it just a good validation set?
- Pre-commit **kill criteria**: if live performance drops below threshold X for N days, stop.
- Write a postmortem **even on success** — record what went well to repeat it.

### B. Edge not confirmed → REFRAME

This is the most common outcome, and **not failure**. Three pivot directions:

1. **Structured exploration**: from "predict the output" → "understand the system better". Output is understanding, not advantage.
2. **Coverage / diversity**: instead of choosing 1 "right" ticket, choose N tickets that cover the space well. See [case-study](../case-study/lottery-prediction.md) for an anonymized example.
3. **Conscious entertainment**: acknowledge this is a non-edge activity, make the experience/UX better, set explicit budget.

See [05-reframing-pivot.md](05-reframing-pivot.md) in detail.

### C. Inconclusive → SHELVE

- Sample size too small, data not good enough, or wrong question. Write a status note documenting blockers, archive.
- Set a **trigger** to revisit: "When 1 more year of data is available, reopen."

**Final output**: postmortem file (see [templates/postmortem.md](../templates/postmortem.md)). This is the most valuable asset from each research effort.

---

## Common anti-patterns

| Anti-pattern | Symptom | Avoid by |
|---|---|---|
| **HARKing** (Hypothesizing After Results are Known) | "Oh, turns out I was testing hypothesis Y" after seeing results | Pre-register in Phase 1 |
| **Optional stopping** | Stopping research at "good-looking" moment | Decide stop criterion in advance |
| **Garden of forking paths** | Each small decision (cleanup, feature, threshold) tilts the result | Document every decision, run sensitivity analysis |
| **Goodhart drift** | Initial metric is good, gradually you optimize the metric instead of the goal | Periodically return to "what is the real goal?" |
| **Skip-the-review** | "No need to review, I was careful" | Phase 4 is not optional |

## Practical cadence

In reality, not every research effort needs the full 5 phases at high intensity. Calibrate by risk:

- **Low risk** (harmless personal experiment): Phase 1 + 3 + 5 light.
- **Medium risk** (decisions affecting personal finance/health): full 5 phases.
- **High risk** (affecting others, irreversible): 5 phases with at least 2 independent reviewers, possibly public pre-registration.
