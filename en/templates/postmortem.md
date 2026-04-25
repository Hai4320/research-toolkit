# Postmortem — [Strategy / research name]

> A postmortem is an asset, not a shame. Purpose: future-you (and others) won't repeat this mistake, and will know how to leverage what was done well.

**Date**: YYYY-MM-DD  
**Author**: ...  
**Project duration**: ... → ...  
**Outcome**: [ ] Edge confirmed / [ ] Edge not confirmed / [ ] Inconclusive / [ ] Pivoted to ... / [ ] Archive

---

## 1. One-paragraph summary

> 3-5 sentences: what was done, what was found, what was the outcome.

```
[Summary]
```

## 2. Original hypothesis

> Quote verbatim from the original charter / hypothesis card.

```
[Original H1]
```

## 3. What was done

- Method: ...
- Data: ...
- Number of strategies / variants tried: ...
- Reviewer: ...
- Real time: ...

## 4. Result

- Effect size: ... (CI: ...)
- Baseline comparison: ...
- Robustness check: ...
- Out-of-sample: ...

## 5. Final decision

> Per the charter, which group does this result fall into in the pre-commit conclusions? Which pivot/ship/archive did you choose?

```
[Decision + reason]
```

## 6. Lessons — What went RIGHT

> Write even when the project failed. You may have: pre-committed well, didn't cheat, stopped at the right time, written this postmortem. These are worth keeping.

- ...
- ...

## 7. Lessons — What went WRONG / NEAR-WRONG

> Be honest. Warn future-you.

- **Cognitive mistake**: ... (e.g., didn't recognize this as a gambler's fallacy variant)
- **Methodology mistake**: ... (e.g., lookahead bias in feature X)
- **Decision mistake**: ... (e.g., extended project past kill criteria)
- **Psychological mistake**: ... (e.g., tried to salvage due to sunk cost)

## 8. Traps fallen into

> Cross-check with [03-fallacies.md](../method/03-fallacies.md). Which traps did you fall into (or nearly fall into) in this project?

- [ ] Gambler's fallacy
- [ ] Hot hand
- [ ] Base rate neglect
- [ ] Survivorship bias
- [ ] Selection bias
- [ ] P-hacking / multiple comparison
- [ ] Look-ahead bias / leakage
- [ ] Regression to the mean
- [ ] Simpson's paradox
- [ ] Texas sharpshooter
- [ ] Goodhart's law
- [ ] Confirmation bias
- [ ] Other: ...

For each checked trap, write a short paragraph: how the trap manifested in this project, and how you recognized it.

## 9. New traps (not on the list)

> If you discovered a domain-specific trap — add it to [03-fallacies.md](../method/03-fallacies.md).

- ...

## 10. Early warning signs ignored

> Were there early signs the project would fail that you ignored? Worth recognizing earlier next time.

- ...

## 11. Toolkit improvements

> Was there anything in this toolkit (template, methodology, fallacy list) that didn't catch the mistake? Propose specific improvements.

- File to update: ...
- Suggestion: ...

## 12. Reusable assets

> What output from this project can be reused?

- Code module: ...
- Dataset / data pipeline: ...
- Insight (even when hypothesis failed): ...
- Method worth scaling: ...

## 13. Open questions / Future work

> Given what we now know, what next question is worth pursuing? (Could be none — that's also a valid conclusion.)

- ...

---

**Signed**: ...  
**Reviewed by**: ...
