# ADR-001: Use LangGraph for Workflow Orchestration

- **Status:** Proposed
- **Date:** 2026-09-14

## Context

IntentGuard requires explicit branching, resumable human-in-the-loop decisions, structured state, bounded retries, and traceable execution.

An open-ended ReAct loop would give the LLM too much control over routing and make repeated behavior harder to reproduce.

## Decision

Use LangGraph as the orchestration layer.

Graph nodes will have narrow responsibilities and Pydantic-defined contracts. Routing should be deterministic whenever the branch condition can be derived from structured state.

## Consequences

### Positive

- explicit control flow,
- checkpointable HITL,
- better observability,
- easier testing,
- easier regression analysis.

### Negative

- more workflow code,
- graph migrations need care as state schemas evolve.
