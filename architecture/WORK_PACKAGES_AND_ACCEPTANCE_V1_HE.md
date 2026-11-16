# AI231 — חבילות עבודה, אחריות ומבחני קבלה
## Work Breakdown Structure V1

**Status:** PROPOSED  
**Purpose:** Translate the system blueprint into estimable engineering work and grant-review evidence.

## 1. Work package map

| WP | Work package | Accountable technical owner | Inputs | Deliverables | Acceptance evidence |
|---|---|---|---|---|---|
| WP01 | Requirements & architecture | Chief/System Architect | Institute goals, source repos | Requirements baseline, context model, ADRs | reviewed scope and traceability matrix |
| WP02 | Knowledge ingestion | Data/Backend Lead | Wix, GitHub, documents | connectors, ingestion jobs, source registry | repeatable import and source-hash tests |
| WP03 | Evidence & provenance | Data Governance Lead | source records | evidence schema, lineage, claim registry | every sample claim links to source locator |
| WP04 | Logical rule service | Formal Methods Lead | approved rule corpus, kernel interface | evaluation API, rule versions, trace | deterministic fixtures and contradiction tests |
| WP05 | Agent platform | Agent Systems Lead | agent contracts | registry, runtime, sandbox, tool gateway | permission and isolation tests |
| WP06 | Workflow orchestration | Platform Lead | workflow definitions | durable orchestrator, retry/checkpoint logic | replay, idempotency, recovery tests |
| WP07 | Authority & rights | Security/Policy Engineering Lead | authority matrix, policy rules | policy enforcement, approval gates | unauthorized actions blocked in negative tests |
| WP08 | Verification & audit | QA/Verification Lead | claims, evidence, workflow traces | verifier, audit store, reports | reproducible independent evaluation |
| WP09 | Trade rights workflow | Domain Product Lead | sample agreements and rules | contract intake and review workflow | benchmarked case studies and expert review |
| WP10 | Logic asset registry | Platform/Data Lead | asset schema, IP rules | asset registry, versions, verification status | traceable registration and version history |
| WP11 | Citizen service prototype | Product/Accessibility Lead | service journeys, privacy requirements | responsive web prototype, case/appeal flow | accessibility and usability test report |
| WP12 | Meta-agent factory | Agent R&D Lead | agent templates, evaluator | candidate generation and sandbox tests | candidate cannot deploy without approval |
| WP13 | Security & operations | SRE/Security Lead | threat model, topology | CI/CD, telemetry, backup, incident runbooks | restore drill, threat test, telemetry trace |
| WP14 | Integration & PoC | Program/QA Lead | all work packages | integrated pilot and final evidence pack | six end-to-end scenarios pass defined criteria |

Owners are role descriptions, not named appointments. The final staffing plan must identify accountable people and their qualifications.

## 2. Proposed 24-month sequence

| Period | Primary focus | Exit condition |
|---|---|---|
| M1–M4 | WP01, initial WP02/WP03, threat model | approved architecture and source inventory |
| M5–M8 | WP02–WP05 | knowledge pipeline, rule API, first sandboxed agents |
| M9–M12 | WP05–WP08 | orchestrated workflow with evidence and authorization |
| M13–M16 | WP09/WP10 | trade and logic-asset workflow prototypes |
| M17–M20 | WP11/WP12 | citizen prototype and sandboxed agent factory |
| M21–M24 | WP13/WP14 | integrated pilot, security review, evaluation report |

Parallelization is possible only after interface contracts and test fixtures are agreed.

## 3. First vertical slice

Do not begin by building all 30 agents or all 300 workflows. First prove one complete vertical slice:

**Use case:** Citizen submits a proposal for review.

Components:
1. API receives and validates the submission.
2. Case service creates a case ID.
3. Knowledge service retrieves approved source records.
4. Orchestrator launches two bounded specialist agents.
5. Logical service checks a small explicit rule set.
6. Verification checks source links and contradictions.
7. Authority service determines whether escalation is required.
8. Human/authorized reviewer records disposition.
9. Audit service stores the event chain.
10. Citizen interface displays status and permitted explanation.

Acceptance: reproducible run, source trace, no unauthorized side effect, reviewable audit record, and tested appeal path.

## 4. First implementation backlog

- Define canonical IDs and JSON schemas.
- Establish source inventory and data classification.
- Write threat model and trust-boundary diagram.
- Create agent contract schema and validation.
- Define workflow run state machine.
- Define logical adapter request/response contract.
- Build one source ingestion connector.
- Build one deterministic rules fixture.
- Build one agent in a sandbox with read-only tools.
- Build orchestrator flow with durable checkpoint.
- Add policy check before any write action.
- Add correlated trace and audit event.
- Implement citizen proposal vertical slice.
- Add failure injection and negative authorization tests.
- Produce baseline latency/cost/reliability measurements.

## 5. Quantities: targets versus deployed assets

| Quantity | Planning target | Current interpretation |
|---|---:|---|
| Principal agent roles | 30 | planned role catalogue |
| Workflow templates | ~300 | planned catalogue; not 300 deployed agents |
| Project period | 24 months | planning assumption |
| Budget envelope | ₪14.8m | preliminary estimate requiring bottom-up validation |
| Global citizen users | Not set | scale target to be determined after pilot |
| Production availability | Not set | establish after workload analysis |

## 6. Definition of Done

A work package is complete only when:
- source and design documents are versioned;
- implementation exists in the repository;
- automated tests run and their output is retained;
- security and privacy checks are completed where relevant;
- acceptance criteria are met;
- limitations and unresolved items are recorded;
- a reviewer other than the implementer has reviewed the evidence.

## 7. Risks and mitigations

| Risk | Mitigation |
|---|---|
| Scope too broad for 24 months | vertical-slice-first and staged gates |
| Hallucinated or unsupported claims | provenance-required outputs and independent verification |
| Agent permission escalation | default-deny policy service and negative tests |
| Prompt injection / data exfiltration | sandbox, allowlists, secret isolation, adversarial tests |
| Unclear legal operator | resolve entity and signatory before submission |
| Grant route mismatch | obtain written route/eligibility guidance before final budget |
| Unbounded operating cost | per-run cost telemetry and workload-based sizing |
| Premature global deployment claims | separate R&D PoC from future public-service scale |

## 8. Open decisions

- Legal applicant/operator and authorized signatory.
- Initial cloud/hosting jurisdiction.
- First two end-to-end use cases.
- Source repositories and publication permissions.
- Data retention and privacy impact requirements.
- Workflow engine selection.
- Exact kernel integration interface.
- Funding track, eligible cost rules, and matching-fund source.
