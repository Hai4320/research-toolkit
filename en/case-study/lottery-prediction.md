# Case Study — A Lottery Prediction Project (Anonymized)

> The project from which this toolkit was distilled. The specific product details are intentionally redacted; only the methodology and lessons are public.

> **Note**: The source project's code and product specifics are private. This case study shares **methodology and lessons learned** only — not implementation, branding, or proprietary design choices.

## Background

**Problem**: A research project on a high-randomness prediction domain — specifically, a lottery game where players pick numbers and prizes depend on matching draws. Initial (implicit) goal: find a number-picking strategy better than random.

**Domain characteristics**: Extremely random (lottery is engineered to be near-i.i.d. by design). This is where **gambler's fallacy** is strongest, and where motivated reasoning is most dangerous.

## Phase 1: Frame (poorly executed early)

**Initial question (implicit)**: "Is there any number-picking strategy better than random?"

**Problem**: The question didn't pre-specify "better by which metric" or "when do I declare not-better".

→ **Lesson**: A charter (see [templates/charter.md](../templates/charter.md)) matters. Without pre-commit, easy to HARK and optional-stop.

## Phase 2: Hypothesize (tried too many)

The team coded and backtested **many strategies**, including:

- Frequency-based (hot numbers — pick by appearance count)
- Long-absence (cold numbers / "due numbers" — pick numbers absent for a long time)
- Pair-frequency (pairs that historically appear together)
- Pattern detection (recurring spacing in draw sequences)
- Exponential decay (frequency with time-decay weighting)
- Bayesian-Dirichlet (posterior over number probabilities)
- Max-entropy / stratified sampling
- Distance-based diversity
- Not-repeat (avoid recently drawn numbers)
- Wheeling systems
- Ensemble combinations
- Random baseline

**Problem realized (later)**: Each strategy tried = one die roll for noise. With 10+ strategies and α=0.05, expect 0.5+ strategies to be "significant" purely by chance.

→ **Lesson**: Must pre-specify the number of strategies and apply multiple-comparison correction. See [04-validation.md](../method/04-validation.md) §3.

## Phase 3: Validate (uncovered many traps)

When backtesting seriously, the findings were:

### 3.1 Every "frequency-based" strategy is a gambler's fallacy variant

- "Hot numbers" → assumes non-stationarity or physical bias without evidence.
- "Due numbers" → assumes "must come out to balance" — wrong for i.i.d.
- "Pair frequency" → finding patterns in noise (Texas sharpshooter).

### 3.2 Backtest on historical data gives false-beautiful results

Causes:
- **In-sample fitting**: strategy tuned on the same data it's tested on.
- **Multiple comparison**: many strategies, some look good naturally.
- **Survivorship**: backtested on a specific period, not robust across time.

### 3.3 Sanity check with shuffled data

If you randomly shuffle historical results and re-run the strategies → **results similar to a random strategy**. This is a strong signal: the strategy is not actually "seeing" pattern — it's exploiting structure in a specific window.

→ **Lesson**: Permutation/shuffle test is a strong defense, worth running on every predictive claim.

## Phase 4: Adversarial Review

A `rigor-reviewer` adversarial-prompt agent (Claude-based) was added with mandate to:

- Find gambler's fallacy and variants.
- Check lookahead bias in backtests.
- Evaluate multiple-comparison correction.
- Warn if strategy promotes harmful gambling behavior.

The rigor-reviewer in this toolkit ([agents/rigor-reviewer.md](../agents/rigor-reviewer.md)) is the **domain-agnostic version** of that agent — generalized for any field.

→ **Lesson**: An adversarial reviewer (human or AI) is the most valuable investment in research. Without it, confirmation bias goes unchecked.

## Phase 5: Reframe (the actually-valuable output)

After accepting **no prediction edge** (the lottery is genuinely i.i.d.), the project pivoted in 2 directions:

### Direction B — Coverage / Diversity

The number-suggestion engine moved from "predict winning numbers" to **"diversity-first ticket selection"**:

- Light composite score only as seed.
- Constraint: each number appears at most a small bounded number of times across all tickets.
- Special numbers spread round-robin → maximum coverage.
- Backtest measures **coverage and prize distribution**, not "expected ROI".

Result: with a bundle of tickets, achieved near-100% coverage of the main number space — doesn't increase EV (still negative-sum), but increases probability of at least 1 ticket winning a small prize and makes the experience more diverse.

### Direction C — Conscious Entertainment

UI/UX was rewritten with these principles:

- Plain, friendly local-language interface (not technical English).
- Removed jargon ("backtest", "ROI", "expected value" → plain phrases).
- Added clear disclaimer: "Lottery is random. This is entertainment, not a financial plan. Play responsibly."
- A "Save / track" feature for user-tracking suggestions over time — turning the project into a **conscious-play companion**, not a prediction tool.

→ **Lesson**: An **honest** pivot to entertainment that creates real value. A **fake** pivot (still selling "edge" with different words) is deception.

## Traps confronted

Per [03-fallacies.md](../method/03-fallacies.md):

- ✅ **Gambler's fallacy**: directly in long-absence and frequency strategies.
- ✅ **Hot hand**: in frequency-weighted strategies.
- ✅ **Texas sharpshooter**: pair-frequency, pattern strategies — finding patterns after looking at the data.
- ✅ **P-hacking / multiple comparison**: many strategies tried on the same dataset.
- ✅ **Confirmation bias**: initially focused on "beautiful" backtests over failed ones.
- ✅ **Goodhart drift**: backtest metric (number of wins) became target, not the real goal (entertainment with budget).

## Domain-specific lessons

For the lottery / random-number domain, add to the fallacy list:

- **"Patterns in random sequences look like real patterns"**: the human brain finds patterns even when none exist. Each pattern you "see" needs an out-of-sample test.
- **"Backtest improvement that doesn't survive"**: small backtest edges typically vanish when accounting for ticket cost, taxes, behavioral adherence.
- **"Coverage strategy is not edge"**: must be careful not to say/sell it as edge. It only changes the risk distribution, not the EV.

## Reusable patterns from the project

- **Walk-forward backtest framework**: pattern usable for any predictive strategy.
- **Adversarial reviewer agent**: generalized as [agents/rigor-reviewer.md](../agents/rigor-reviewer.md).
- **UX-first reframing pattern**: rewriting jargon → friendly language when pivoting from "tool with edge" to "conscious-entertainment tool". This pattern applies to any product whose positioning shifts.
- **This toolkit**.

## If starting over from scratch

With current understanding, would do differently:

1. **Pre-commit charter** before writing the first line of code — commit to "I will declare no edge when ..."
2. **Pre-specify number of strategies** — small fixed count, not unbounded exploration.
3. **Train/val/test split** from the start, test set touched only once at the end.
4. **Permutation test** early — before investing effort in tuning strategies.
5. **Pivot decision earlier**: right after the first failed validate phase, don't extend.
6. **Output is honest entertainment tool from the start** — not pretending predictive tool then pivoting.

The "failures" produced this toolkit — the methodology investment was not wasted.

## Connection to life

The pattern "invest in a favorite hypothesis → backtest looks pretty → reviewer finds errors → honest pivot to real value" applies to:

- **Personal investing**: believe in a system → backtest looks pretty → friction kills edge → pivot to index investing.
- **Productivity hack**: believe morning routine X is optimal → tracker shows no correlation → pivot to "consistency, not optimization".
- **Diet experiment**: believe keto/IF/X solves the problem → result not robust → pivot to "eat less junk + sleep enough".

In every case, **honest value after pivot > pretended value before pivot**.
