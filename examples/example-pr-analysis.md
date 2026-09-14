# Example PR Analysis

## Scenario

A pull request replaces synchronous inventory updates with Kafka events.

### Existing ADR

`ADR-018`:

> Inventory updates must remain synchronous because checkout requires acknowledgement before completion.

### IntentGuard result

```markdown
## IntentGuard — Documentation Integrity

**Human review required**

This PR changes inventory updates from synchronous requests to asynchronous Kafka events.

### Governing documentation

`ADR-018 — Inventory communication`

Relevant claim:
> Inventory updates must remain synchronous because checkout requires acknowledgement before completion.

### Divergence

1. `services/inventory/publisher.py`
   - publishes stock updates to Kafka.

2. `checkout/order.py`
   - no longer waits for inventory acknowledgement.

### Resolution

Is this architecture change intentional?

- **Intentional** → supersede/update ADR-018.
- **Unintentional** → update the implementation.
- **False positive** → dismiss and record feedback.
```

## Why this is not an automatic failure

IntentGuard detects divergence, not truth.

The accepted ADR may be stale, or the implementation may be wrong. Human intent determines the next action.
