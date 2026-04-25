# Research Toolkit — Standardized Research Methodology

> **English is the canonical language of this toolkit.** When bootstrapping a new research project via the [`research-init`](.claude/skills/research-init.md) skill, you can choose any target language for your project's artifacts and the relevant docs will be translated into your chosen language at setup time.

This repository distills a research process from a high-randomness prediction project into a set of documents and templates that can be reused for **any field** involving: prediction, decision-making under uncertainty, hypothesis testing, or evaluating a strategy before betting money / time / reputation on it.

## When to use this toolkit

Use it when your work has **any** of these traits:

- You have a **hypothesis you want to be true** (e.g., "this sleep style is better", "this investment strategy works", "this learning method is faster").
- You plan to make decisions based on historical data (backtest, personal A/B test, product experiment).
- There is **commercial / emotional pressure** pushing you toward a specific conclusion.
- High noise level, small signals, easy to "see" patterns that aren't there.
- Mistakes have real consequences: money, health, time, reputation.

You do NOT need this when: the work is fully deterministic, already well-validated, or risk is so low that "just try it" is cheaper than research.

## Folder structure

```
research-toolkit/
├── README.md                    # This file
├── method/
│   ├── 01-philosophy.md         # Core mindset — entropy is the default
│   ├── 02-workflow.md           # The 5-phase process
│   ├── 03-fallacies.md          # 12 classic cognitive traps
│   ├── 04-validation.md         # How to validate properly
│   └── 05-reframing-pivot.md    # When prediction fails — how to pivot
├── templates/
│   ├── charter.md               # Open a project: question + kill criteria
│   ├── hypothesis.md            # Card to test 1 hypothesis
│   ├── cycle-review.md          # Neutral end-of-cycle review (every cycle)
│   ├── postmortem.md            # Deeper failure analysis (when warranted)
│   └── architecture-review.md   # Periodic project audit (drift, debt, methodology)
├── agents/
│   └── rigor-reviewer.md        # Domain-agnostic reviewer agent
├── case-study/
│   └── lottery-prediction.md    # Anonymized applied example
└── .claude/
    └── skills/
        └── research-init.md     # Bootstrap a new project (interactive, multilingual)
```

## Two ways to use the toolkit

### A. Manual (read & adapt)

1. **Before starting** a research effort: copy [templates/charter.md](templates/charter.md) → fill it in → commit to kill criteria.
2. **Per hypothesis**: use [templates/hypothesis.md](templates/hypothesis.md), one card per hypothesis.
3. **When you catch yourself wanting to believe**: re-read [method/03-fallacies.md](method/03-fallacies.md), self-check.
4. **Before publishing / shipping to production**: run it through [agents/rigor-reviewer.md](agents/rigor-reviewer.md) — or have a person (or Claude with this prompt) review.
5. **When a strategy fails**: write [templates/postmortem.md](templates/postmortem.md). It is an asset — not wasted failure.
6. **When you hit "this can't be predicted"**: [method/05-reframing-pivot.md](method/05-reframing-pivot.md) shows how to pivot.

### B. Guided (Claude Code skill)

If you have Claude Code, run the `research-init` skill:

```
> bắt đầu nghiên cứu mới        # in your project directory
> start a new research project
```

The skill will:
1. Ask your **target language** (English, Vietnamese, or any other) — toolkit docs get translated into your chosen language for your project.
2. Capture intent, classify research type, compare against worldwide methodologies, recommend a fit.
3. Design Python research + (optional) Next.js UI architecture.
4. Generate Cycle 0 (setup) + Cycle 1 (first hypothesis) plans.
5. Initialize append-only cycle history tracking.

To install the skill into a fresh project, copy `.claude/skills/research-init.md` into your project's `.claude/skills/` directory.

## Philosophy in one line

> **The default is no edge. You must prove edge exists. If you cannot, be honest about it — and pivot to a different value that is real.**

## Applying to life

This toolkit was distilled from a lottery problem (extremely random, easy to deceive yourself). Precisely for that reason it transfers well to:

- **Personal investing**: trading strategies, fund selection, market timing.
- **Health & lifestyle**: diet / sleep / exercise experiments, reasoning from personal trackers.
- **Career**: choice of profession, pivoting, evaluating a startup opportunity.
- **Learning**: study methods, productivity tools, "brain biohacking".
- **Product**: A/B test on small samples, retention, growth experiments.

Domains differ, but the cognitive traps (gambler's fallacy, survivorship bias, p-hacking…) are **invariant**.

## Living with it

This is not a read-once document. Each time you:
- Finish a research effort → add to `case-study/` with new lessons (or extend your project's local `cycles/` log).
- Discover a trap not yet in `03-fallacies.md` → add it.
- Find a better template → improve the template.

The more you use it, the sharper it gets.

## License

MIT — see [LICENSE](LICENSE).
