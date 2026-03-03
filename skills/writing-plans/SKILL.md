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

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:ml-experimentation to execute this plan.

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

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:scientific-eda to execute this plan.

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

**If ML experiment:**
- **REQUIRED SUB-SKILL:** Use superpowers:ml-experimentation
- Execute the experiment lifecycle: de-risk → scripts → logging → journal → plots → report

**If DS analysis:**
- **REQUIRED SUB-SKILL:** Use superpowers:scientific-eda
- Execute the analysis: context → session setup → journal → shape → human-guided exploration → report
