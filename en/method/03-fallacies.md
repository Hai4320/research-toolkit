# 03 — 12 Classic Cognitive Traps

> This is a list of enemies. Each entry: **name → description → example → how to check if you're falling for it**.

Before trusting your own result, read this list and self-check. It is short because it is concentrated, but covers ~80% of the most common mistakes in empirical research.

---

## 1. Gambler's Fallacy

**Description**: Believing independent events depend on history. "Red came up 5 times, this time must be black."

**Examples beyond lottery**:
- "Many startups failed launches this month, next month must go better."
- "I've been unlucky for ages, my luck is due to turn."
- "This stock dropped 5 sessions, it must rebound."

**Self-test**: Is your data really i.i.d.? If yes (lottery, true coin flip), then history carries **no** information about the future. If you think it does, ask: what physical/causal mechanism creates that dependency?

---

## 2. Hot Hand Fallacy

**Description**: Opposite of #1 — believing winning/losing streaks will continue. "On a roll, going all in."

**Examples**:
- "This number came out 3 times in a row, it'll keep coming" (hot number).
- "This fund beat the market 5 years straight, I trust the manager."
- "This team won 8 games, they'll win again."

**Self-test**: Streaks are usually random. Compute the probability such a streak appears purely by chance — you'll be surprised how high it is. Especially when you observe thousands of entities and only notice the entity with the longest streak (survivorship — see #6).

---

## 3. Base Rate Neglect

**Description**: Treating high probability in a test while forgetting the base rate of the phenomenon. "Cancer test 99% accurate positive → I have 99% chance of cancer" — wrong if the disease is rare.

**Examples**:
- "Model is 99% accurate on fraud" — but if fraud is only 0.1% of transactions, total precision can still be very low.
- "Interview felt this candidate is great" — forgetting the input pool is already strong, so even average candidates "feel great".

**Self-test**: Apply Bayes. P(A|B) = P(B|A) × P(A) / P(B). Especially when A is rare (low P(A)), even a very accurate test gives low posterior.

---

## 4. Survivorship Bias

**Description**: Looking only at "survivor" entities, forgetting those that dropped out of the sample.

**Examples**:
- "All billionaires dropped out of school → dropping out makes you a billionaire" (forgetting the millions of dropouts who didn't).
- "This investment strategy backtested 10 years gives 30%/year" — backtested on funds still in existence. Bankrupt funds were deleted from the dataset.
- "All my remaining customers love the product" — you don't talk to the customers who churned.

**Self-test**: Does your sample exclude any entity? "If lost, who/what disappears from the data?" Find precisely those entities to add back.

---

## 5. Selection Bias

**Description**: Your sample doesn't represent the population you care about, because the sampling mechanism correlates with the variable being measured.

**Examples**:
- Twitter survey on reading habits → Twitter people don't represent the general population.
- "Patients receiving treatment X recover faster" — but doctors prescribe X for mild cases, Y for severe.
- A/B test self-selected (opt-in beta users) — they don't behave like normal users.

**Self-test**: Could the sampling process correlate with the outcome? Are there groups that never enter the sample?

---

## 6. P-Hacking & Multiple Comparison

**Description**: Test many hypotheses / fine-tune a lot — some will be "significant" by pure chance.

**Examples**:
- Try 20 strategies, publish the one with p<0.05.
- Tweak cutoffs, thresholds, features 50 times — final version "looks great".
- Subgroup analysis: "This strategy works for women aged 30-40 in the Northeast on Tuesdays" — already cut into many subgroups.

**Self-test**: 
- Count how many versions you tried.
- Apply Bonferroni: α_corrected = 0.05 / N (N = number of tests).
- Or Benjamini-Hochberg for FDR.
- Pre-register before looking at results.

---

## 7. Look-Ahead Bias / Data Leakage

**Description**: Model "peeks" at future information it won't have when deployed.

**Examples**:
- Backtest uses today's closing price to decide a morning buy.
- Feature engineering normalization (mean, std) on the entire dataset before split.
- Target leakage: feature actually derived from the label itself (e.g., "paid late fee" to predict default).

**Self-test**:
- Every feature at time t uses only data ≤ t-1.
- Fit preprocessing (scaler, imputer) only on train, transform on val/test.
- Ask: is this feature truly available at prediction time?

---

## 8. Regression to the Mean

**Description**: Extreme values tend to be more moderate on subsequent measurement. Easy to mistake for "intervention effect".

**Examples**:
- "Top performer this period usually drops next period" → not because of pressure, but because this period they were above true ability.
- "After scolding the bottom employees, they improved" → not because of scolding, but because last period they were at the bottom.
- "Therapy X helps the most severe patients recover the most" → most severe patients naturally regress toward the mean.

**Self-test**: Without intervention, how much does the control group naturally recover? Pre-/post- is not enough — you need a counterfactual.

---

## 9. Simpson's Paradox

**Description**: A trend overall can reverse when split into subgroups (or vice versa) due to confounding.

**Examples**:
- School A has lower admit rate than B overall — but higher in every department. Cause: A has more applicants in competitive departments.
- Treatment X works better than Y in both men and women separately — but worse overall. Cause: X is prescribed more often for severe cases.

**Self-test**: Is there a confounder (Z) correlated with both treatment and outcome? Stratify by Z to see if direction flips.

---

## 10. Texas Sharpshooter

**Description**: Shoot at the wall, then draw circles around the holes. Find pattern after looking at the data.

**Examples**:
- "Numbers 13 and 27 showed up together 3 times in a row — pattern!" — but you didn't pre-specify this pair.
- Disease cluster around a factory → could be real, could be noise because you drew "cluster" after looking.
- "Stocks starting with G performed best in Q3" — after trying every cutting criterion.

**Self-test**: Did you pre-specify this pattern? Or did you find it after looking? If after, you need a fresh out-of-sample test.

---

## 11. Goodhart's Law

**Description**: "When a metric becomes a target, it ceases to be a good metric."

**Examples**:
- Measure employees by lines of code → code bloat.
- Measure by backtest performance → fine-tune until metric looks great, generalization is bad.
- KPI "ticket processing time" → tickets get closed hastily, reopened later.

**Self-test**: Does your metric still align with the real goal? Is there a way to "game" the metric without creating real value?

---

## 12. Confirmation Bias

**Description**: Selectively seek/remember data that supports your hypothesis.

**Examples**:
- Reading backtest results, attention to wins, gloss over losses.
- Asking user feedback in a leading way for the answer you want.
- When the hypothesis looks weak, naturally find reasons why "now isn't the right time to test".

**Self-test**: If the hypothesis you're testing were the **opposite** of what you want, what would you do differently? Each difference = a sign of confirmation bias.

---

## Bonus: Domain-specific traps

When applying to a new domain, add domain-specific traps to this file. Examples from the lottery domain:

- **"Hot number" / "Due number"**: direct variants of #1 and #2.
- **"Pattern frequency"**: actually #10 (sharpshooter) — you're finding the pattern after looking.
- **"Strategy outperformed in 6/10 backtest periods"**: usually noise + multiple-comparison when many strategies were tried.
- **Backtest improvement that doesn't survive real-world friction** (transaction costs, taxes, slippage in finance; or implementation costs in life experiments).

## How to use the list

Before trusting your own result, **go through each entry** and write a short answer:

```
1. Gambler's fallacy — Applies? □ Counter-argument: ...
2. Hot hand — Applies? □ Counter-argument: ...
... 
12. Confirmation bias — Applies? □ Counter-argument: ...
```

A bit tedious, but much faster than deploying a wrong strategy.
