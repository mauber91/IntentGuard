# Problem, Goals, and Scope

## Problem statement

Code changes are reviewed continuously, but documentation is not. Architecture decisions, product behavior, operational assumptions, and technical contracts gradually diverge from implementation.

Traditional code review tools answer questions such as:

- Is the code correct?
- Is it secure?
- Does it follow style?
- Does it break tests?

IntentGuard answers a different class of questions:

- Is this area of the system already documented?
- Does this PR still conform to that documentation?
- Is the PR intentionally changing a documented decision?
- If the change is undocumented, is it important enough to preserve as durable engineering knowledge?
- What is the minimum documentation update required?

## Product thesis

The most valuable moment to detect documentation drift is the PR, because the implementation, rationale, context, and responsible engineers are all present at the same time.

## Goals

### G1 — Detect documentation coverage

Given a semantic change, determine whether existing documentation governs the changed behavior, architecture, contract, or operational model.

Output:

- `COVERED`
- `PARTIALLY_COVERED`
- `NOT_COVERED`
- `UNCERTAIN`

### G2 — Detect conformance and drift

When governing documentation exists, compare the implementation change against the specific relevant claims.

Output:

- `CONFORMS`
- `CONFLICTS`
- `UNCLEAR`

### G3 — Detect undocumented durable decisions

For uncovered changes, determine whether the change introduces knowledge that should outlive the PR.

Examples:

- new architectural boundary
- new persistence strategy
- new infrastructure dependency
- new public API behavior
- changed product workflow
- changed operational procedure
- changed security boundary
- important tradeoff

### G4 — Keep humans authoritative

A documentation conflict means:

```text
implementation != documented intent
```

It does **not** automatically mean the implementation is wrong.

Humans decide whether:

- the implementation is wrong,
- the documented decision is obsolete,
- the existing document should be updated,
- an ADR should be superseded,
- the finding is a false positive.

### G5 — Minimize noise

The system should optimize for precision.

A documentation bot that comments on every PR will be ignored. Low-confidence findings should abstain or remain informational rather than block.

## Non-goals

IntentGuard is not primarily:

- a general code-review agent,
- a vulnerability scanner,
- a linting tool,
- an autonomous architecture authority,
- an unrestricted documentation generator,
- a replacement for engineers, architects, PMs, or reviewers.

## Success criteria

For a historical PR evaluation set:

- high precision on documentation-impact findings,
- high precision on actual documentation conflicts,
- low false-positive PR comment rate,
- measurable recall on known documentation-impacting changes,
- stable decisions across repeated runs,
- evidence-backed findings,
- low rate of human overrides caused by agent misunderstanding.

## Example outcome

A PR adds Kafka-based asynchronous inventory updates.

Existing ADR:

> Inventory updates must remain synchronous because checkout requires acknowledgement before completion.

IntentGuard should produce:

```text
Conflict detected.

Document: ADR-018
Claim: Inventory updates must remain synchronous.
Observed change: Inventory writes are emitted asynchronously through Kafka.

Human decision required:
1. Intended architecture change → supersede/update ADR-018.
2. Unintended drift → change implementation.
3. False positive → continue and record feedback.
```
