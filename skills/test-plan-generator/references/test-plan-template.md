# Test Plan Template

Canonical structure for a generated test plan. Sections marked *(if
relevant)* are omitted when the story does not warrant them — never padded.

```markdown
# Test Plan — <story title>

## Story under test
As a <actor>, I want <action>, so that <benefit>.

## Acceptance criteria
- AC-1: …
- AC-2: …

## Risk assessment
| Area | Impact | Likelihood | Priority |
|---|---|---|---|
| … | High/Med/Low | High/Med/Low | P1/P2/P3 |

## Test cases

### TC-1 — <title>   [P1] [traces: AC-1]
- Preconditions: …
- Steps:
  1. …
  2. …
- Expected result: …

### TC-2 — …

## Exploratory charters *(if relevant)*
- CH-1: Explore <target> using <resources> to discover <information>.
  Timebox: <duration>.

## Non-functional checks *(if relevant)*
- NF-1: …

## Traceability matrix
| AC | Covered by |
|---|---|
| AC-1 | TC-1, TC-3 |
| AC-2 | TC-2 |

## Open questions
- Q-1: …
```

## Annotated example (abridged)

Story: *As a registered user, I want to reset my password by email, so that
I can regain access to my account.*

- AC-1: a reset link is emailed to a registered address within 5 minutes.
- AC-2: the link expires after 24 hours.
- AC-3: a non-registered address shows the same confirmation message
  (no account enumeration).

Derivation notes:

- AC-1 yields the nominal case (TC-1) plus a boundary on the 5-minute
  budget (TC-2: verify delivery time is measured and asserted, not assumed).
- AC-2 is boundary-driven: at 23 h 59 (valid), at 24 h 01 (expired) — two
  cases, not one.
- AC-3 is security-flavored: the negative case asserts *identical* response
  body and timing for registered vs unknown addresses.
- Open question example: "Q-1: what happens on a second reset request while
  a link is still valid — invalidate the first, or keep both?" This is an
  undefined behavior in the story; it must not be guessed into a test case.
