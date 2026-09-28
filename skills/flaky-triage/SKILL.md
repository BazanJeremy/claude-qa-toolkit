---
name: flaky-triage
description: This skill should be used when the user asks to "triage flaky tests", "analyze test instability", "which tests are flaky", "why does this test fail intermittently", or provides CI test history (JUnit XML reports, CI run logs) to classify unstable tests. Applies deterministic scoring before any narrative and reports "unknown" below the evidence threshold instead of guessing.
version: 1.0.2
---

# Flaky Test Triage

## Purpose

A flaky test fails then passes with no code change. The real cost is CI
trust: red stops meaning "defect" and starts meaning "re-run the job", and
genuine regressions hide in the noise. This skill turns a test-run history
into an actionable triage: a flakiness score, a probable cause backed by
evidence, and a corrective action — statistics decide, narrative only
explains.

## Procedure

### 1. Gather run history

Collect results for the same test suite across multiple runs: JUnit XML
files, CI logs, or a pasted pass/fail history. A single report cannot show
flakiness — nothing can alternate: report "insufficient history (1 run)" and
stop. Below 4 runs, score anyway: the score is damped (step 2), and the
triage states that the history is thin (N runs, 4 for an undamped score).

For each test and run, extract: outcome (pass/fail/error/skip), duration,
and position in the suite if available.

### 2. Score each unstable test

Compute a flakiness score in [0, 1] from three signals. Compute it with
code (a short script over the parsed runs), not by estimation, so the same
history always yields the same score. Use the terminal runs in chronological
order (skips excluded; fail and error both count as failures):

| Signal | Weight | Reading |
|---|---|---|
| Intermittency | 0.4 | `4 × p × (1 − p)`, p = failure ratio: maximal at 50 %. A test failing 100 % of runs is **broken, not flaky** — score it 0 and route it to normal debugging. |
| Flip rate | 0.4 | Pass/fail changes between consecutive runs ÷ (runs − 1). |
| Duration instability | 0.2 | Coefficient of variation of durations (population standard deviation ÷ mean), capped at 1. |

Score = weighted sum × min(1, runs ÷ 4): a history under 4 runs is damped,
never refused.

Treat scores ≥ 0.5 as flaky, 0.2–0.5 as suspect (monitor, do not quarantine
yet), < 0.2 as stable noise.

### 3. Classify the probable cause

Score each cause hypothesis independently against the evidence signatures in
`references/cause-heuristics.md` (timing/async, test ordering/shared state,
environment, resource contention). The strongest signal wins. A single
failing run is never enough for a diagnosis: with only one failure, halve
the confidence. **Below the confidence floor of 0.4, answer `unknown` —
never guess.** An honest `unknown` with evidence is more useful than a
confident fabrication.

### 4. Render the triage

Output one block per flagged test:

```
<test id>
  flakiness <score> | cause: <cause> (confidence <c>) | runs: <n>
  evidence:       <concrete observations: run numbers, positions, durations>
  recommendation: <corrective action from the cause matrix>
```

Order by descending score. Close with a one-line suite summary
(N tests analyzed across M runs: K flaky, J suspect).

## Rules

- Broken ≠ flaky: consistent failure is a defect, route it out of triage.
- Every claim cites its evidence (which runs, which positions, which
  durations). No evidence, no claim.
- Recommend quarantine + targeted fix; never recommend blind re-runs as a
  remedy — they are the disease being treated.
- Deterministic first: the score and classification never depend on
  intuition; the narrative merely explains numbers that already exist.

## Additional Resources

- **`references/cause-heuristics.md`** — evidence signatures, confirmation
  protocol, and corrective actions for each cause family.
