# Hypothesis Card — [Short hypothesis name]

> One card per hypothesis. Must be falsifiable: you must know which result will make you abandon it.

**ID**: H_xxx  
**Date created**: YYYY-MM-DD  
**Charter link**: [link]

---

## 1. Hypothesis statement

> A single sentence, a proposition that can be true or false (not a question, not a wish).

```
[H1: ...]
```

**Opposing null (H0)**:
```
[H0: ...]
```

## 2. Basis

> Why are you proposing this hypothesis? What mechanism (physical, economic, behavioral) makes you suspect it's true?

```
[Reason to believe / disbelieve]
```

> If the only reason is "I feel like it works" or "in the first N observations there seems to be a pattern" — this is a weak hypothesis, note it upfront.

## 3. Observable predictions

### If hypothesis is TRUE, I expect to observe:

- ...
- ...

### If hypothesis is FALSE, I expect to observe:

- ...
- ...

> The two lists must clearly differ. If the same observation fits both — the hypothesis is not falsifiable.

## 4. Specific test

- **Statistic used**: ...
- **Null distribution**: ...
- **Threshold to reject H0**: p < ... (post-correction if applicable)
- **Minimum effect size worth caring about**: ...
- **Required sample size (power analysis)**: ...

## 5. Confounders / Exclusion

> If the result "supports H1", what alternative explanations remain?

- Confounder 1: ... → controlled by: ...
- Confounder 2: ... → controlled by: ...
- Reverse causation: ... → ruled out by: ...
- Selection effect: ... → ruled out by: ...

## 6. Pre-commit Result

> Before looking at validation/test data, write: what will you conclude with each outcome?

| Outcome | Conclusion |
|---|---|
| Reject H0, large effect, robust | ... |
| Reject H0, small effect | ... |
| Fail to reject H0 | ... |
| Inconclusive | ... |

## 7. Run notes

> Update after running. DO NOT edit sections above after results are in.

- **Run date**: 
- **Raw result**: 
- **Diagnostic checks pass?**: 
- **Conclusion** (per pre-commit table): 
- **Lesson**: 
