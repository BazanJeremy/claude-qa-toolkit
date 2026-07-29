---
name: session-close
description: This skill should be used when the user says "end of session", "close the session", "session close", "fin de session", "on clôture", announces a feature or work phase is finished, or when the conversation context is getting heavy and continuity must be preserved before clearing it. Updates the project's active-context file and append-only session journal so the next session resumes without re-discovery.
version: 1.0.0
---

# Session Close

## Purpose

Long-running projects lose momentum between AI-assisted sessions: the next
session starts blind, re-discovers state, and re-litigates settled decisions.
This skill closes a working session by persisting the project's hot state into
two continuity files, so the next session starts from facts instead of
archaeology.

Two files with opposite write disciplines:

| File | Role | Write discipline |
|---|---|---|
| Active context (e.g. `ACTIVE-CONTEXT.md`) | Hot state: where the project is *right now* | **Full rewrite** every close — never append |
| Session journal (e.g. `PROJECT-LOG.md`) | Archive: decisions and session history | **Append only** — never alter existing entries |

## Procedure

Execute in order, without skipping a step.

### 1. Locate the continuity files

- Check the project's agent instruction file (`CLAUDE.md`, `AGENTS.md`, or the
  equivalent for the harness in use) for declared continuity file names or a
  session-close convention. Honor it if present.
- Otherwise look at the repository root for common names:
  `ACTIVE-CONTEXT.md`, `CONTEXTE-ACTIF.md` (active context) and
  `PROJECT-LOG.md`, `etat-du-projet.md` (journal).
- If none exist, propose creating `ACTIVE-CONTEXT.md` and `PROJECT-LOG.md` at
  the repository root, then proceed with the newly created files.

### 2. Session recap

List what the session actually accomplished: features delivered, PRs opened
or merged, bugs found and fixed, decisions taken **with their rationale**.
Report only what happened — never pad the recap with planned or hoped-for
work.

### 3. Rewrite the active-context file entirely

Overwrite the previous content completely. Target **40 lines maximum**.
Canonical sections:

- current phase or milestone;
- state considered done (merged, verified);
- next task, stated precisely — or the candidate options if not yet decided;
- open risks and gotchas still valid (drop the ones no longer true);
- environment notes if they changed this session;
- date of this close.

A stale line in the active context is worse than a missing one: the file must
be trustworthy at first read.

### 4. Append to the session journal

Only if a feature, PR, or decision was completed this session. Add — without
modifying any existing content:

- the decisions taken, each with its justification;
- one summary line in the session journal table (date, scope, outcome).

### 5. Propose the commit

Propose committing both files — inside the open PR if one exists, otherwise
as a dedicated commit (suggested message: `docs: session close`). Never push
without an explicit request.

### 6. Announce completion

End with an explicit statement that the close is done and the user can safely
clear the conversation context.

## Rules

- Full rewrite for the active context; append-only for the journal. Never
  swap these disciplines.
- Record only facts from the session — no invented or anticipated state.
- Keep the active context under 40 lines; move anything bigger to the journal.
- Convert relative dates ("today", "last week") to absolute dates.
