# 01 — Philosophy: Entropy is the default

> Before learning the process, you must internalize the underlying mindset. A process without the underlying mindset is just ritual.

## Five foundational principles

### 1. Entropy is the default. Edge must be proven.

In most prediction problems with real value (markets, user behavior, medical outcomes, lottery, sports results), **the starting assumption is: there is no edge.** Signal, if any, is very small. The world is more efficient than you think — if an edge is so obvious you spot it on a weekend, somebody has likely been exploiting it for a long time, or it is an illusion.

**Practical consequence**: The burden of proof rests entirely on the claimant of an edge. There is no "absence of contradicting evidence → strategy is usable". That inverts the burden of proof.

### 2. You are your own biggest enemy.

When you have a hypothesis you **want** to be true (because you've invested time, because you promised someone, because of ego), you will:

- Unconsciously dismiss contradicting data.
- Interpret borderline results in the favorable direction.
- Stop research at the moment it is "looking good" (optional stopping).
- Try many variants until one "works" — then forget how many you tried.
- Change success criteria after seeing results.

This is not because you are bad. This is how the human brain works. Process exists **precisely because** you cannot trust your own brain in motivated situations.

### 3. Rigor on the work, charity on the intent.

When reviewing (your own or someone else's): attack mercilessly the **argument, data, methodology**. But hold a **neutral attitude toward the person who created it** — most mistakes are honest mistakes, not fraud. These two things do not contradict:

- "This backtest method has lookahead bias" ✓
- "You intentionally hid the lookahead bias to make results look better" ✗ (unless evidence)

Separating person from work helps others (and yourself) accept feedback.

### 4. Distinguish "prediction" from "structured exploration".

Many problems **cannot be predicted**, but you can still do well a different way:

- Lottery: cannot predict the next number. But you can choose tickets so coverage is high, diverse, providing a better playing experience.
- Investing: cannot predict short-term prices. But you can manage risk, diversify, reduce costs.
- Health: there is no magic "biohack". But you can track systematically, eliminate wrong hypotheses, understand your own body better.

When you cannot predict, **don't pretend you can**. Pivot to a different value you ACTUALLY create. See [05-reframing-pivot.md](05-reframing-pivot.md).

### 5. The research process must always be fully documented.

Documentation is not paperwork — it is the **substrate of accumulated knowledge**. A research effort without documentation is a private experience that dies with you, defaults to your in-the-moment mood, and cannot be audited, reproduced, or learned from.

**Specifically, every cycle requires**:

| Artifact | When | Purpose |
|---|---|---|
| **Charter** ([templates/charter.md](../templates/charter.md)) | Before cycle 1 / on major scope shift | Pre-commit research scope, success/kill criteria |
| **Hypothesis card** ([templates/hypothesis.md](../templates/hypothesis.md)) | Per hypothesis, before validation | Pre-commit predictions, statistic, threshold |
| **Cycle review** ([templates/cycle-review.md](../templates/cycle-review.md)) | At every cycle close, regardless of outcome | Neutral close artifact: result vs criteria, verdict, carry-over |
| **Postmortem** ([templates/postmortem.md](../templates/postmortem.md)) | When cycle has painful lessons / multiple traps | Deeper failure analysis (complements cycle review, doesn't replace) |
| **Cycle history** (`RESEARCH_HISTORY.md`) | Append-only on every cycle close | Project-wide chronological log; never rewrite |
| **Architecture review** ([templates/architecture-review.md](../templates/architecture-review.md)) | Periodically (every N cycles, every quarter, before major refactor) | Audit drift, technical debt, methodology integration |

**Rules of documentation discipline**:

1. **Pre-commit before action**: charter + hypothesis card exist BEFORE data is touched. Editing them after seeing results = HARKing.
2. **History is append-only**: `RESEARCH_HISTORY.md` and closure records never rewrite. New decisions append; old entries stay even when wrong (annotate with revision links).
3. **Null results are first-class**: a closed cycle with "no edge found" is documented with the same rigor as a confirmed result. Without this, the project's null-rejection knowledge evaporates.
4. **No silent refactors**: changing scope, methodology, architecture, or accepted result requires a written entry. Architectural reviews catch silent drift.
5. **Documentation is verified at architecture review**: the [architecture-review.md](../templates/architecture-review.md) §10 documentation hygiene check is a structural enforcement of this principle.
6. **One source of truth per fact**: don't duplicate methodology content into project-local docs — copy/symlink the toolkit.

If you finish a cycle and have nothing written down, the cycle did not happen — its lessons are gone the moment your context window resets. The only proof that you did the work, and the only mechanism to learn from it, is the documentation you produced.

## Two mindsets to train

### The "prosecutor" mindset (against yourself)

Every time you have a beautiful result, switch to prosecutor and ask:

1. **How many versions did I try before this one worked?** (Multiple comparison)
2. **Could this just be noise?** (Effect size, sample size)
3. **If I swapped data for random, would the result differ much?** (Permutation test)
4. **Did I commit success criteria BEFORE seeing results?** (Pre-registration)
5. **Is anything in the data letting the model "peek" into the future?** (Lookahead bias)
6. **If I am wrong, how do I find out?** (Falsifiability)

If you cannot ask yourself those 6 questions, your result is not actionable yet.

### The "lose gracefully" mindset

A research conclusion of "no edge" **is not failure**. It is a valuable result: you have just eliminated a hypothesis, saving future time/money for yourself or others.

The problem is that cultural reward is biased toward "positive results" — papers, posts, products all prefer the story "I found X". You must actively counter this bias by:

- **Pre-commit**: declare upfront (to yourself, or in writing) what counts as success and failure.
- **Honor null results**: write a clear postmortem for each strategy that fails. They are assets, not embarrassments.
- **Honest reporting**: when telling others, don't "trim" the story to make it prettier.

## A quick test

Before starting any research, answer this question out loud (or in writing):

> "If after the research the conclusion is **no edge / no effect**, what will I do next?"

If your answer is *"I'll keep trying variants until I find an edge"* — you are setting yourself up for a no-lose situation, and your eventual positive result is **almost certainly noise**.

A good answer is: *"I'll accept it, write a postmortem, and move to a different question / pivot to a non-prediction reframing."*

This is the difference between science and superstition with data.
