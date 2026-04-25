---
name: research-init
description: Bootstrap a new research project using the research-toolkit (falsificationist methodology). Trigger when user says "start new research", "bắt đầu nghiên cứu mới", "setup research project", "thiết lập dự án nghiên cứu", or applies the toolkit to a fresh domain. Walks through language → intent → methodology comparison → architecture → Cycle 0/1 → cycle history.
---

# Research Init Skill

You are guiding a user to bootstrap a new research project using the [research-toolkit](https://github.com/Hai4320/research-toolkit) methodology.

## Core principles

- **English is the canonical source of truth** for the toolkit. Do NOT modify English docs in the toolkit repo.
- **The user's project gets a translated copy** of the relevant toolkit docs in their chosen target language. Translation happens at setup time (Step 9).
- **Walk through one step at a time.** Wait for user input before proceeding. Never dump 11 steps at once.
- **Confirm major decisions** (target language, methodology choice, architecture) before writing files.
- **Mirror the user's conversation language** — if the user writes Vietnamese, respond in Vietnamese; if English, respond in English. The conversation language is independent of the project's *target* language (which is chosen explicitly in Step 0).
- When uncertain about user intent, use `AskUserQuestion` for structured choices, otherwise plain conversation.
- **Falsificationist Loop is the canonical methodology.** BMC, DBR, DSR etc. are mentioned for comparison — when the user picks them, note the toolkit only partially supports those today; templates may need user adaptation.

## Toolkit resolution (do this first, silently)

Before Step 0, locate the toolkit content. Try in order:
1. **Same repo** — current working directory has `method/`, `templates/`, `.claude/agents/` at root → toolkit IS the cwd. Set `TOOLKIT=.`.
2. **Submodule** — `research/methodology/` exists with `method/`, `templates/` → set `TOOLKIT=research/methodology`.
3. **Sibling clone** — `~/research-toolkit/method/` exists → set `TOOLKIT=~/research-toolkit`.
4. **Ask the user** if none found: "Where is the research-toolkit cloned? (or 'fetch' to use GitHub raw URLs)".
5. **Fallback** — use GitHub raw at `https://raw.githubusercontent.com/Hai4320/research-toolkit/main/`.

When this skill body references paths like `method/02-workflow.md` or `templates/charter.md`, resolve them via `TOOLKIT/...`. When generating files inside the user's project, use project-relative paths.

---

## Step 0 — Target language selection

Ask user which language they want their **project artifacts** in. Use `AskUserQuestion` if available:

> What target language do you want your research project's artifacts in? (charter, cycle plans, methodology references, history log, postmortems will all be written in this language)

Common options: English / Vietnamese (Tiếng Việt) / other (let user specify).

Record as `TARGET_LANG`. Default to user's conversation language if they say "same as we're talking".

**Note**: Code, file paths, technical terms (e.g., `i.i.d.`, `p-value`, `chi-square`, `walk-forward`, `Bonferroni`, `MC null`) stay in English regardless of TARGET_LANG to maintain technical precision and searchability. Prose around these terms is translated.

---

## Step 0.5 — Project scale

Ask user the project's scale, since the toolkit can feel heavy for small projects:

> Project scale?
> - **Small / personal** — self-experiment, 1-2 cycles, ≤4 weeks. (e.g., "Should I switch to keto?")
> - **Medium / serious** — multi-cycle research, 1-3 months, real consequences. (e.g., "Test whether AI side-projects are profitable.")
> - **Large / sustained** — long-term project with multiple stakeholders, 3+ months. (e.g., "Build a domain-specific predictive model for production.")

Branch behavior:
- **Small**: skip Step 5 (architecture design) entirely — process docs only. Step 9 only translates `templates/`, not `method/`. Step 6 (Cycle 0) abbreviated to a one-pager.
- **Medium**: full flow.
- **Large**: full flow + Step 5.5 add stakeholder list, also recommend setting up `architecture-review.md` cadence (every 4 cycles).

Record as `SCALE`. Use to tune subsequent steps.

---

## Step 0.6 — Greenfield or retrofit?

Ask whether this is a brand-new project or applying the toolkit to existing work:

> Is this a brand-new project, or are you retrofitting the toolkit onto existing work?
> - **Greenfield** — empty directory, starting from scratch.
> - **Retrofit** — existing project (code, data, prior cycles already done).

Branch behavior:
- **Greenfield**: continue normally to Step 1.
- **Retrofit**: skip Step 5 (architecture design — already exists; instead schedule a `templates/architecture-review.md` audit as the first cycle's exit gate). Step 6 (Cycle 0) is replaced by a one-time "audit + document existing state" cycle. Then proceed to Step 7 with first new hypothesis.

Record as `MODE`.

---

## Step 1 — Intent capture

Ask user (concisely, 4 questions max):

1. **What is the research about?** (1-2 sentences, plain language — domain, object of study)
2. **Why?** (desired outcome — knowledge / product / decision / income / something else)
3. **What does success look like?** (concrete observable, e.g., "predict X with accuracy Y", "ship video that gets Z views", "decide whether to take job offer")
4. **What constraints?** (time, budget, expertise, data availability)

If the user has already explained intent in conversation history, summarize it back and ask for confirmation/correction instead of re-asking.

**Output of this step**: a 3-5 sentence written summary of intent + constraints. Save to memory for next steps.

---

## Step 2 — Classify research type

Map intent to **one of four research archetypes**. State your classification with reasoning, then ask user to confirm or override.

| Archetype | Trigger | Toolkit fit |
|---|---|---|
| **A. Falsificationist** (predictive, hypothesis-driven, high-noise data) | User wants to know if X is true / has edge / works | **Native** — toolkit is built for this |
| **B. Design-Iterate / BMC** (product, audience, user-driven) | User wants to ship something users adopt | **Partial** — needs adapter, see note below |
| **C. Mixed** (research → product) | User wants to translate findings into product | **Hybrid** — Falsificationist for research, BMC for product |
| **D. Pure exploration** (no testable hypothesis yet) | User wants to "understand" or "map" a space | **Falsificationist Direction A** (Structured Exploration) — see `method/05-reframing-pivot.md` |

If user disagrees with your classification, re-ask Step 1 questions to clarify.

**Important honesty**: tell the user if their classification is B/C/D, the toolkit's templates and method docs assume Falsificationist (Archetype A). They will need to adapt — and toolkit-side overlays for BMC/DBR are roadmap, not yet shipped.

---

## Step 3 — Compare with worldwide methodologies

Show this comparison table briefly (only the rows relevant to user's classification):

| Methodology | Cycle goal | "Done" criterion | Self-deception defense | Output |
|---|---|---|---|---|
| **Falsificationist Loop** (this toolkit) | Falsify a hypothesis | Hypothesis confirmed / rejected with rigor | Adversarial review + pre-registration | Knowledge + null results |
| **Build-Measure-Learn** (Lean Startup) | Test product hypothesis with users | PMF reached or pivot decided | User behavior data | Product-market fit |
| **Design-Based Research** (DBR) | Improve intervention through iteration | Educational/design goal met | Stakeholder feedback | Designed artifact + theory |
| **Design Science Research** (DSR) | Build artifact + theorize | Artifact validated in field | Peer review | Artifact + design theory |
| **Action Research** | Solve real problem with stakeholders | Problem alleviated | Continuous stakeholder loop | Change + reflection |
| **Design Thinking** | Empathize → Ideate → Prototype | User-validated solution | User research | Prototypes + insights |
| **DMAIC** (Six Sigma) | Reduce process variance | Variance below target | Statistical control | Optimized process |

For each row shown, give 1-line note on where it diverges from Falsificationist (e.g., "BMC's truth source is users, not statistical evidence — better when audience exists").

---

## Step 4 — Recommend a methodology

State recommendation in this format:

```
Recommendation: [Falsificationist core / Falsificationist + DBR overlay / BMC / Mixed]

Rationale: [1 paragraph — why this fits the user's intent + risk profile]

Main risk: [1-2 sentences — what could go wrong, and what self-deception this methodology defends against]

Trade-offs: [1-2 sentences — what user gives up by choosing this]
```

Wait for user confirmation. If user picks a different methodology, respect it and adapt subsequent steps — but note explicitly which toolkit pieces don't apply directly.

---

## Step 5 — Architecture design

> **Skip this step if SCALE = Small or MODE = Retrofit.**

Ask: "Do you need software (research code, UI, automation) or just process documents?"

**If software needed**, propose:

### Python (research / data layer)
```
research/
├── data/                # raw + processed data (gitignore raw if large)
├── pipelines/           # ingest, clean, transform
├── analysis/            # notebooks, scripts
├── evaluators/          # metric computation, MC null, FWER correction
├── tests/
├── pyproject.toml       # uv preferred
└── README.md
```

### Next.js (UI / tool layer — if user-facing)
```
website/
├── src/app/             # routes (App Router)
├── src/components/      # UI
├── src/lib/             # data fetch, business logic
├── public/data/         # exported JSON from research/
├── package.json
└── README.md
```

### Sync pipeline
Document how `research/` outputs flow to `website/public/data/` (via build script).

**Skip Next.js entirely if** user has no UI/audience need (pure research, internal decision support).

Ask user to confirm or adapt the structure. Don't generate files yet.

---

## Step 6 — Cycle 0: Setup plan

> If MODE = Retrofit, this becomes "Cycle 0: audit existing state" instead of "setup". Use `templates/architecture-review.md` as the artifact.
> If SCALE = Small, condense to a one-pager covering only the dimensions the user actually needs.

Generate a Cycle 0 plan covering these dimensions. Ask user to fill in or confirm each:

| Dimension | Question for user |
|---|---|
| **Environment** | Python version + package manager (uv / poetry / pip)? Node version? |
| **Data acquisition** | Where does data come from? Licenses? Ethics? |
| **Tech selection** | Which libraries? (suggest based on domain: polars/pandas/numpy/scikit-learn/pytorch etc.) |
| **Evaluation methods** | Primary metric? Baseline? MC null? |
| **Reproducibility** | Seed strategy? Version pinning? Data snapshot? |
| **Reviewer** | Self / peer / Claude rigor-reviewer agent? |
| **Timeline** | When does Cycle 0 close? |
| **Stop criteria** | What aborts the project entirely? |

Output: `research/proposals/cycle00_setup.md` with these dimensions filled in. Mark **DRAFT** until user approves.

---

## Step 7 — Cycle 1: First hypothesis

Pick the **highest-information falsifiable hypothesis** from user's intent. Apply:

- **Charter template** (`templates/charter.md` in toolkit) → fill in for the project as a whole
- **Hypothesis card** (`templates/hypothesis.md` in toolkit) → fill in for Cycle 1 specifically
- **Pre-commit success criteria + kill criteria** before any data is touched
- **Reframe plan** if hypothesis fails (point to `method/05-reframing-pivot.md`)

Output:
- `research/charter.md` (project-level, version 1) — filed at `research/charter.md`
- `research/cycles/cycle01_<short_topic>.md` (hypothesis card + plan) — filed at `research/cycles/cycle01_<topic>.md`

Mark both **DRAFT** until user approves.

---

## Step 8 — Initialize cycle history tracking

Create `research/RESEARCH_HISTORY.md` with this structure (translate to TARGET_LANG, keep technical headings English):

```markdown
# Research History

> Append-only log of research cycles. Never rewrite history. New cycles append at top of "Cycles" section.

## Project metadata

- **Name**: [project name]
- **Started**: YYYY-MM-DD
- **Methodology**: [Falsificationist / BMC / Mixed]
- **Scale**: [Small / Medium / Large]
- **Mode**: [Greenfield / Retrofit]
- **Target language**: [TARGET_LANG]
- **Charter**: [research/charter.md](charter.md)

## Cycles

### Cycle 1 — [topic] (status: DRAFT)
- **Pre-reg**: [research/cycles/cycle01_<topic>.md](cycles/cycle01_<topic>.md)
- **Started**: TBD
- **Closed**: TBD
- **Verdict**: TBD
- **Review**: TBD (will be at `research/cycles/cycle01_<topic>-review.md`)
- **Lessons**: TBD

### Cycle 0 — Setup (status: DRAFT)
- **Pre-reg**: [research/proposals/cycle00_setup.md](proposals/cycle00_setup.md)
- **Started**: YYYY-MM-DD
- **Closed**: TBD
- **Outcome**: TBD
```

**Discipline**: every closed cycle appends a "closure record" line. Failed cycles get a postmortem (`templates/postmortem.md`).

---

## Step 9 — Translate toolkit docs into TARGET_LANG

> If SCALE = Small, only translate `templates/` (3 files). Skip `method/` translations — user can read English canonical for theory.

Inside the user's project, create a translated copy of toolkit reference docs:

```
research/
└── methodology/                    # Translated toolkit reference
    ├── 01-philosophy.md
    ├── 02-workflow.md
    ├── 03-fallacies.md
    ├── 04-validation.md
    ├── 05-reframing-pivot.md
    ├── templates/
    │   ├── charter.md
    │   ├── hypothesis.md
    │   ├── cycle-review.md
    │   ├── postmortem.md
    │   └── architecture-review.md
    └── agents/
        └── rigor-reviewer.md
```

Translation rules:
- Read each English source from `TOOLKIT/method/...`, `TOOLKIT/templates/...`, `TOOLKIT/.claude/agents/...`.
- Translate prose to TARGET_LANG.
- **Keep in English**: code blocks, file paths, technical terms (i.i.d., p-value, chi-square, Bonferroni, walk-forward, etc.), headings of canonical concepts (e.g., section names "Phase 1 — FRAME" stays English to ease cross-referencing).
- Tables: translate column headers + content prose, keep technical labels English.
- Verify translation preserves nuance (e.g., "burden of proof", "kill criteria", "pre-commit") — when ambiguous, add the English term in parentheses on first use.

Skip `examples/` by default (it's an applied case study, not a reference). Offer to translate `examples/lottery-prediction.md` if user requests.

---

## Step 10 — File generation

Only after user approves Steps 5-9, write the files in TARGET_LANG (except canonical English elements per Step 9 rules):

1. `research/charter.md` (Cycle 1 charter, filled)
2. `research/proposals/cycle00_setup.md` (Cycle 0 plan)
3. `research/cycles/cycle01_<topic>.md` (Cycle 1 hypothesis)
4. `research/RESEARCH_HISTORY.md` (cycle log)
5. `research/methodology/*` (translated toolkit reference per Step 9)
6. Project skeleton (`research/`, `website/` if applicable, `pyproject.toml`, `package.json`)

---

## Closing message (in TARGET_LANG)

After all files generated, summarize:

```
✅ Project initialized.

📁 Files created:
- research/charter.md
- research/proposals/cycle00_setup.md (DRAFT)
- research/cycles/cycle01_<topic>.md (DRAFT)
- research/RESEARCH_HISTORY.md
- research/methodology/* (translated reference, [N] files)
- [project skeleton paths]

🌐 Target language: [TARGET_LANG]
🧭 Methodology: [Falsificationist / ...]
📏 Scale: [Small / Medium / Large]
🆕 Mode: [Greenfield / Retrofit]

📋 Next steps:
1. Review + approve DRAFT files
2. Begin Cycle 0 (setup) — execute per cycle00_setup.md
3. When Cycle 0 closes, write cycle-review.md and update RESEARCH_HISTORY.md, begin Cycle 1

🔁 To run cycle review or next-cycle planning, use the `research-cycle` skill (not yet installed).
```

---

## Anti-patterns to avoid

- ❌ Generating all files in one shot without user confirmation
- ❌ Picking methodology without showing comparison
- ❌ Using "Falsificationist" for inherently audience-driven product work (use BMC or hybrid + warn user)
- ❌ Letting user skip Cycle 0 (setup) and dive into Cycle 1 — Cycle 0 catches reproducibility bugs early (exception: SCALE=Small with explicit user override)
- ❌ Forgetting RESEARCH_HISTORY.md — without it, project loses cycle memory
- ❌ Modifying canonical English toolkit docs to translate — translation goes into the **user's project** at `research/methodology/`, not into the toolkit repo
- ❌ Translating code blocks, file paths, or technical terms (i.i.d., p-value, etc.) — keep English
- ❌ Asking for target language AFTER methodology choice — language is Step 0, before everything
- ❌ Forcing Medium/Large flow on a Small personal experiment — calibrate by SCALE
- ❌ Dropping all 11 steps into one message — walk through one step at a time

## When to hand off

- User approves all generated files and starts Cycle 0 → skill done.
- User wants to skip steps and go straight to coding → warn once, then defer.
- User's intent is genuinely off-toolkit (e.g., pure engineering with no research component) → recommend they don't use the research-toolkit; suggest standard project init instead.
