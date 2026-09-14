# Agent Workflow

## Why a graph instead of an autonomous loop

IntentGuard should behave more like a compiler pipeline than an open-ended autonomous agent.

Each node has:

- a known input contract,
- a known output contract,
- bounded responsibility,
- explicit routing,
- traceable evidence,
- deterministic failure behavior.

## LangGraph state machine

```mermaid
stateDiagram-v2
    [*] --> CollectPR
    CollectPR --> StaticAnalysis
    StaticAnalysis --> ExtractSemanticChanges
    ExtractSemanticChanges --> ValidateChangeSummary

    ValidateChangeSummary --> ResolveCoverage

    ResolveCoverage --> AssessWorthiness: NOT_COVERED
    ResolveCoverage --> CheckConformance: COVERED
    ResolveCoverage --> PartialCoverage: PARTIALLY_COVERED
    ResolveCoverage --> NeedsEvidence: UNCERTAIN

    AssessWorthiness --> RecommendNewDocs: DOCUMENT
    AssessWorthiness --> Pass: DONT_DOCUMENT

    CheckConformance --> Pass: CONFORMS
    CheckConformance --> HumanReview: CONFLICTS
    CheckConformance --> NeedsEvidence: UNCLEAR

    PartialCoverage --> CheckConformance
    PartialCoverage --> AssessWorthiness

    HumanReview --> UpdateDocs: INTENDED_CHANGE
    HumanReview --> RequestCodeChange: UNINTENDED_CHANGE
    HumanReview --> RecordFalsePositive: FALSE_POSITIVE

    NeedsEvidence --> Informational
    RecommendNewDocs --> [*]
    UpdateDocs --> [*]
    RequestCodeChange --> [*]
    RecordFalsePositive --> [*]
    Informational --> [*]
    Pass --> [*]
```

## Proposed state

```python
from typing import Literal
from pydantic import BaseModel, Field

Coverage = Literal[
    "COVERED",
    "PARTIALLY_COVERED",
    "NOT_COVERED",
    "UNCERTAIN",
]

Conformance = Literal[
    "CONFORMS",
    "CONFLICTS",
    "UNCLEAR",
]

Action = Literal[
    "PASS",
    "CREATE_DOCUMENTATION",
    "UPDATE_DOCUMENTATION",
    "HUMAN_REVIEW",
    "INFORMATIONAL",
]

class Evidence(BaseModel):
    path: str
    start_line: int | None = None
    end_line: int | None = None
    summary: str

class ChangeFact(BaseModel):
    kind: str
    subject: str
    predicate: str
    object: str | None = None
    confidence: float = Field(ge=0, le=1)
    evidence: list[Evidence]

class RelevantDocument(BaseModel):
    document_id: str
    path: str
    relevance_reason: str
    claim_ids: list[str]
    confidence: float = Field(ge=0, le=1)

class AgentState(BaseModel):
    repository: str
    pr_number: int

    facts: list[ChangeFact] = []
    coverage: Coverage | None = None
    relevant_documents: list[RelevantDocument] = []

    conformance: Conformance | None = None
    conflicts: list[dict] = []

    documentation_worthy: bool | None = None
    suggested_artifacts: list[str] = []

    action: Action | None = None
    human_resolution: str | None = None
```

## Node responsibilities

### `collect_pr`

Input: repository, PR number  
Output: normalized PR payload

### `static_analysis`

Deterministically extract machine-readable signals.

### `extract_semantic_changes`

Use an LLM with structured output to infer semantic facts that static analyzers cannot reliably derive.

### `validate_change_summary`

Reject or downgrade unsupported facts.

Rules:

- every important fact requires evidence,
- every file path must exist in the PR,
- confidence must be bounded,
- unsupported enums are rejected,
- model prose is never consumed directly by downstream routing.

### `resolve_coverage`

Determine whether the changed concepts are governed by existing documentation.

### `assess_documentation_worthiness`

Apply deterministic policy to uncovered changes.

### `check_conformance`

Compare change facts to governing claims.

### `human_review`

Interrupt the graph and persist state.

Human options:

- intentional change,
- unintended drift,
- false positive.

### `render_result`

Generate a concise PR comment from already-computed structured findings.

The renderer should not invent new findings.

## Human interrupt pattern

```mermaid
sequenceDiagram
    participant PR as Pull Request
    participant IG as IntentGuard
    participant H as Human Reviewer
    participant LG as LangGraph Checkpoint

    PR->>IG: PR updated
    IG->>IG: Detect documented conflict
    IG->>LG: Persist graph state
    IG->>PR: Request intent decision
    H->>PR: Select intentional / unintended / false positive
    PR->>IG: Resolution event
    IG->>LG: Resume checkpoint
    IG->>PR: Update result and next action
```
