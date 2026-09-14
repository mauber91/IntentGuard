# ADR-004: Humans Resolve Intent Conflicts

- **Status:** Proposed
- **Date:** 2026-09-14

## Context

When code differs from an ADR or specification, two valid situations exist:

1. implementation drifted from the documented decision,
2. the decision intentionally changed.

The source code alone usually cannot establish which is authoritative.

## Decision

Confirmed documentation conflicts trigger human-in-the-loop resolution.

The agent may present evidence and recommended next actions, but it may not autonomously decide whether the code or documentation is correct.

## Consequences

This preserves human ownership of architectural and product intent while allowing the agent to automate detection and follow-up.
