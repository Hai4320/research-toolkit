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

## Step 1 — Intent capture (single open question + inferred meta)

Ask the user **one open-ended question** (not a 4-question form):

> Tell me about your research — what it's about, why you're doing it, what success looks like, any constraints (time/budget/expertise/data), and any prior work I should know about.

User typically answers in 2-5 sentences, possibly partial. **Do NOT re-ask sub-questions one by one.** Parse what they said, then **infer** the rest with reasonable defaults.

### Build intent summary + inferred meta

Generate a 3-5 sentence written summary of intent. Then **infer four meta-attributes** with explicit reasoning:

| Inferred | Default reasoning rules |
|---|---|
| `SCALE` ∈ {Small, Medium, Large} | Small if "personal experiment" / "weekend" / "self-test"; Large if multi-stakeholder / 6+ months / production stakes; Medium otherwise |
| `MODE` ∈ {Greenfield, Retrofit} | Retrofit only if user mentions "existing project / channel / codebase / prior cycles". Default Greenfield |
| `CONSTRAINTS` (time, budget, expertise) | Time default 5-10h/week if not stated; budget bootstrap; expertise infer from intent ("first time" = beginner, etc.) |
| `LAYERS` (Single / Multi) | Multi if intent mentions distinct concerns (vd "research X AND build product AND grow audience"). Single otherwise |

### State back + offer override

Show user this format:

```
Heard:
[3-5 sentence intent summary]

Inferred meta (override anything wrong):
• Scale: [Small/Medium/Large] — because [reason]
• Mode: [Greenfield/Retrofit] — because [reason]
• Time: ~[X]h/week — [stated/inferred]
• Expertise: [beginner/intermediate/expert in domain] — [stated/inferred]
• Layers: [Single / Multi (list layers)] — [reason]

Anything wrong? Otherwise I'll continue.
```

User can override any field with one short message. **Do not iterate this step more than 2 turns** — if user pushes back twice, just accept whatever they say.

**Branch behavior** based on inferred meta (apply silently in subsequent steps):
- `SCALE=Small`: skip Step 5 (architecture); Step 9 translates `templates/` only.
- `SCALE=Large`: add stakeholder section to charter; recommend `architecture-review.md` cadence (every 4 cycles).
- `MODE=Retrofit`: skip Step 5; Step 6 becomes "audit existing state" cycle using `templates/architecture-review.md`.
- `LAYERS=Multi`: Step 4 recommend per-layer methodology, not single global.

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

### Step 6.0 — Choose Cycle 0 goal explicitly (NEW v2)

Before drafting the plan, ask the user which Cycle 0 goal they want:

> What's the goal of Cycle 0?
> - **(a) Infra-only setup** — environment, dependencies, scaffolding. NO shippable artifact. End-state: pipeline can run a smoke test, but no real product output. (Best when: pure research, complex backend, no audience pressure yet.)
> - **(b) MVP cycle** — produce a real shippable artifact end-to-end (even if rough), so you can self-evaluate quality before Cycle 1. (Best when: product/audience-driven projects, when you need to feel the pipeline working before iterating.)

**Default heuristic** (state your guess + ask user to confirm):
- `LAYERS=Multi` OR product/audience-driven intent (BMC/DBR archetypes) → default **(b) MVP cycle**.
- Pure research / complex theoretical project → default **(a) infra-only**.
- `MODE=Retrofit` → default **(audit)** — the variant of (a) for existing work.

Record as `CYCLE0_GOAL` ∈ {infra, mvp, audit}.

**Implication for the plan** (Step 6.1 below):
- `infra`: Cycle 0 deliverable = "pipeline executes smoke test on sample input, no artifact for review".
- `mvp`: Cycle 0 deliverable = "1 complete artifact (vd 1 short video, 1 working prediction, 1 prototype) suitable for self-quality eval. NOT public release — private review only."
- `audit`: Cycle 0 deliverable = "filled `architecture-review.md` documenting existing state, identifying gaps for Cycle 1+."

### Step 6.1 — Generate plan dimensions

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
🧭 Methodology: [Falsificationist / ... (per layer if Multi)]
📏 Scale: [Small / Medium / Large] (inferred)
🆕 Mode: [Greenfield / Retrofit] (inferred)
🎯 Cycle 0 goal: [infra-only / mvp / audit]

📋 Next steps:
1. Review + approve DRAFT files
2. Begin Cycle 0 — execute per cycle00_setup.md
3. When Cycle 0 closes, write cycle-review.md and update RESEARCH_HISTORY.md, begin Cycle 1

🔁 To run cycle review or next-cycle planning, use the `research-cycle` skill (not yet installed).
```

---

## Anti-patterns to avoid

- ❌ Generating all files in one shot without user confirmation
- ❌ Picking methodology without showing comparison
- ❌ Using "Falsificationist" for inherently audience-driven product work (use BMC or hybrid + warn user)
- ❌ Letting user skip Cycle 0 and dive into Cycle 1 — Cycle 0 catches reproducibility bugs early (exception: SCALE=Small with explicit user override)
- ❌ Forgetting RESEARCH_HISTORY.md — without it, project loses cycle memory
- ❌ Modifying canonical English toolkit docs to translate — translation goes into the **user's project** at `research/methodology/`, not into the toolkit repo
- ❌ Translating code blocks, file paths, or technical terms (i.i.d., p-value, etc.) — keep English
- ❌ Asking for target language AFTER methodology choice — language is Step 0, before everything
- ❌ Forcing Medium/Large flow on a Small personal experiment — calibrate by SCALE
- ❌ Dropping all steps into one message — walk through one step at a time
- ❌ Re-asking sub-questions after user gave open-ended intent in Step 1 — INFER, state defaults, offer override
- ❌ Defaulting Cycle 0 to "no shippable artifact" for product/audience-driven projects — ask explicitly per Step 6.0
- ❌ Skipping the `LAYERS=Multi` recognition — per-layer methodology recommendation matters when intent has distinct concerns

## When to hand off

- User approves all generated files and starts Cycle 0 → skill done.
- User wants to skip steps and go straight to coding → warn once, then defer.
- User's intent is genuinely off-toolkit (e.g., pure engineering with no research component) → recommend they don't use the research-toolkit; suggest standard project init instead.
