# MatchLens Forecasting Workflow Specification

**Status:** Proposed v0.1  
**Scope:** Pre-match 1X2 probabilistic forecasting  
**Authority:** Approved technical specification under the MatchLens source-of-truth hierarchy

## 1. Purpose

This specification defines the controlled lifecycle of a MatchLens forecast from request through post-match evaluation.

The workflow is:

**REQUEST → DATA SNAPSHOT → DATA VALIDATION → FEATURE SNAPSHOT → MODEL SELECTION → FORECAST GENERATION → CALIBRATION → UNCERTAINTY ASSESSMENT → GOVERNANCE DECISION → PUBLICATION/AUDIT → OUTCOME SETTLEMENT → EVALUATION**

The purpose is to establish contracts and invariants before domain models, persistence schemas, application services, or forecasting implementations are built.

## 2. Scope

### 2.1 Initial scope

The initial workflow supports:

- pre-match forecasts;
- a single football match as the forecast subject;
- the 1X2 outcome space: Home, Draw, Away;
- one explicitly defined forecast cutoff timestamp;
- versioned models and calibration procedures;
- governed publication and immutable audit records;
- post-match outcome settlement and retrospective evaluation.

### 2.2 Out of scope for this version

This specification does not yet define:

- a specific competition;
- a specific data provider;
- a specific database technology;
- a specific model implementation;
- a specific calibration algorithm;
- live/in-play forecasting;
- betting execution;
- automated wagering;
- player-level forecasting;
- multi-outcome markets beyond 1X2.

These may be introduced through later approved specifications.

## 3. Core terminology

### Match
A uniquely identified football fixture between a home team and an away team, associated with a competition and scheduled kickoff.

### Forecast cutoff
The timestamp at which the information set for a forecast is frozen.

Only information demonstrably available by the cutoff may influence the forecast.

### Forecast horizon
The relationship between forecast cutoff and scheduled kickoff. The initial system is pre-match, but the exact permitted horizon is a controlled configuration rather than an implicit assumption.

### Data snapshot
An immutable, identifiable representation of the source information available to the forecasting workflow.

### Feature snapshot
An immutable representation of the model-ready features derived from an identified data snapshot and feature definition/version.

### Model version
A uniquely identifiable forecasting model artifact/configuration approved or evaluated for a defined use.

### Calibration version
A uniquely identifiable calibration procedure and fitted calibration artifact applicable to a defined model/output regime.

### Forecast record
An immutable record of a forecast event, including its subject, timing, probability distribution, provenance, versions, governance result, and publication state.

### Outcome settlement
The controlled process of associating the completed match result with the forecast subject after the relevant result is available from an approved source.

### Supersession
The explicit relationship between forecasts for the same match and outcome space when a later forecast is generated. Supersession must not mutate the earlier forecast.

## 4. Forecast identity

A forecast is identified by at least:

- match identity;
- outcome-space identity;
- forecast cutoff timestamp;
- forecast generation event/version.

A model version is not the forecast identity.

A later forecast for the same match is a distinct forecast record even when it uses the same model.

No published forecast may be silently overwritten.

## 5. Information-time contract

Temporal integrity is a hard invariant.

**information availability time ≤ forecast cutoff**

The relevant timestamp is the time at which the information became available for use, not merely the time at which the record was retrieved by MatchLens.

Where a source provides only a retrieval timestamp and cannot establish underlying information availability, that limitation must be represented in provenance and may make the input ineligible for governed forecasting.

The workflow must distinguish at least:

- event/effective time;
- source publication or availability time where known;
- MatchLens retrieval time;
- forecast cutoff time;
- scheduled kickoff time.

A post-cutoff correction or update must not be used retroactively in the original forecast.

## 6. Workflow lifecycle

The workflow uses the following conceptual states:

1. **REQUESTED**
2. **DATA_SNAPSHOT_CREATED**
3. **DATA_VALIDATED**
4. **FEATURES_VALIDATED**
5. **MODEL_SELECTED**
6. **FORECAST_GENERATED**
7. **CALIBRATED**
8. **UNCERTAINTY_ASSESSED**
9. **GOVERNANCE_DECIDED**
10. **PUBLISHED** or **NOT_PUBLISHED**
11. **SETTLED**
12. **EVALUATED**

Blocking or terminal conditions may occur at multiple stages.

The exact persistence representation of these states is intentionally deferred to the domain model specification.

## 7. Stage contracts

### 7.1 Request

**Input**

A forecast request must identify:

- match;
- competition/context;
- scheduled kickoff;
- forecast cutoff;
- requested outcome space;
- requested forecast horizon or applicable policy;
- request context or correlation identifier where required.

**Output**

A validated forecast request with a unique request identity.

**Invariants**

- Match identity must be resolvable.
- Kickoff must be known or explicitly marked unavailable.
- Cutoff must be earlier than kickoff for the initial pre-match workflow.
- Outcome space must be supported.
- The request must have a deterministic interpretation.

**Blocking conditions**

- unresolved match;
- unsupported outcome space;
- invalid timestamps;
- cutoff at or after kickoff;
- missing mandatory request data.

### 7.2 Data snapshot

The workflow creates or selects an immutable data snapshot representing the information eligible for the forecast.

**Output**

- snapshot identity/version;
- source identities;
- source observation/retrieval metadata;
- effective/availability metadata where supported;
- schema/version information;
- provenance reference.

**Invariants**

- Snapshot contents must be reproducible.
- Snapshot identity must not change after forecast generation.
- Source/licensing restrictions must be respected.
- The snapshot must be associated with the forecast cutoff.

**Blocking conditions**

- incomplete snapshot;
- unresolvable provenance;
- unsupported source state;
- licensing/usage restriction that prevents the intended use;
- inability to establish temporal eligibility for required information.

### 7.3 Data validation

Validation is divided conceptually into independent controls.

**Schema validation** checks structural compatibility, required fields, types, and schema version.

**Semantic validation** checks whether values represent valid football-domain concepts and relationships.

**Identity validation** checks that teams, competitions, matches, and other entities resolve consistently.

**Completeness validation** checks required coverage and missingness against the approved data contract.

**Freshness validation** checks whether required information is sufficiently current for the forecast policy.

**Temporal validation** checks that no input violates the information-time contract.

**Provenance validation** checks that required source and transformation lineage exists.

**Output**

A machine-readable validation result containing individual checks, statuses, evidence references, and blocking/non-blocking classification.

**Invariant**

A forecast cannot proceed to governed publication if a blocking validation failure remains unresolved.

### 7.4 Feature snapshot

Features are constructed only from the validated information set.

**Output**

- feature snapshot identity/version;
- feature definition/version;
- source data snapshot identity;
- feature values;
- transformation/provenance metadata;
- temporal eligibility evidence.

**Invariants**

- Every model input must be traceable to source data.
- Feature construction must be deterministic for a fixed input/configuration where deterministic behavior is required.
- No feature may use information unavailable at the forecast cutoff.
- Feature definitions must be versioned.
- Missingness must be represented explicitly rather than silently imputed without policy.

**Blocking conditions**

- feature leakage;
- missing required features;
- incompatible feature definition;
- non-reproducible transformation;
- invalid feature values.

### 7.5 Model selection

Model selection is a governed compatibility decision, not an unrestricted runtime optimization.

The workflow must identify:

- model family;
- model version;
- training/data regime;
- applicable competition/context;
- applicable forecast horizon;
- required feature definition/version;
- model lifecycle state;
- evaluation/approval evidence;
- compatibility result.

A model marked candidate, blocked, or retired is not eligible for governed publication.

Runtime model selection must not silently choose a model based on future match information.

If a champion/challenger architecture is later introduced, its selection policy must be separately specified and versioned.

### 7.6 Forecast generation

The selected model receives the approved feature snapshot and produces a raw probability distribution.

For initial 1X2:

**P(Home) + P(Draw) + P(Away) = 1**

within a defined numerical tolerance.

**Output**

- raw probabilities;
- model version;
- feature snapshot identity;
- generation timestamp;
- numerical validation result.

**Invariants**

- Probabilities are finite.
- Probabilities are within valid bounds.
- Distribution is normalized within defined tolerance.
- Output shape matches the requested outcome space.
- Generation is reproducible to the project-defined standard given identical controlled inputs.

A model that produces an invalid probability distribution cannot proceed as a valid forecast.

### 7.7 Calibration

Calibration transforms or validates raw model probabilities using an approved, versioned calibration procedure.

**Output**

- calibrated probability distribution;
- calibration version;
- calibration applicability evidence;
- calibration validation result.

**Invariants**

- Calibration must be fitted/evaluated without using the forecast's future outcome.
- Calibration applicability must match the model/output regime for which it was approved.
- Calibrated probabilities must satisfy the same numerical validity requirements as raw probabilities.
- Calibration must not be silently skipped when the selected model requires it.

If the model has no approved calibration procedure and calibration is mandatory for its governed use, the forecast is blocked.

### 7.8 Uncertainty assessment

MatchLens must distinguish numerical probability from the reliability of the forecasting process.

This stage may assess:

- historical calibration quality;
- applicable out-of-sample performance;
- prediction-set or interval methods where later adopted;
- data completeness;
- model disagreement where explicitly defined;
- distribution-shift indicators;
- forecast-horizon limitations.

The system must not use vague confidence language without a defined operational meaning.

**Output**

A versioned uncertainty/reliability assessment with explicit evidence and thresholds.

**Important boundary**

"LOW_CONFIDENCE" is a governed classification, not permission to invent a subjective confidence percentage.

The exact uncertainty methodology and thresholds are deferred to a dedicated technical contract.

### 7.9 Governance decision

Governance evaluates whether the forecast may be published.

The governance decision must be deterministic from the defined controls and recorded evidence.

At minimum, it considers:

- request validity;
- data validation;
- temporal integrity;
- feature validation;
- model eligibility;
- model/data/feature compatibility;
- probability validity;
- calibration status;
- uncertainty/reliability status;
- provenance completeness;
- relevant monitoring/drift controls.

**Output**

A governance decision containing:

- decision;
- status;
- evaluated controls;
- blocking reasons, if any;
- governance-policy version;
- decision timestamp.

Initial status vocabulary:

- **FORECAST**
- **LOW_CONFIDENCE**
- **NO_FORECAST**
- **MODEL_BLOCKED**

A governance decision must never be inferred from a model score alone.

### 7.10 Publication and audit

Only forecasts that satisfy the publication policy may be published.

The published forecast must retain, directly or by immutable references:

- forecast identity;
- match identity;
- forecast cutoff;
- scheduled kickoff;
- outcome space;
- probability distribution;
- model/version;
- calibration/version;
- feature snapshot/version;
- data snapshot/version;
- governance decision;
- uncertainty/reliability assessment;
- creation/publication timestamps;
- provenance references.

The original forecast record is immutable.

Corrections are represented by explicit new records or controlled correction events, never by silently changing the historical forecast.

### 7.11 Outcome settlement

After the match has concluded, the system associates the approved result with the forecast subject.

Settlement must identify:

- result source;
- source version/reference where applicable;
- settlement timestamp;
- match completion status;
- resolved 1X2 outcome;
- correction/reconciliation state if the result is later changed.

The result source and settlement policy are separate from the forecasting model.

A forecast must not be scored until its outcome is considered settled under the approved policy.

### 7.12 Evaluation

Evaluation compares the forecast distribution with the settled outcome.

Initial evaluation should support:

- Log Loss;
- multiclass Brier Score;
- calibration diagnostics;
- reliability analysis;
- discrimination metrics where justified;
- temporal stability;
- subgroup analysis where justified;
- comparison with defined baselines.

Evaluation records must retain:

- forecast population;
- time period;
- forecast horizon;
- model/version;
- calibration/version;
- data/feature regime;
- metric definitions/version;
- evaluation methodology;
- result.

Evaluation must never retroactively modify the original forecast.

## 8. Failure and blocking policy

The workflow follows a fail-closed principle.

A failed control must have one of three explicit dispositions:

1. **blocking** — forecast cannot proceed to governed publication;
2. **non-blocking with qualification** — forecast may proceed with a defined qualification such as LOW_CONFIDENCE;
3. **informational** — retained for audit/monitoring but does not affect eligibility.

Every blocking result must have a machine-readable reason code.

Free-text explanations may supplement reason codes but must not replace them.

## 9. Reforecasting and supersession

Football information changes before kickoff. MatchLens must therefore support multiple forecasts for the same match.

Examples include forecasts generated after:

- updated team information;
- lineup availability;
- late injury/news information;
- data corrections;
- model/version changes under an approved policy.

A later forecast is a new immutable forecast event.

It may explicitly supersede an earlier forecast for a defined purpose, but the earlier record remains auditable.

The system must preserve:

- predecessor forecast identity;
- successor forecast identity;
- supersession reason;
- supersession timestamp;
- policy/version governing supersession.

The project does not yet prescribe a single persistence model for supersession.

## 10. Forecast publication policy

Publication is a separate concern from forecast generation.

A technically generated probability is not automatically a published MatchLens forecast.

At minimum:

- **FORECAST** may be published as a governed forecast.
- **LOW_CONFIDENCE** may be published only when the presentation policy explicitly supports qualified forecasts.
- **NO_FORECAST** represents absence of a valid forecast and must not be rendered as a probability estimate.
- **MODEL_BLOCKED** must not be presented as a valid model forecast.

The final user-interface representation is deferred.

## 11. Audit requirements

For every governed forecast, the audit trail should allow an independent reviewer to answer:

1. What match was forecast?
2. At what cutoff time?
3. What information was eligible at that time?
4. Which data snapshot was used?
5. Which features were used?
6. Which model/version produced the raw probabilities?
7. Which calibration/version was applied?
8. What probabilities were published?
9. What uncertainty/reliability assessment was recorded?
10. Which governance checks passed or failed?
11. When and why was the forecast published or blocked?
12. What outcome was eventually settled?
13. How was the forecast evaluated?

The audit record must be sufficient to reconstruct the decision path without relying on undocumented human memory.

## 12. Determinism and reproducibility

For controlled research and governed publication, the workflow must record enough information to reproduce the result to the project's defined tolerance.

At minimum this includes:

- code/repository version;
- data snapshot/version;
- feature definition/version;
- model/version;
- calibration/version;
- governance-policy version;
- configuration;
- forecast cutoff;
- environment/dependency identity where material.

Randomness must be controlled or recorded where stochastic model behavior affects the result.

## 13. Separation of responsibilities

The workflow intentionally separates:

**Forecasting** — estimates probabilities.

**Calibration** — improves or evaluates probability reliability under an approved procedure.

**Uncertainty assessment** — characterizes reliability limitations.

**Governance** — decides whether the resulting artifact is eligible for publication.

**Presentation** — communicates an already-governed result.

No presentation or generative-AI layer may bypass governance.

## 14. Security and data-use constraints

The workflow must not require credentials or secrets to be embedded in source code or forecast records.

Data-provider terms, licensing, redistribution rights, privacy requirements, and access restrictions are part of data governance.

A technically retrievable data point is not automatically an authorized production input.

## 15. Deferred technical decisions

This workflow deliberately leaves the following for later specifications:

- competition/provider selection;
- precise forecast-horizon policy;
- source priority and conflict resolution;
- exact data-quality thresholds;
- feature contract;
- model registry schema;
- calibration algorithm and fitting policy;
- uncertainty methodology;
- governance reason-code catalogue;
- publication policy details;
- settlement source hierarchy;
- persistence/schema design;
- API boundaries;
- experiment-tracking design.

These decisions must not be silently embedded in implementation.

## 16. Acceptance criteria for this specification

Before implementation of the domain model, this specification should be accepted only if:

1. each workflow stage has defined inputs and outputs;
2. temporal availability is explicitly distinguished from retrieval time;
3. invalid or unverifiable information can block publication;
4. raw and calibrated probabilities are distinguishable;
5. model and calibration versions are independently traceable;
6. governance is separated from model inference;
7. forecasts are immutable;
8. reforecasting does not overwrite history;
9. outcome settlement is separate from forecast generation;
10. evaluation is performed against settled outcomes;
11. every blocking condition has a machine-readable representation;
12. the workflow can support future extensions without weakening the initial temporal and governance invariants.

## 17. Next specification

Once this workflow is accepted, the next controlled artifact should define the **MatchLens domain contracts**.

That specification should derive the domain objects, value objects, enums/statuses, identifiers, invariants, and application-port boundaries from this workflow.

Persistence should follow the domain contract rather than define it retroactively.
