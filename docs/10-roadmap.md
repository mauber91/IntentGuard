# Roadmap

## Phase 0 — Evaluation harness

Before building the full GitHub experience:

- collect 50–200 historical PRs,
- label documentation-impacting changes,
- identify known ADR/spec conflicts,
- define baseline precision/recall,
- create Langfuse datasets.

Deliverable: reproducible offline evaluation.

## Phase 1 — MVP

Scope:

- GitHub PR ingestion,
- Markdown documentation only,
- ADR + tech spec support,
- static signals,
- semantic `ChangeSummary`,
- simple repository path-to-doc mapping,
- structured coverage classification,
- conformance checks,
- PR comment output,
- Langfuse tracing.

Do **not** automatically modify docs.

## Phase 2 — Deterministic policy engine

Add:

- configurable rules,
- API/schema diffing,
- dependency/infrastructure analysis,
- confidence thresholds,
- abstention,
- severity and blocking policy,
- policy versioning.

## Phase 3 — Claim index

Add:

- offline claim extraction,
- document lifecycle/status,
- document-to-code links,
- entity normalization,
- claim-level retrieval,
- supersession graph for ADRs.

## Phase 4 — Human-in-the-loop

Add:

- persistent LangGraph checkpoints,
- GitHub action buttons or commands,
- intended/unintended/false-positive resolution,
- feedback dataset ingestion.

## Phase 5 — Documentation proposal engine

After intent is resolved:

- propose ADR,
- update spec sections,
- supersede ADR,
- produce documentation branch/PR,
- never merge autonomously by default.

## Phase 6 — Multi-repository architecture graph

Support:

- service-to-service relationships,
- centrally stored architecture docs,
- shared ADRs,
- cross-repository conformance,
- organization-level policies.

## Phase 7 — Continuous documentation health

Beyond PRs:

- scheduled drift analysis,
- stale-document detection,
- docs with no implementation references,
- code paths with no governing documentation,
- architecture-decision coverage dashboard.

## MVP prioritization

```mermaid
quadrantChart
    title MVP Value vs Complexity
    x-axis Low Complexity --> High Complexity
    y-axis Low Value --> High Value
    quadrant-1 Build Later
    quadrant-2 Strategic
    quadrant-3 Avoid
    quadrant-4 Build First

    Static diff signals: [0.25, 0.78]
    Structured change extraction: [0.35, 0.9]
    Path-to-doc mappings: [0.2, 0.8]
    Claim extraction: [0.55, 0.88]
    Full ontology graph: [0.82, 0.7]
    Automatic doc commits: [0.72, 0.45]
    Langfuse eval harness: [0.38, 0.82]
    HITL conflict resolution: [0.55, 0.9]
```
