---
name: test-plan-generator
description: This skill should be used when the user asks to "generate a test plan", "write test cases for this user story", "what should we test here", or provides a user story, acceptance criteria, or feature description that needs structured test coverage. Produces a risk-prioritized test plan with full traceability from acceptance criteria to test cases.
version: 1.0.0
---

# Test Plan Generator

## Purpose

Turn a user story into a test plan a QA lead could execute or hand off:
risk-prioritized cases, explicit traceability to acceptance criteria, and
open questions surfaced instead of silently assumed away. The plan derives
strictly from the story — ambiguity becomes a question, never an invented
requirement.

## Procedure

### 1. Parse the story

Extract and restate:

- **actor** (who), **action** (what), **benefit** (why);
- every explicit **acceptance criterion** (AC), numbered `AC-1`, `AC-2`, …;
- implicit constraints only when they are certain from context (platform,
  data, permissions).

Anything unclear — undefined limits, unspecified error behavior, missing
roles — goes to an **Open questions** list. Do not resolve ambiguity by
assumption.

### 2. Assess risk

For the story as a whole and per AC, rate **impact** (what breaks for the
user or business if this fails) and **likelihood** (complexity, novelty,
integration surface). Use High/Medium/Low. The product of the two drives
test priority: highest-risk areas get cases first and deepest.

### 3. Derive test cases

For each AC, derive at minimum:

- one **nominal** case (happy path);
- the relevant **negative** cases (invalid input, unauthorized actor,
  unavailable dependency);
- **boundary** cases wherever a limit, count, threshold, or date appears
  (at, just below, just above).

Each case carries: id (`TC-n`), title, priority (from risk), preconditions,
steps, expected result, and the AC(s) it traces to.

### 4. Add cross-cutting coverage

Where the story warrants it — never as boilerplate:

- edge cases spanning several ACs (concurrency, retries, interrupted flows);
- non-functional checks (performance budgets, accessibility, security)
  when the story touches them;
- one or two **exploratory charters** for the areas the risk analysis
  flagged as uncertain ("Explore X to discover Y, timebox Z").

### 5. Render the plan

Use the structure in `references/test-plan-template.md`. Close with the
**traceability matrix**: every AC maps to at least one test case. An AC
that cannot be tested as written is flagged `NOT TESTABLE — needs
clarification`, listed in Open questions, and never silently dropped.

## Rules

- Traceability is the contract: no AC without a case, no case without an AC
  or an explicitly stated risk it covers.
- Prioritize by risk, not by ease of writing.
- Boundary values are mandatory wherever a number appears in the story.
- Open questions are a first-class deliverable — a plan that hides its
  unknowns is wrong, not incomplete.

## Additional Resources

- **`references/test-plan-template.md`** — canonical plan structure with an
  annotated example.
