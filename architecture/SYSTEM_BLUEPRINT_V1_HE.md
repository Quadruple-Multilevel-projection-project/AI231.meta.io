# AI231 — System Architecture Blueprint V1
## תכנון ארכיטקטורה משולבת למכון לממשל נוירוני

**Status:** PROPOSED / DESIGN BASELINE  
**Version:** 1.0  
**Scope:** Public-service AI infrastructure, algorithmic governance support, international trade and logic-asset workflows.  
**Important:** This is a proposed architecture. It does not assert that the integrated platform is implemented, legally authorized, production-ready, or globally deployed.

## 1. Architectural objectives

1. Provide a single, auditable workflow platform for the Institute.
2. Preserve source provenance from ingestion through every material output.
3. Combine probabilistic AI interpretation with deterministic rules and explicit constraints.
4. Coordinate specialist agents through versioned contracts and bounded permissions.
5. Separate analysis, recommendation, authorization, and execution.
6. Support citizen-facing services with privacy, accessibility, appeal, and transparency controls.
7. Enable controlled development and evaluation of future agents.
8. Support incremental deployment, rollback, and independent verification.

## 2. System context

\`\`\`mermaid
flowchart TB
  subgraph External["External actors and sources"]
    CIT["Citizens / participants"]
    INST["Authorized institutions"]
    WEB["Wix / public websites"]
    GH["GitHub repositories"]
    DOCS["Documents / research / datasets"]
    TRADE["Trade agreements / counterparties"]
  end
  subgraph AI231["AI231 Institute Platform"]
    EDGE["Web / Mobile / API Gateway"]
    CASE["Case & Workflow Service"]
    ORCH["Workflow Orchestrator"]
    AGENTS["Agent Runtime"]
    LOGIC["Logical Rule Service"]
    KNOW["Knowledge & Evidence Service"]
    AUTH["Authority / Policy Enforcement"]
    VERIFY["Independent Verification"]
    DOMAIN["Domain Services: Civic / Trade / Logic Assets"]
    AUDIT["Audit & Transparency Service"]
  end
  subgraph Ops["Platform operations"]
    IAM["Identity & Access"]
    SEC["Security / Secrets / Threat Detection"]
    OBS["Observability"]
    INFRA["Compute / Storage / Backup"]
  end
  CIT --> EDGE
  INST --> EDGE
  WEB --> KNOW
  GH --> KNOW
  DOCS --> KNOW
  TRADE --> DOMAIN
  EDGE --> CASE --> ORCH
  ORCH --> AGENTS
  ORCH --> LOGIC
  AGENTS <--> KNOW
  LOGIC <--> KNOW
  AGENTS --> VERIFY
  LOGIC --> VERIFY
  VERIFY --> AUTH
  AUTH --> DOMAIN
  AUTH -->|Escalation / authorized decision| INST
  DOMAIN --> AUDIT
  CASE --> AUDIT
  IAM -.-> EDGE
  IAM -.-> AUTH
  SEC -.-> AGENTS
  SEC -.-> KNOW
  OBS -.-> ORCH
  OBS -.-> INFRA
  INFRA -.-> AI231
\`\`\`

## 3. Logical layers and ownership

| Layer | Primary service | Owns | Must not do |
|---|---|---|---|
| L0 Experience | Web/mobile/API | User interaction and accessibility | Decide policy or authority |
| L1 Identity | IAM/consent | Identity, roles, consent, sessions | Infer legal authority from identity alone |
| L2 Case management | Case service | Cases, lifecycle, status, appeals | Rewrite evidence |
| L3 Knowledge | Ingestion, index, provenance | Sources, versions, extracted records | Convert generated summaries into source facts |
| L4 Logic | Rule engine, ontology, constraints | Deterministic rule evaluation and trace | Invent rules or weights |
| L5 Agent runtime | Agent registry and workers | Specialist execution under contracts | Expand own permissions |
| L6 Orchestration | Workflow graph engine | Task decomposition, routing, retries, state | Bypass verification or authorization |
| L7 Verification | Evidence and test services | Independent checks, contradictions, quality | Treat model confidence as proof |
| L8 Authority | Policy enforcement | Permission decisions and escalation | Create public/legal authority |
| L9 Domain services | Civic, trade, logic assets | Domain workflows and records | Make final decisions absent authority |
| L10 Audit/operations | Audit, observability, security | Logs, metrics, response, recovery | Expose private data by default |

## 4. Core data entities

Minimum versioned entities:
- \`SourceRecord\`: source_id, uri, publisher, captured_at, source_version, content_hash, access_class.
- \`EvidenceItem\`: evidence_id, source_id, locator, excerpt_hash, evidence_state, reviewer, reviewed_at.
- \`Claim\`: claim_id, normalized_statement, epistemic_state, evidence_refs[], author_agent, created_at.
- \`Rule\`: rule_id, version, scope, antecedents[], consequence, source_refs[], status, approval_ref.
- \`AgentDefinition\`: agent_id, version, role, input_schema, output_schema, allowed_tools[], permission_profile, evaluation_suite.
- \`WorkflowDefinition\`: workflow_id, version, graph, entry_conditions, exit_conditions, escalation_rules, owner.
- \`Case\`: case_id, domain, requester_ref, status, workflow_ref, created_at, updated_at.
- \`ActionProposal\`: action_id, case_id, proposed_action, evidence_refs[], permission_check, disposition.
- \`DecisionRecord\`: decision_id, decision_body, authorized_actor, authority_ref, procedure_ref, timestamp.
- \`AuditEvent\`: event_id, actor_id, action, target_ref, timestamp, correlation_id, result, integrity_ref.

All persistent identifiers are stable. Changes create new versions rather than silently rewriting prior records.

## 5. Evidence and epistemic state model

Allowed states (initial proposal):
- \`SOURCE\`: directly captured source material.
- \`EXTRACTED\`: machine-extracted content linked to source location.
- \`ASSERTED\`: claim supplied by a participant or agent.
- \`DERIVED\`: result produced by explicit inference/rules.
- \`VERIFIED\`: passed a defined verification procedure.
- \`UNRESOLVED\`: evidence or interpretation remains incomplete.
- \`REJECTED\`: failed a stated acceptance rule.

\`VERIFIED\` must name the verifier, test procedure, evidence set, and time. A model's confidence score alone never upgrades a claim to VERIFIED.

## 6. Agent contract

Every agent must publish a machine-readable contract:

\`\`\`yaml
agent_id: rights-reviewer
version: 0.1.0
role: "Review a case for specified rights-related risks"
inputs:
  - case_summary
  - evidence_refs
outputs:
  findings:
    type: array
  missing_evidence:
    type: array
  limitations:
    type: array
  recommended_escalation:
    type: string
permissions:
  tools:
    - knowledge.search
  actions: []
prohibited:
  - issue_binding_decision
  - alter_rules
  - grant_own_permissions
  - publish_personal_data
verification:
  required: true
  evaluator_suite: rights-reviewer-v0
\`\`\`

The schema is illustrative and must be validated before use. Domain-specific contracts will be separately versioned.

## 7. Orchestration model

Recommended pattern: durable stateful workflow graph with explicit nodes, transitions, retry policy, timeouts, and human/authorized-actor checkpoints.

\`\`\`mermaid
stateDiagram-v2
  [*] --> Received
  Received --> Classified
  Classified --> EvidenceCollection
  EvidenceCollection --> Analysis
  Analysis --> Verification
  Verification --> NeedsMoreEvidence: unresolved
  NeedsMoreEvidence --> EvidenceCollection
  Verification --> AuthorityCheck: passed
  AuthorityCheck --> Escalated: approval required
  AuthorityCheck --> LimitedExecution: permitted action
  Escalated --> DecisionRecorded
  LimitedExecution --> AuditRecorded
  DecisionRecorded --> AuditRecorded
  AuditRecorded --> Closed
  Closed --> [*]
\`\`\`

Workflow state is durable. Re-running a node must be idempotent where possible. External side effects require idempotency keys and a permission check immediately before execution.

## 8. Logical kernel integration boundary

AI231/WANGA logical processing is integrated through a versioned adapter, not by direct coupling between every agent and the kernel.

Adapter responsibilities:
1. Convert candidate language-derived structures into typed input.
2. Validate JSON schema and required identifiers.
3. Submit only explicit evidence, claims, and rules.
4. Receive derivation trace, contradictions, rejection reasons, and limitations.
5. Preserve original language and source references.
6. Prevent unverified derived output from being re-labelled as source fact.

The WANGA-LAB Logical AI Kernel V1 document describes a bounded structured rule engine and explicitly lists limitations: it does not parse arbitrary language, autonomously acquire rules, or establish semantics from the 231-gate table. Those limits remain binding until code and tests demonstrate otherwise.

Reference: https://github.com/Quadruple-Multilevel-projection-project/WANGA-LAB/blob/main/docs/LOGIC_KERNEL_V1_IMPLEMENTATION.md

## 9. Security and trust boundaries

Trust zones:
- Public client zone.
- API and identity zone.
- Orchestration control plane.
- Agent execution sandbox.
- Knowledge and evidence storage.
- Policy/authorization service.
- External connectors.
- Audit and operations plane.

Minimum controls:
- OIDC-compatible identity; MFA for privileged operators.
- RBAC + ABAC, default deny.
- Per-agent service identity and scoped credentials.
- Secret vault; no secrets in prompts or repository.
- Encryption in transit and at rest.
- Tenant/case-level access boundaries.
- Sandboxed tool execution and outbound allowlists.
- Prompt-injection and data-exfiltration testing.
- Immutable or tamper-evident audit records.
- Backup, restore, incident response, and key rotation.
- Privacy impact assessment and data retention schedule.

## 10. Service/API boundaries

Proposed internal APIs:
- \`POST /cases\`
- \`GET /cases/{case_id}\`
- \`POST /cases/{case_id}/evidence\`
- \`POST /knowledge/search\`
- \`POST /logic/evaluate\`
- \`POST /workflows/{workflow_id}/runs\`
- \`GET /workflows/runs/{run_id}\`
- \`POST /verification/evaluate\`
- \`POST /authority/check\`
- \`POST /actions/{action_id}/approve\`
- \`POST /appeals\`
- \`GET /audit/events?case_id=...\`

All write endpoints require authentication, authorization, validation, audit correlation IDs, and idempotency handling as appropriate. These are proposed contracts, not deployed endpoints.

## 11. Deployment topology

Initial deployment recommendation: hybrid-capable, but start with one controlled environment and avoid premature multi-region complexity.

- Containerized stateless API and worker services.
- PostgreSQL as primary transactional store.
- Object storage for source documents and artifacts.
- Vector retrieval as a bounded index over approved content.
- Optional graph database only when graph queries justify operational cost.
- Queue for asynchronous tasks.
- Separate GPU inference pool if model workload requires it.
- Central telemetry and audit pipeline.
- Infrastructure as code and reproducible environments.

No production scale, throughput, latency, or GPU count is assumed until workload tests exist.

## 12. Technology selection — candidates, not commitments

| Concern | Candidate | Selection gate |
|---|---|---|
| Workflow orchestration | LangGraph or Temporal | durability, audit, retries, checkpoint tests |
| API | FastAPI or equivalent | schema, auth, observability |
| Database | PostgreSQL | transaction and migration tests |
| Vector retrieval | pgvector | retrieval evaluation on project corpus |
| Graph | PostgreSQL graph model / Neo4j | only if query benchmarks justify |
| Containers | Docker | reproducible build |
| Scheduling/orchestration | Kubernetes later if needed | load and operations evidence |
| Telemetry | OpenTelemetry | trace correlation across services |
| Identity | OIDC provider | security and privacy review |

Selection must be made through a documented ADR and a small benchmark; no vendor is pre-approved by this blueprint.

## 13. Non-functional requirements

- Reliability: recoverable workflows and tested backups.
- Security: least privilege and threat-model coverage.
- Privacy: data minimization, purpose limitation, retention controls.
- Accessibility: WCAG-aligned web/mobile design.
- Explainability: source-linked findings and traceable derivations.
- Interoperability: versioned APIs and exportable records.
- Maintainability: automated tests, migrations, CI/CD, rollback.
- Cost control: per-workflow and per-agent usage telemetry.
- Localization: Hebrew and English first; further languages by priority.

Quantitative SLOs will be set only after baseline load and threat testing.

## 14. Failure handling

- Unknown source or rule: mark unresolved and stop dependent action.
- Contradictory evidence: preserve both sources, flag conflict, escalate.
- Agent timeout: bounded retry, then alternate path or human review.
- Authorization denial: fail closed; do not ask another agent to bypass it.
- Verification failure: block publication/execution and retain trace.
- Data breach suspicion: isolate credentials/workers and activate incident process.
- Model/provider outage: queue or safe degrade; no silent substitution.

## 15. Acceptance gates

| Gate | Evidence required |
|---|---|
| G0 Requirements | approved scope, glossary, data inventory |
| G1 Knowledge | source capture, hash/version, retrieval evaluation |
| G2 Logic | deterministic test fixtures, trace, contradiction behavior |
| G3 Agents | contracts, sandbox, permission tests |
| G4 Orchestration | durable state, retries, idempotency, replay |
| G5 Authority | negative tests proving unauthorized action is blocked |
| G6 Verification | independent evaluator and reproducible reports |
| G7 Citizen service | accessibility, privacy, case/appeal lifecycle |
| G8 Integrated PoC | end-to-end scenarios, security review, measured results |

## 16. Architecture decision register

Open decisions:
- AD-001: legal operator and contracting entity.
- AD-002: public/private/hybrid deployment boundary.
- AD-003: identity assurance and eligibility rules for citizen participation.
- AD-004: data classification, retention, and jurisdiction.
- AD-005: workflow engine selection.
- AD-006: exact integration contract with WANGA logical kernel.
- AD-007: which actions can be automated and which require authorized approval.
- AD-008: public-source and open-source publication policy.
- AD-009: quantitative performance and security targets.
- AD-010: grant route and eligible applicant.

## 17. Implementation sequence

1. Repository baseline, requirements, threat model, and data inventory.
2. Knowledge/evidence service and schema.
3. Logical adapter and deterministic evaluation fixtures.
4. Agent registry, one specialist agent, and sandbox.
5. Orchestrator and one end-to-end workflow.
6. Authority checks, audit, and independent verification.
7. One trade-rights workflow and one civic proposal workflow.
8. Citizen-facing prototype.
9. Meta-agent sandbox and evaluation.
10. Integrated pilot and evidence-based scale decision.

**No claim of completion is made until each acceptance gate has concrete test output and reviewed evidence.**
