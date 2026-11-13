# Cause Heuristics — Evidence Signatures and Corrective Actions

Score each cause family independently against the observed history, using
only the signatures below, each at its fixed strength. The strongest signal
wins; on a tie, the first family in this file's order wins.
Confidence = winning strength × min(1, failing runs ÷ 2). Below 0.4
confidence, classify as `unknown`.

Keywords are case-insensitive substrings of a failing run's message. Anything
outside these signatures may be quoted as context, but it is not evidence and
adds no confidence.

These signatures, strengths and keyword lists mirror FlakySense's rule-based
classifier (`src/flakysense/agents/classifier.py`, commit `db1a845`).

## timing / async

**Evidence signature**
- Keyword: a failing run's message contains `timeout`, `timed out` or
  `deadline`.
- Duration outlier: every failing run is slower than the slowest passing
  run (needs at least one passing run with a non-zero duration).
- Strength: 0.85 when both hold, 0.7 for the duration outlier alone, 0.5
  for the keyword alone.

**Confirmation protocol**
- Re-run the test alone under artificial load or reduced CPU; flakiness
  should reproduce or worsen.

**Corrective action**
- Replace fixed sleeps with condition-based waits (poll until predicate).
- Make timeouts explicit and generous in CI profiles.
- Mock or fake the slow dependency where the wait is incidental to the
  behavior under test.

## ordering / shared state

**Evidence signature**
- Position split: with at least 2 failing runs and 2 passing runs, the
  suite positions where the test fails and those where it passes never
  overlap (fails at position k, passes at position j).
- Strength: 0.8.

**Confirmation protocol**
- Run the test alone, then under randomized ordering (e.g. pytest-randomly);
  position-dependent results confirm the hypothesis.

**Corrective action**
- Isolate shared state: fresh fixtures per test, reset caches/singletons in
  teardown, unique test data per test.
- Remove hidden coupling; make each test own its full arrange phase.

## environment

**Evidence signature**
- Environment split: with at least 2 failing runs and 1 passing run, an
  environment property recorded on every failing run (runner, OS, container
  image…) has one single value across all failures, and no passing run
  carries that value.
- Strength: 0.9.

**Confirmation protocol**
- Compare pass/fail distribution across runners or images; re-run on the
  suspect environment only.

**Corrective action**
- Pin the environment (image digest, locale, timezone, dependency versions).
- Make the test independent of wall-clock time and machine specifics
  (inject clocks, use temp dirs).

## resource contention

**Evidence signature**
- Keyword: a failing run's message contains `locked`, `deadlock`,
  `connection refused`, `address already in use` or `resource busy`.
- Strength: 0.5.

**Confirmation protocol**
- Run serially; if flakiness disappears, contention is confirmed.

**Corrective action**
- Allocate unique resources per worker (ports, schemas, temp dirs).
- Bound parallelism for the contended resource; add jitter/backoff only at
  the infrastructure layer, never inside test logic.

## unknown

When no signature reaches 0.4 confidence: report `unknown`, list the
evidence that was considered, and recommend quarantining the test while
collecting more run history until a cause signal emerges — never a
speculative fix. The cheapest way to collect it is usually: run alone × 20,
then randomized order × 20.
