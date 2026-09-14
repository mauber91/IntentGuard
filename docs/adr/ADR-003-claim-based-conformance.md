# ADR-003: Evaluate PR Facts Against Documentation Claims

- **Status:** Proposed
- **Date:** 2026-09-14

## Context

Comparing an entire pull request with an entire design document introduces unnecessary context and makes results harder to explain.

## Decision

During indexing, convert documentation into normalized statements with source references. During PR analysis, compare structured change facts only with the statements relevant to the affected component or behavior.

## Consequences

### Positive

- smaller model context,
- clearer evidence,
- more precise explanations,
- reusable document index.

### Negative

- additional indexing work,
- statement extraction needs its own evaluation and versioning.
