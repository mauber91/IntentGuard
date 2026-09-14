# ADR-002: Keep Organizational Policy Outside the LLM

- **Status:** Proposed
- **Date:** 2026-09-14

## Context

A model can infer whether architecture changed, but the rule determining whether that requires an ADR is organizational policy.

If both interpretation and policy are embedded in prompts, behavior becomes harder to audit, tune, and reproduce.

## Decision

LLMs produce structured semantic facts. A deterministic policy engine maps those facts to actions.

Example:

```text
LLM:
  architecture_changed = true
  persistence_strategy_changed = true

Policy:
  architecture_changed AND persistence_strategy_changed
  => ADR review required
```

## Consequences

Rules can be versioned, tested, explained, and customized per repository or organization.
