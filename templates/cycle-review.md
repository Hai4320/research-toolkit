# Cycle Review — Cycle [N] · [topic]

> Run this at every cycle close, regardless of outcome. Lighter than [postmortem.md](postmortem.md). Postmortem is reserved for cycles where deeper failure analysis is warranted (lessons painful enough to write at length).
>
> **Filed at**: `research/cycles/cycleN_<short-topic>-review.md` — sibling of the cycle's hypothesis card. Append the §7 closure record block to `research/RESEARCH_HISTORY.md` after filling in.

**Cycle ID**: N  
**Date opened**: YYYY-MM-DD  
**Date closed**: YYYY-MM-DD  
**Author**: ...  
**Charter**: [link to charter]  
**Pre-reg**: [link to hypothesis card / cycle proposal]

---

## 1. Cycle in one paragraph

> 3-5 sentences: what hypothesis, what method, what result.

```
[Summary]
```

## 2. Result vs. pre-commit criteria

> Quote each pre-commit success/kill criterion from the charter or hypothesis card. State whether it was met.

| Pre-committed criterion | Threshold | Observed | Met? |
|---|---|---|---|
| Effect size | ≥ X | ... | ✓/✗ |
| p-value (post-correction) | < α | ... | ✓/✗ |
| Robustness | All checks pass | ... | ✓/✗ |
| Out-of-sample | ≥ baseline + Y | ... | ✓/✗ |
| ... | ... | ... | ... |

## 3. Verdict

Tick exactly one:

- [ ] **Edge confirmed** — all gates passed → SHIP path (small-scale deploy + monitor)
- [ ] **Edge not confirmed** — fail-to-reject null → REFRAME (Direction A/B/C per [05-reframing-pivot.md](../method/05-reframing-pivot.md))
- [ ] **Inconclusive (underpowered)** — power < threshold → SHELVE with revisit trigger
- [ ] **Inconclusive (other)** — design flaw, data quality, etc. → fix and rerun
- [ ] **Pivoted** — changed direction mid-cycle → document the pivot, open new cycle
- [ ] **Archive** — no path forward → write [postmortem.md](postmortem.md)

**One-line rationale**: ...

## 4. Key insights (max 3)

> What is genuinely worth remembering from this cycle? Insights that change how you'd approach the next problem.

1. ...
2. ...
3. ...

## 5. Brief fallacy check

> Quick scan of [03-fallacies.md](../method/03-fallacies.md). Tick any that surfaced this cycle (even if you avoided them).

- [ ] Gambler's fallacy
- [ ] Hot hand
- [ ] Base rate neglect
- [ ] Survivorship / selection bias
- [ ] P-hacking / multiple comparison
- [ ] Look-ahead bias / leakage
- [ ] Regression to the mean
- [ ] Simpson's / Berkson's paradox
- [ ] Texas sharpshooter
- [ ] Goodhart's law
- [ ] Confirmation bias
- [ ] HARKing / optional stopping

For each tick, write 1 line on how it manifested and what countered it.

## 6. Carry-over

> Open threads to the next cycle — without these, history is lost.

- **Unresolved questions**: ... (route to next-cycle pre-reg)
- **Deferred items**: ... (specific specs/tasks not done this cycle)
- **Blocked items**: ... (need external input / data / time)
- **Tech debt incurred**: ... (track in [architecture-review.md](architecture-review.md))

## 7. Closure record (snippet for RESEARCH_HISTORY.md)

> Copy this block into the project's `RESEARCH_HISTORY.md` under "Cycles".

```markdown
### Cycle N — [topic] (status: closed YYYY-MM-DD)
- **Pre-reg**: [path]
- **Verdict**: [Edge confirmed / Not confirmed / Inconclusive / Pivoted / Archive]
- **Headline**: [1-line takeaway]
- **Review**: [path to this file]
- **Postmortem**: [path or "n/a"]
- **Carry-over to next cycle**: [1-line]
```

## 8. Next cycle handoff (optional)

If the next cycle is already mapped:

- **Proposed Cycle N+1 topic**: ...
- **Hypothesis seed**: ...
- **Pre-reg target date**: ...

Otherwise leave blank — open the planning conversation separately.

---

**Reviewed by**: ... (self / peer / [rigor-reviewer agent](../.claude/agents/rigor-reviewer.md))  
**Approved by**: ... (project owner)
