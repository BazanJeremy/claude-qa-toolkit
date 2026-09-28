# Cause Heuristics — Evidence Signatures and Corrective Actions

Score each cause family independently against the observed history; the
strongest signal wins. Below 0.4 confidence, classify as `unknown`.

## timing / async

**Evidence signature**
- Failure messages mention timeouts, waits, sleeps, "element not found",
  "connection refused then succeeded".
- Duration of failing runs deviates strongly from passing runs (often
  longer — waits exhausted).
- Failures uncorrelated with suite position or environment.

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
- Failures cluster at specific suite positions (fails at position k, passes
  at position j) or only after specific predecessor tests.
- Test passes in isolation, fails in the full suite (or the reverse).
- Assertions on state the test did not itself create (caches, singletons,
  database rows, files).

**Confirmation protocol**
- Run the test alone, then under randomized ordering (e.g. pytest-randomly);
  position-dependent results confirm the hypothesis.

**Corrective action**
- Isolate shared state: fresh fixtures per test, reset caches/singletons in
  teardown, unique test data per test.
- Remove hidden coupling; make each test own its full arrange phase.

## environment

**Evidence signature**
- Failures correlate with a runner, OS, container image, region, or time of
  day — not with code or ordering.
- Messages mention missing env vars, DNS, locale, filesystem paths,
  clock/timezone.

**Confirmation protocol**
- Compare pass/fail distribution across runners or images; re-run on the
  suspect environment only.

**Corrective action**
- Pin the environment (image digest, locale, timezone, dependency versions).
- Make the test independent of wall-clock time and machine specifics
  (inject clocks, use temp dirs).

## resource contention

**Evidence signature**
- Failures correlate with parallel execution, shared ports, database locks,
  quota or rate-limit errors.
- Duration instability high across the whole suite, not just one test.
- Messages mention "address already in use", deadlocks, connection pool
  exhaustion.

**Confirmation protocol**
- Run serially; if flakiness disappears, contention is confirmed.

**Corrective action**
- Allocate unique resources per worker (ports, schemas, temp dirs).
- Bound parallelism for the contended resource; add jitter/backoff only at
  the infrastructure layer, never inside test logic.

## unknown

When no signature reaches 0.4 confidence: report `unknown`, list the
evidence that was considered, and recommend the cheapest discriminating
experiment (usually: run alone × 20, then randomized order × 20) rather than
a speculative fix.
