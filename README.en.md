# claude-qa-toolkit

**QA toolkit for Claude Code — disciplined session closing, deterministic
flaky-test triage, test-plan generation from a user story. Three skills, no
external service, no API key.**

[![License: MIT](https://img.shields.io/badge/license-MIT-lightgrey)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-plugin-d97757)](https://docs.anthropic.com/en/docs/claude-code/plugins)

> 🇫🇷 [Version française](README.md)

## The problem

Three recurring frictions of AI-assisted QA work:

- **Continuity between sessions.** Every new session starts blind,
  re-discovers project state, and re-litigates settled decisions.
- **Triage of unstable tests.** Faced with noisy CI history, the temptation
  is to narrate a plausible cause instead of measuring it.
- **Test-plan drift.** A freely generated plan invents requirements and
  loses track of the actual acceptance criteria.

This plugin packages three working disciplines — not executable code, but
protocols the agent follows — with the same principle as the companion tools
below: **deterministic rules decide, narrative explains.**

## The three skills

| Skill | Triggers on | What it guarantees |
|---|---|---|
| `session-close` | "end of session", "fin de session", feature finished | Full rewrite of the active-context file (≤ 40 lines) + append-only journal: the next session starts from facts |
| `flaky-triage` | CI history provided, "which tests are flaky?" | Score over 3 signals (intermittency, flip rate, duration), confidence-gated probable cause — `unknown` over invention, 4-run minimum |
| `test-plan-generator` | user story provided, "generate the test plan" | Risk-prioritized nominal/negative/boundary cases, AC → case traceability matrix, ambiguities surfaced as open questions |

## Installation

From this repository (which is its own marketplace):

```bash
claude plugin marketplace add BazanJeremy/claude-qa-toolkit
claude plugin install claude-qa-toolkit@claude-qa-toolkit --scope project
```

Or from a local clone:

```bash
claude plugin marketplace add /path/to/claude-qa-toolkit
claude plugin install claude-qa-toolkit@claude-qa-toolkit --scope project
```

Skills load on the next session and trigger automatically on the phrases
above — no command to memorize.

### Outside Claude Code

All three skills follow the `SKILL.md` format and depend on no Claude
Code-specific mechanism, so they also install into any agent that reads that
format:

```bash
npx skills add BazanJeremy/claude-qa-toolkit
```

`npx skills` is a community installer, not a vendor channel: the marketplace
route above remains the reference path.

## Design

- **Deterministic first.** The `flaky-triage` heuristics (0.4/0.4/0.2
  weights, dampening under 4 runs, 0.5 confidence floor) are the ones proven
  in [FlakySense](https://github.com/BazanJeremy/flakysense); the skill
  applies the method where the tool applies the code.
- **Ambiguity is a deliverable.** `test-plan-generator` turns every unclear
  area into an open question instead of papering over it with an assumption.
- **Continuity is a ritual, not magic memory.** `session-close` enforces two
  opposite write disciplines: full rewrite for hot context, append-only for
  the archive.
- **Progressive disclosure.** Each `SKILL.md` stays short; detailed matrices
  (cause signatures, plan template) live in `references/` and only load into
  context when needed.

## Known limitations

- Skills are protocols the agent follows, not executables: reproducibility
  is methodological, not mechanical.
- `flaky-triage` requires multi-run history; a single JUnit report yields no
  verdict.
- Heuristics are calibrated on FlakySense's synthetic scenarios; large
  real-world histories may need threshold tuning.

## Related projects

These tools share the same principles: **deterministic first, AI where it adds value — QA stays the arbiter.** All run locally, no API key required.

| Project | Focus |
|---|---|
| [claude-qa-toolkit](https://github.com/BazanJeremy/claude-qa-toolkit) **← this repo** | Claude Code plugin: QA disciplines as skills |
| [EvalForge](https://github.com/BazanJeremy/EvalForge) | LLM evaluation & judge calibration |
| [ReleaseGuard](https://github.com/BazanJeremy/ReleaseGuard) | Explainable GO/NO-GO release gate |
| [FlakySense](https://github.com/BazanJeremy/flakysense) | Statistical flaky-test diagnosis |
| [Anomaly Sentinel](https://github.com/BazanJeremy/anomaly-sentinel) | Testing anomaly-detection AIs (medtech · fintech) |
| [TestScribe](https://github.com/BazanJeremy/testscribe) | AI-assisted bug report enrichment |
| [SkyGuard](https://github.com/BazanJeremy/skyguard) | Security quality gate for avionics-critical systems |

## Author

**Jérémy Bazan** — QA Engineer / QA Tech Lead, specialized in AI-driven Quality.
ISTQB Foundation v4. LLM integration (Claude, GPT) in production QA pipelines
within a major energy-sector group.

[LinkedIn](https://www.linkedin.com/in/jeremy-bazan/) · [GitHub](https://github.com/BazanJeremy)

## License

[MIT](LICENSE)
