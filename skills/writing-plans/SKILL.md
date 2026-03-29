---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase and questionable taste. Document everything they need to know: which files to touch for each task, code, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI. TDD. Frequent commits.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** This should be run in a dedicated worktree (created by brainstorming skill).

**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- "Commit" - step

## Plan Document Header

**Every plan MUST start with a header. Use the appropriate track-specific header:**

### SW Plan Header (default)

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

### ML Experiment Plan Header

When the plan is for an ML experiment, use this header instead:

```markdown
# [Experiment Name] ML Experiment Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans or superpowers:subagent-driven-development to implement this plan.

**Hypothesis:** [one testable claim: "If X, then Y (measured by Z)"]

**Success criteria:** [metric thresholds that confirm the hypothesis]

**Failure criteria:** [what would reject the hypothesis]

**De-risk plan:** [scaled-down version completing in < 60 seconds]

**Scale-up plan:** [full run parameters after de-risk passes]

**Metrics to log:** [only what the hypothesis needs]

---
```

### DS Analysis Plan Header

When the plan is for a data science analysis, use this header instead:

```markdown
# [Analysis Name] Data Science Analysis Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans or superpowers:subagent-driven-development to implement this plan.

**Decision:** [what decision will this analysis inform? who makes it?]

**Lever:** [what action can be taken based on results?]

**Primary metric:** [outcome measure — is it proxy or true objective?]

**Counterfactual:** [what happens if we do nothing?]

**Constraints:** [data, time, compute, regulatory, ethical]

**Candidate approaches:** [3-5 selected from initial 8-12 screening]

---
```

## Task Structure

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

**Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

**Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

**Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
````

## ML Experiment Task Pattern

For ML experiment plans, use these task types instead of the TDD SW pattern:

````markdown
### Task 1: Set up experiment

**Step 1:** Create experiment directory with canonical structure:
`<experiment-name>/runs/`, `JOURNAL.md`

**Step 2:** Record in JOURNAL.md: hypothesis, success criteria, failure criteria, metrics to log, approach chosen (and alternatives considered)

**Step 3:** Commit
```bash
git commit -m "feat: scaffold <experiment-name> experiment"
```

### Task 2: De-risk loop

**Step 1:** Scale down — smaller data subset, fewer epochs, miniature model

**Step 2:** Run correctness checklist: dataset sanity → shape/dtype assertions → determinism check (fix seed, run tiny batch twice) → single-iteration test → overfit-a-small-batch

**Step 3:** Run de-risk script (target: < 60 seconds):
```bash
uv run train.py de-risk
```
Expected: run completes, loss decreases on overfit batch

**Step 4:** Record observations in JOURNAL.md

### Task 3: Full run

**Step 1:** After de-risk passes, scale up to full parameters

**Step 2:** Run (with custom timeout if > 2 minutes):
```bash
uv run train.py full
```

**Step 3:** Record results, anomalies, follow-ups in JOURNAL.md

### Task 4: Diagnostic plots and report

**Step 1:** Generate plots from logged data only — save as WebP in `runs/<run>/plots/`

**Step 2:** Write scientific report: Abstract, Introduction, Methods, Results, Discussion, Conclusion — every claim tied to a log file, table, or figure; no unsupported claims
````

## DS Analysis Task Pattern

For data science analysis plans, use these task types:

````markdown
### Task 1: Set up analysis session

**Step 1:** Create session folder:
`analysis/YYYY-MM-DDTHH-MM-SS-<slug>/plots/`, `scripts/`, `journal.md`

**Step 2:** Record in journal.md: problem framing (decision, lever, metric, counterfactual, constraints), candidate approaches selected

### Task 2: Inspect data shape

**Step 1:** Write and run shape-inspection script (PEP723, `uv run`):
```bash
uv run scripts/inspect_shape.py
```
Expected: column names, dtypes, row count, nulls

**Step 2:** Record `[SHAPE]` entry in journal.md

### Task 3: [Analytical step name]

**Step 1:** Confirm with user why this plot/table is needed (what decision it serves)

**Step 2:** Write script with PEP723 metadata; save plot as WebP:
```bash
uv run scripts/<step_name>.py
```

**Step 3:** Record `[PLOT]` / `[FINDING]` entry; suggest one logical next step

### Task 4: Report

**Step 1:** Scrutinize all tables and plots for inconsistencies; generate a diagnostic plot for every table; flag specific discrepancies

**Step 2:** Write report: Context/Question, Methods, Findings, Implications, Limitations, Next Steps

**Step 3:** Include statistical rigor: effect sizes + CIs, multiple comparisons correction, assumptions audit
````

## Remember
- Exact file paths always
- Complete code in plan (not "add validation")
- Exact commands with expected output
- Reference relevant skills with @ syntax
- DRY, YAGNI, TDD, frequent commits

## Execution Handoff

After saving the plan, offer execution choice:

**"Plan complete and saved to `docs/plans/<filename>.md`. Two execution options:**

**1. Subagent-Driven (this session)** - I dispatch fresh subagent per task, review between tasks, fast iteration

**2. Parallel Session (separate)** - Open new session with executing-plans, batch execution with checkpoints

**Which approach?"**

**If Subagent-Driven chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:subagent-driven-development
- Stay in this session
- Fresh subagent per task + code review

**If Parallel Session chosen:**
- Guide them to open new session in worktree
- **REQUIRED SUB-SKILL:** New session uses superpowers:executing-plans
