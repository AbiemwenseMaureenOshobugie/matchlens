# MatchLens Domain Contracts Specification

**Status:** Proposed v0.1  
**Scope:** Domain model and application-port contracts derived from the Forecasting Workflow Specification  
**Authority:** Technical specification subordinate to the Project Constitution and Product Charter

## 1. Purpose

This specification defines the domain contracts required to implement the MatchLens forecasting workflow without allowing persistence, framework choices, or model implementation details to redefine the domain.

The domain layer represents business meaning, invariants, identities, lifecycle states, and governed decisions.

Persistence schemas, HTTP/API models, provider-specific records, ML framework objects, and UI representations are not domain contracts.

## 2. Contract hierarchy

The domain contracts derive from:

1. docs/PROJECT_CONSTITUTION.md
2. docs/PRODUCT_CHARTER.md
3. docs/specifications/FORECASTING_WORKFLOW.md

If implementation pressure conflicts with these contracts, the contract must be reviewed rather than silently weakened.

## 3. Design principles

### 3.1 Domain first
The domain model must not be shaped primarily by a database ORM, API framework, provider SDK, or ML library.

### 3.2 Explicit invariants
Invalid domain states should be difficult or impossible to construct.

### 3.3 Immutable material events
Forecasts, forecast probabilities, governance decisions, and settlement/evaluation evidence are historical records. They must not be silently mutated.

### 3.4 Versioned artifacts
Data, features, models, calibration procedures, governance policies, and relevant configurations require explicit identities/version references.

### 3.5 Separation of concerns
The domain must distinguish match identity, forecast request, data snapshot, feature snapshot, model selection, raw forecast, calibrated forecast, uncertainty assessment, governance decision, publication, outcome settlement, evaluation, and supersession.

### 3.6 Provider independence
Provider-specific identifiers and schemas must not become the canonical domain identity.

### 3.7 No hidden policy
Thresholds, reason codes, eligibility rules, and lifecycle transitions must be explicit contracts.

## 4. Initial domain vocabulary

Initial conceptual areas:

- Match: Match, TeamRef, CompetitionRef, MatchId
- Forecast request: ForecastRequest, ForecastRequestId, OutcomeSpace, ForecastCutoff, ForecastHorizon
- Provenance: DataSnapshot, DataSnapshotId, FeatureSnapshot, FeatureSnapshotId, FeatureDefinitionVersion, ProvenanceRef
- Model: ModelRef, ModelVersion, ModelLifecycleState, ModelCompatibility
- Forecast: Forecast, ForecastId, ProbabilityDistribution, ForecastGeneration, CalibrationRef, CalibrationVersion
- Governance: UncertaintyAssessment, GovernanceDecision, GovernanceStatus, GovernanceReason, ValidationResult
- Publication/audit: PublicationRecord, AuditRef
- Settlement/evaluation: OutcomeSettlement, EvaluationRecord, EvaluationMetric
- Relationships: Supersession

## 5. Value objects

Value objects represent constrained concepts whose equality is based on value rather than database identity.

### 5.1 MatchId
Opaque canonical identifier for a Match.

Requirements: stable, provider-independent, non-empty, and never derived solely from a provider identifier.

Provider identifiers belong in a separate mapping/provenance structure.

### 5.2 TeamRef
Reference to a canonical team identity. It must distinguish the canonical team from provider-specific aliases.

### 5.3 CompetitionRef
Reference to a canonical competition/context. The initial system may support one competition, but the domain must not hard-code a single competition into Match.

### 5.4 ScheduledKickoff
A timezone-aware timestamp representing the currently recognized scheduled kickoff.

Historical forecast context must preserve the value used at the relevant cutoff; later corrections require controlled records rather than silent mutation.

### 5.5 ForecastCutoff
A timezone-aware timestamp.

Invariant: ForecastCutoff is earlier than ScheduledKickoff for the initial pre-match workflow.

### 5.6 ForecastHorizon
Represents the relationship between cutoff and kickoff. It must be derived from explicit timestamps rather than stored as an unexplained free-form label.

### 5.7 OutcomeSpace
Initial supported value: 1X2, with exactly HOME, DRAW, and AWAY.

The design should permit future outcome spaces without allowing an unsupported space into the 1X2 forecast contract.

### 5.8 Probability
A finite numeric value constrained to 0 <= p <= 1.

### 5.9 ProbabilityDistribution
For 1X2, contains exactly one probability for Home, Draw, and Away.

Invariant: p_home + p_draw + p_away = 1 within a defined numerical tolerance.

The domain must reject NaN, infinity, negative values, values greater than one, missing outcomes, and invalid cardinality.

### 5.10 DataSnapshotId
Opaque identifier for an immutable data snapshot.

### 5.11 FeatureSnapshotId
Opaque identifier for an immutable feature snapshot.

### 5.12 VersionRef
A strongly typed reference to a versioned artifact. Semantic distinctions should be preserved for model version, calibration version, feature-definition version, governance-policy version, and code version.

### 5.13 ProvenanceRef
Reference to evidence establishing where a domain artifact came from. It should support source identity, source record/reference, retrieval time, information availability time where known, and transformation identity/version.

### 5.14 ReasonCode
Machine-readable identifier for a validation, governance, blocking, or settlement condition. Free text may supplement but must not replace it.

### 5.15 CorrelationId
Identifier used to trace a workflow request across application boundaries. It is operational metadata, not business identity.

## 6. Core entities

### 6.1 Match
Represents the football fixture being forecast.

Required conceptual attributes: match_id, home team, away team, competition, scheduled kickoff, identity/provenance references.

Invariants: home and away teams are distinct; competition is resolvable; kickoff is timezone-aware; match identity is stable; provider aliases do not redefine canonical identity.

A historical forecast must retain the match context used at its cutoff.

### 6.2 ForecastRequest
Represents an explicit request to generate a forecast.

Required attributes: request_id, match_id, outcome space, forecast cutoff, forecast horizon, request/correlation context, creation timestamp.

Invariants: supported outcome space; cutoff before kickoff; deterministic interpretation; resolvable match identity.

A request is not itself a forecast.

### 6.3 DataSnapshot
Represents the immutable eligible information set used by the workflow.

Required attributes: snapshot identity, cutoff association, source/provenance references, schema/version references, creation metadata, validation status.

Invariant: the snapshot must not change after it becomes the source artifact for a governed forecast.

### 6.4 FeatureSnapshot
Represents model-ready features derived from an identified DataSnapshot.

Required attributes: snapshot identity, source data snapshot, feature-definition version, feature values, transformation/provenance references, temporal eligibility result.

Invariants: no feature may depend on information unavailable at the cutoff; feature definition is versioned; source snapshot is identifiable; required features satisfy the approved feature contract.

### 6.5 ModelRef / ModelVersion
Identifies the forecasting model used.

Required conceptual attributes: model identity, model version, model family, lifecycle state, supported outcome space, applicable feature definition/version, applicable competition/context, applicable forecast horizon, approval/evaluation references.

ModelVersion must be distinct from ForecastId.

### 6.6 ModelCompatibility
Represents whether a model version may be used for a forecast request.

Checks may include outcome-space compatibility, feature compatibility, competition/context compatibility, forecast-horizon compatibility, lifecycle eligibility, calibration compatibility, and monitoring/drift eligibility.

Compatibility is an evaluated result, not a property inferred solely from a model name.

### 6.7 ForecastGeneration
Represents the model inference event.

Required attributes: generation identity, forecast request, model version, feature snapshot, raw probability distribution, generation timestamp, numerical validation result, reproducibility/configuration references.

The raw output must remain distinguishable from calibrated output.

### 6.8 CalibrationRef / CalibrationVersion
Identifies the calibration procedure and fitted calibration artifact.

Required conceptual attributes: calibration identity/version, applicable model/output regime, fitting/evaluation provenance, lifecycle/eligibility state, calibration evidence.

Calibration must not be represented as an unexplained boolean such as is_calibrated.

### 6.9 UncertaintyAssessment
Represents a controlled assessment of reliability limitations.

Required attributes: assessment version, assessment timestamp, methodology/reference, evidence references, classification, reason codes where applicable.

The domain must not encode an arbitrary subjective confidence percentage as a substitute for a defined methodology.

### 6.10 Forecast
Represents the immutable governed forecast artifact.

Required attributes: forecast identity, match identity, forecast cutoff, outcome space, raw forecast reference, calibrated probability distribution where applicable, model version, calibration version where applicable, data snapshot, feature snapshot, uncertainty assessment, governance decision, publication state, creation timestamp.

Invariant: a published Forecast is immutable. A later forecast for the same match is a distinct Forecast.

### 6.11 GovernanceDecision
Represents the deterministic eligibility decision.

Required attributes: decision identity, policy version, decision timestamp, status, evaluated controls, reason codes, evidence references.

The decision must not be reducible to a single model score.

### 6.12 PublicationRecord
Represents the publication event for an eligible forecast.

Required attributes: publication identity, forecast identity, publication timestamp, publication policy version, presentation/reference metadata.

Publication is downstream of governance.

### 6.13 OutcomeSettlement
Represents the accepted post-match outcome used for evaluation.

Required attributes: settlement identity, match identity, result source/provenance, settlement timestamp, completion status, resolved 1X2 outcome, reconciliation/correction status.

Invariant: settlement must satisfy the approved result-source policy before becoming evaluable.

### 6.14 EvaluationRecord
Represents an evaluation of one or more settled forecasts.

Required attributes: evaluation identity, forecast population/reference, outcome settlement references, model/calibration versions, evaluation window, methodology version, metric results.

An EvaluationRecord must never rewrite the forecasts it evaluates.

### 6.15 Supersession
Represents an explicit relationship between forecast records.

Required attributes: predecessor forecast, successor forecast, reason code, effective/supersession timestamp, policy version.

Invariant: supersession does not mutate or delete the predecessor forecast.

## 7. Enumerations and lifecycle states

The following are provisional domain enums and require implementation-level confirmation.

### 7.1 GovernanceStatus
- FORECAST
- LOW_CONFIDENCE
- NO_FORECAST
- MODEL_BLOCKED

### 7.2 ModelLifecycleState
- CANDIDATE
- EVALUATED
- APPROVED
- BLOCKED
- RETIRED

Only APPROVED is eligible for governed publication, subject to compatibility and other controls.

### 7.3 ValidationDisposition
- BLOCKING
- NON_BLOCKING
- INFORMATIONAL

### 7.4 ValidationStatus
- PASS
- FAIL
- NOT_EVALUATED

### 7.5 PublicationState
- NOT_PUBLISHED
- PUBLISHED

The domain must not imply that every generated forecast is published.

### 7.6 SettlementState
- PENDING
- SETTLED
- RECONCILIATION_REQUIRED
- INVALIDATED

The precise invalidation semantics require later settlement policy.

## 8. Cross-entity invariants

### Temporal invariant
No information with availability time after the forecast cutoff may influence a governed forecast.

### Probability invariant
A 1X2 probability distribution contains exactly three valid probabilities whose sum satisfies the defined tolerance.

### Version invariant
Every governed forecast identifies the versions of the model, feature definition, calibration where applicable, and governance policy used.

### Provenance invariant
Every material forecast input has traceable provenance to the required standard.

### Immutability invariant
Published forecasts and their original probability distributions cannot be silently changed.

### Governance invariant
A generated probability distribution is not a governed forecast until the governance decision permits publication.

### Settlement invariant
Evaluation requires an approved settled outcome.

### Supersession invariant
A later forecast does not overwrite an earlier forecast.

### Compatibility invariant
A model version may be used only when its approved applicability and compatibility conditions are satisfied.

## 9. Domain services

Candidate domain services include ForecastEligibilityService, ModelCompatibilityService, ProbabilityValidationService, ForecastSupersessionService, OutcomeSettlementService, and EvaluationService.

These are behavioral concepts, not permission to create a large service layer. Pure invariants should remain close to the relevant value object/entity where practical.

## 10. Application ports

The domain remains independent of external providers and infrastructure.

Initial port concepts:

- Match repository: resolve canonical matches and preserve identity/provenance.
- Data snapshot repository: store/retrieve immutable snapshots and support reproducibility.
- Feature snapshot repository: store/retrieve feature snapshots and feature-definition versions.
- Model registry: resolve model versions, lifecycle state, applicability, and compatibility metadata.
- Calibration registry: resolve calibration versions, applicability, and lifecycle state.
- Forecast repository: persist/retrieve immutable forecasts and supersession relationships.
- Governance policy registry: resolve governance-policy versions.
- Governance decision repository: persist/retrieve decisions and evidence.
- Publication repository: persist publication events and support audit retrieval.
- Outcome source/settlement repository: obtain or persist approved outcomes and support reconciliation.
- Evaluation repository: persist evaluation records and retrieve evaluation history.
- Clock: provide current time through an injectable abstraction where deterministic testing requires it.
- Unit of work/transaction boundary: application-level mechanism for coordinated durability where required.

The exact persistence mechanism is deliberately deferred.

## 11. Provider boundary

External football-data providers must be adapted into the domain through an anti-corruption boundary.

Provider-specific concepts must not leak into canonical Match identity, Forecast identity, FeatureSnapshot semantics, ModelVersion semantics, or GovernanceStatus.

A provider adapter may retain provider-native identifiers and metadata in infrastructure/provider records and map them to domain references.

## 12. Commands and results

Application commands should represent intent, not database operations.

Initial command concepts:

- CreateForecastRequest
- BuildDataSnapshot
- ValidateDataSnapshot
- BuildFeatureSnapshot
- ValidateFeatureSnapshot
- SelectModel
- GenerateForecast
- CalibrateForecast
- AssessUncertainty
- EvaluateGovernance
- PublishForecast
- SettleOutcome
- EvaluateForecast

Command handlers belong outside domain entities.

The command sequence must enforce the workflow specification rather than allow arbitrary stage skipping.

## 13. Domain errors

Errors must distinguish invalid domain state from infrastructure failure.

Initial categories:

- invalid identifier;
- invalid timestamp;
- unsupported outcome space;
- invalid probability distribution;
- temporal eligibility violation;
- missing required provenance;
- incompatible model;
- ineligible model lifecycle state;
- calibration incompatibility;
- governance blocked;
- invalid publication transition;
- invalid settlement transition;
- invalid supersession relationship.

Provider, network, and database errors belong outside the domain error taxonomy unless translated into an explicit application-level failure.

## 14. What the domain contract must not decide

This specification does not choose the database, ORM, web framework, ML framework, football provider, first competition, model implementation, calibration algorithm, uncertainty algorithm, exact governance thresholds, exact reason-code catalogue, or deployment architecture.

Those decisions belong in later specifications or ADRs.

## 15. Implementation boundary

The intended Python architecture should preserve the conceptual separation:

- domain/ — entities, value objects, domain services, domain errors;
- application/ — use cases, commands, ports, orchestration;
- infrastructure/ — providers, persistence, model artifacts, external integrations;
- interfaces/ — API, CLI, or UI adapters when introduced.

The exact package structure is not frozen by this document.

No persistence schema should be created until the domain contracts and their required repository semantics have been reviewed.

## 16. Acceptance criteria

The domain contract is ready for implementation only when:

1. forecast identity is unambiguous;
2. Match identity is provider-independent;
3. forecast cutoff and information availability are explicitly distinct;
4. 1X2 probability invariants are explicit;
5. raw and calibrated forecasts are distinguishable;
6. model and calibration versions are independently identifiable;
7. model lifecycle eligibility is explicit;
8. governance is a separate domain concern;
9. blocking outcomes are machine-readable;
10. published forecasts are immutable;
11. supersession preserves historical forecasts;
12. settlement is separate from forecasting;
13. evaluation requires settled outcomes;
14. external providers cannot redefine domain identity;
15. repository ports do not dictate persistence technology;
16. application commands cannot silently bypass workflow stages;
17. domain errors are distinguishable from infrastructure failures;
18. no unresolved implementation convenience has been disguised as a domain decision.

## 17. Next controlled artifact

After this contract is reviewed and accepted, the next step should be a Domain Contract Review / ADR pass, followed by the actual Python package skeleton and contract-level tests.

Persistence should come after those contracts have executable tests.