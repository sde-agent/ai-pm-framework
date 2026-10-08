# AI PM planning templates

Use these with [the field guide](AI-PM-FIELD-GUIDE.md) and [evaluation playbook](EVALUATION-PLAYBOOK.md). Replace placeholders with decisions or explicit Unknowns. A template is complete when the team can test it, not when every field sounds confident.

## PRD: an evidence-to-action product

### 1. User decision and outcome

- **User / workflow:** [Who is deciding what, at which moment?]
- **Current alternative:** [Manual process, rules, existing tool; measured baseline or unknown]
- **Outcome hypothesis:** [Desired change, comparison, measurement window]
- **Capability:** [Observable behavior required to make the decision better]
- **Non-goals:** [Claims and actions outside this release]

**ProductSignal draft:** A PM reviews a specific customer-observed problem with linked evidence and unknowns, decides whether to investigate, and receives material updates on the same problem. Intended outcome: earlier useful recognition with manageable review effort. Root-cause proof, automatic alerts and measured time savings are outside the established feasibility claim.

### 2. Problem and asymmetric risk

| Error | Affected user / consequence | Relative priority | Safeguard / owner |
|---|---|---|---|
| [Miss] | [Cost] | [Priority] | [Control] |
| [False candidate] | [Cost] | [Priority] | [Control] |
| [Unsupported inference] | [Cost] | [Priority] | [Control] |
| [Wrong action] | [Cost] | [Priority] | [Control] |

Record conflicting priorities and their resolution. ProductSignal: protect meaningful small behaviors, avoid over-merging, then reduce fragmentation. Review capacity imposes another constraint; it does not erase the first priorities.

### 3. Evidence contract

- Authorized sources, fields, timestamps, freshness and retention: [ ]
- Unit of evidence and independence assumptions: [ ]
- Supported facts versus hypotheses: [ ]
- Missing/contradictory evidence behavior: [ ]
- Evidence citations and access controls: [ ]
- Reference labels, adjudication and ambiguity rules: [ ]

**Acceptance example:** A card’s “three reports” resolves to three source IDs. It never claims three customers unless identity supports that count. Unsupported root cause stays Unknown or explicitly hypothetical.

### 4. Decision policy

| Condition | Response | Exception | Required evidence | Version / owner |
|---|---|---|---|---|
| [Evidence sufficient] | [Review candidate] | [High-risk low-count path] | [Fields/spans] | [ ] |
| [Existing problem match] | [Quiet update] | [Material change] | [Identity history] | [ ] |
| [Insufficient evidence] | [Abstain / request context] | [ ] | [Missing fields] | [ ] |

Define rubric precedence, score interpretation, calibrated thresholds where used, reviewer capacity, queue expiry, and appeal/correction behavior. Separate interpretation confidence from action eligibility.

### 5. Experience and authority

- First presentation: [Specific title, facts, evidence, unknowns, next step]
- Updates: [Quiet versus review-worthy; comparison window]
- Human controls: [Investigate, dismiss with reason, split, merge, request context]
- System permissions: [Read, draft, organize, execute; per tool and resource]
- Approval: [Exact payload and expiry; changes invalidate approval]
- Recovery: [Undo, retry, rollback, unresolved partial action]

### 6. Evaluation and success criteria

| Requirement | Test population / denominator | Baseline | Threshold and rationale | Owner |
|---|---|---|---|---|
| [Small behavior preserved] | [Reserved rare-behavior cases] | [ ] | [Risk-derived] | [ ] |
| [Policy faithful] | [Boundary cases, including Unknown] | [ ] | [ ] | [ ] |
| [Useful review] | [Human-reviewed candidates] | [ ] | [ ] | [ ] |
| [Outcome improves] | [Eligible incidents / comparison workflow] | [ ] | [ ] | [ ] |

Freeze criteria before final reserved evaluation. Record data provenance, slices, evaluation version and known blind spots. Keep synthetic mechanism tests separate from production/outcome claims.

### 7. Rollout and feedback

- Entry gate: [Offline evidence, UX test, controls]
- Shadow plan: [Duration, traffic, reviewer coverage]
- Pilot: [Users, authority, comparison, review capacity]
- Stop/rollback conditions: [Quality, harm, burden, system failure]
- Monitoring owner and response time: [ ]
- Corrections: [Structured reason, adjudication, privacy, release review]
- Phase stopping rule: [Question answered and decision enabled]

## System design: contracts at each boundary

### 1. Capability and constraints

Document required distinctions, scale, languages, hardware, privacy, latency budget, cost budget, availability and maximum authority. Compare at least one simpler baseline. Explain why each AI component is necessary.

### 2. Component contract

Repeat this table for ingest, representation, grouping, identity, retrieval, extraction, synthesis, policy, action and evaluation. Omit components the product does not need.

| Field | Specification |
|---|---|
| Input | Schema, permitted sources, availability time |
| Output | Schema, evidence IDs, uncertainty and failure status |
| Invariant | What must remain true, including permissions |
| Quality test | Metric, denominator, slices and reserved cases |
| Failure behavior | Retry, abstain, queue, fallback or stop |
| Version | Code/config/model/data/index identifiers |
| Trace | Reconstructable inputs, decisions and outcomes |
| Owner | Quality and operational accountability |

### 3. ProductSignal proposed contracts

| Boundary | Contract | Failure behavior |
|---|---|---|
| Ingest → grouping | Valid source ID, timestamp, text; no hidden labels | Quarantine malformed data; expose missing fields |
| Grouping → identity | Candidate members and grouping version; no incident-truth claim | Audit noise; uncertain matches go to review |
| Identity → evidence | Stable problem ID, attachment rationale, split/merge history | Preserve originals; reversible correction |
| Evidence → card | Authorized excerpts, known/unknown fields, claim support | Omit unsupported claims; expose gaps |
| Facts → severity | Versioned rubric inputs and evidence spans | Unknown for insufficient context; human review |
| Policy → action | Permitted action, reason, exact payload, approval where required | Deny unsupported authority; record rejection |
| Feedback → evaluation | Correction type, reason, reviewer, prior version | Adjudicate before changing reference data |

### 4. Decision records and alternatives

For each significant choice, write: context; options; evidence; chosen tradeoff; remaining risk; test that could reverse the choice. Example: embedding/clustering may discover novelty, but rules may be sufficient for known failures. Compare both at equal review budgets before selecting the more complex system.

### 5. Operational plan

Specify persistence, cache invalidation, index rebuilds, access deletion, retention, rate limits, queue backpressure, stage timeouts, tool idempotency and rollback. Explain what happens when inference is unavailable or evidence changes during approval. Include a kill switch for external actions and a way to continue manual review.

### 6. Design-review acceptance

The reviewer should be able to follow one report through every stage, reconstruct one failure, see why an action was allowed, and identify who handles an unresolved case. Attach one good trace, one ambiguous trace, one high-risk failure, and the reserved evaluation summary. A polished architecture diagram alone is insufficient.
