# Architecture Review — [Project name] · v[N]

> Periodic audit of the project's overall architecture. Run at milestone boundaries (every N cycles, every quarter, or before any major refactor). Independent of single-cycle reviews.
>
> Goal: catch drift between original goals and current state, surface technical debt, validate that methodology is still structurally enforced in code.

**Review date**: YYYY-MM-DD  
**Review version**: N  
**Last review**: [link or "first review"]  
**Reviewer(s)**: ...  
**Project state at review**: cycle [N] closed, [N+1] in progress

---

## 1. Inventory

### 1.1 Components

| Component | Path | Purpose | Status |
|---|---|---|---|
| Research/data | `research/` | ... | ✓ healthy / ⚠ debt / ✗ broken |
| UI/tool | `website/` | ... | ... |
| Methodology docs | `research/methodology/` | ... | ... |
| Cycle history | `research/RESEARCH_HISTORY.md` | ... | ... |
| ... | ... | ... | ... |

### 1.2 Tech stack

| Layer | Stack | Pinned version | Last updated |
|---|---|---|---|
| Python | uv + polars + ... | ... | ... |
| Node | Next.js + ... | ... | ... |
| Data store | JSONL / Parquet / DB | ... | ... |
| CI / hooks | ... | ... | ... |

### 1.3 Data flow diagram

> Sketch (text or link to image). At minimum: data source → ingest → process → analysis → (optional) export to UI.

```
[data source] → [ingest] → [clean] → [analysis] → [evaluator] → [history log]
                                                            ↘ [website public/data/]
```

---

## 2. Goal alignment

### 2.1 Original goals (from charter)

> Quote the charter's success criteria + scope.

```
[Charter goals]
```

### 2.2 Current state vs. original

| Goal | Original | Current | Drift? |
|---|---|---|---|
| ... | ... | ... | none / minor / major |

### 2.3 Drift analysis

If drift exists:
- **Cause**: ... (scope creep / new constraint / discovery during cycles / forgotten goal)
- **Action**: realign / accept and re-document / pivot

---

## 3. Methodology integration check

> Does the code structurally enforce the methodology, or is it left as discipline?

| Gate | Method file | Code enforcement | Status |
|---|---|---|---|
| Pre-commit charter | `templates/charter.md` | Charter required before code? | ✓/✗ |
| Walk-forward backtest | `method/04-validation.md` | Time-aware split enforced in code? | ✓/✗ |
| Multiple-comparison correction | `method/04-validation.md` | FWER applied in evaluator? | ✓/✗ |
| MC null baseline | `method/04-validation.md` | MC null library exists + used? | ✓/✗ |
| Adversarial review | `agents/rigor-reviewer.md` | Run before each cycle close? | ✓/✗ |
| Cycle history append-only | (skill) | Discipline only / hooks enforce? | ✓/✗ |

**Gates failing**: ... (these become refactor priorities)

---

## 4. Technical debt inventory

> List items with cost estimate and risk.

| ID | Description | Origin | Cost to fix | Risk if not fixed |
|---|---|---|---|---|
| TD-1 | ... | Cycle X | ~Yh | ... |
| TD-2 | ... | ... | ... | ... |

Group by severity: **Critical** (blocks correctness) / **High** (blocks next cycle) / **Medium** / **Low**.

---

## 5. Code health

| Metric | Target | Current | Action |
|---|---|---|---|
| Test coverage | ≥ X% | ... | ... |
| Type coverage (mypy / ts) | strict | ... | ... |
| Lint passing | 100% | ... | ... |
| Doc freshness | every cycle close | ... | ... |
| Reproducibility (re-run produces same output) | yes | ... | ... |

---

## 6. Anti-pattern scan

Tick any that apply, with 1-line evidence:

- [ ] **Goodhart drift** — metric optimized has drifted from real goal
- [ ] **Over-engineering** — abstraction layers without 3+ users
- [ ] **Premature optimization** — perf work before correctness validated
- [ ] **Dead code** — modules untouched for 3+ cycles
- [ ] **Documentation rot** — docs reference functions that no longer exist
- [ ] **Implicit dependencies** — code assumes data shape not enforced
- [ ] **Sneaking complexity** — small additions accumulate without review
- [ ] **Test theater** — tests exist but don't catch real failures
- [ ] **Reproducibility erosion** — seeds, snapshots, version pins drifted
- [ ] **Methodology bypass** — gates documented but not run

---

## 7. Risk register

| ID | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R-1 | Data source could change schema | M | H | Add schema validation + alerting |
| R-2 | ... | ... | ... | ... |

---

## 8. Decisions

### 8.1 Keep
- Components/patterns that are working — note them so they don't get refactored away by accident.

### 8.2 Refactor
- Components needing rewrite. List with target shape + rough cost.

### 8.3 Migrate
- Components moving to a different library/approach. List migration plan.

### 8.4 Prune
- Components/files to delete. Verify no callers via `grep`.

### 8.5 Defer
- Items recognized but not addressed this round. Set revisit trigger.

---

## 9. Action items (prioritized)

| # | Action | Owner | Target cycle | Linked TD |
|---|---|---|---|---|
| 1 | ... | ... | Cycle N+1 | TD-1 |
| 2 | ... | ... | Cycle N+2 | TD-3 |

---

## 10. Documentation hygiene check

> Per the toolkit principle "research process is always fully documented". Verify:

- [ ] Every closed cycle has a [cycle-review.md](cycle-review.md) entry
- [ ] All postmortems linked from `RESEARCH_HISTORY.md`
- [ ] Pre-regs (charter + hypothesis cards) match what was actually executed
- [ ] Methodology docs in `research/methodology/` still match toolkit canonical
- [ ] No undocumented refactors / silent rewrites of past decisions
- [ ] Architecture diagrams (if any) reflect current code layout

For each unticked item, file an action item in §9.

---

## 11. Next review trigger

Pre-commit when this review will run again (don't leave open-ended):

- [ ] After cycle N+M closes
- [ ] On YYYY-MM-DD (calendar trigger)
- [ ] Before any major refactor / dependency upgrade
- [ ] Before public release

---

**Approved by**: ... (project owner)  
**Filed at**: `research/architecture-reviews/YYYY-MM-DD-v[N].md`
