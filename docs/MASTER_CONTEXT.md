# MatchLens Master Context

**Status:** Foundation v0.1  
**Last updated:** 2026-09-28  
**Repository:** AbiemwenseMaureenOshobugie/matchlens  
**Default branch:** main

## 1. Canonical identity

**Project name:** MatchLens

**Canonical description:** A governed probabilistic football intelligence and forecasting platform focused on reliable, calibrated, and reproducible predictions.

The word "prediction" in the public description refers to probabilistic forecasting. MatchLens must not represent uncertain future events as guaranteed outcomes.

## 2. Current repository state

The repository is public, uses main as the default branch, has a Python .gitignore, and began with an initial README.

At Foundation v0.1 there is no forecasting implementation, dataset, model artifact, application package, or CI workflow.

## 3. Source-of-truth hierarchy

When project information conflicts, use this order:

1. PROJECT_CONSTITUTION.md — non-negotiable principles and governance.
2. PRODUCT_CHARTER.md — product purpose, scope, and success criteria.
3. MASTER_CONTEXT.md — current working state, terminology, decisions, and roadmap.
4. Architecture Decision Records and approved technical specifications, once introduced.
5. Implementation code and tests.
6. README.md — public summary derived from controlled project documents.

The README must not become a second source of truth.

## 4. Core methodological posture

MatchLens is a probabilistic forecasting system, not a binary outcome classifier marketed as certainty.

The initial target is pre-match 1X2 forecasting: Home, Draw, Away.

The system should ultimately produce a probability distribution plus a validity/governance status.

## 5. Initial architecture direction

**Data Sources → Ingestion → Data Quality → Feature Construction → Feature Validation → Forecasting → Calibration → Uncertainty → Governance → Forecast Status → Explanation/Audit → Monitoring**

The forecasting layer may contain multiple model families.

The governance layer must be capable of preventing downstream publication when required controls fail.

## 6. Expected model progression

1. Historical frequency/reference baselines.
2. Elo-style rating baseline.
3. Poisson goal model.
4. Dixon-Coles-style model or equivalent low-score correction.
5. Regularized statistical classification where justified.
6. Tree-based or other ML models where justified by the feature set.
7. Ensemble approaches.
8. Calibration and uncertainty methods.
9. Governance and monitoring integration.

This is a working direction, not permission to implement every model automatically.

## 7. Evaluation principles

Evaluation must preserve temporal causality.

Preferred methods include chronological train/validation/test splits, rolling or expanding walk-forward evaluation, controlled final holdout periods, and leakage checks.

Core evaluation dimensions are expected to include Log Loss, Brier Score, calibration diagnostics, reliability diagrams, discrimination metrics where relevant, stability across time, and subgroup analysis where justified.

Accuracy may be reported, but it is not the primary measure of probabilistic quality.

## 8. Governance concepts

The project will need explicit controls for data freshness, schema validity, semantic validity, entity identity, temporal availability, feature leakage, model compatibility, calibration status, model version, data version, drift, provenance, and auditability.

The system should fail closed when a blocking condition is detected.

## 9. Forecast status vocabulary

Current working vocabulary:

- FORECAST
- LOW_CONFIDENCE
- NO_FORECAST
- MODEL_BLOCKED

These names are provisional until formal domain contracts are introduced.

## 10. Key design boundaries

### Forecasting vs. betting
Forecast generation is the core problem. Betting or wagering decisions are downstream concerns.

### Model vs. governance
A model estimates probabilities. Governance determines whether the model is currently permitted to produce a governed forecast.

### Data vs. features
Raw records, normalized records, derived features, and model inputs are separate artifacts with separate provenance requirements.

### Explanation vs. evidence
A generated explanation may summarize recorded evidence, but must not invent evidence or override deterministic controls.

## 11. Initial non-goals

Do not prematurely implement autonomous betting, live wagering execution, broad multi-league support, a consumer dashboard before the forecasting contract exists, complex deep learning without baseline evidence, uncontrolled web scraping, proprietary-data assumptions without licensing decisions, or claims based on in-sample performance.

## 12. Open decisions

- first competition;
- first data provider(s);
- data licensing and redistribution boundaries;
- exact forecast timestamp/horizon;
- initial feature contract;
- exact baseline suite;
- calibration method;
- uncertainty methodology;
- model registry design;
- persistence architecture;
- application/API boundaries;
- deployment target;
- CI/CD policy;
- repository licensing model.

These must be resolved through explicit decisions rather than silently inferred during implementation.

## 13. Proposed development phases

### Phase 0 — Foundation
Project constitution, product charter, master context, terminology, decision process.

### Phase 1 — Data foundation
Provider research, data contracts, ingestion boundaries, provenance, validation, entity identity.

### Phase 2 — Statistical forecasting
Reference models, chronological evaluation, scoring, calibration analysis.

### Phase 3 — ML research
Feature engineering, ML model families, controlled comparison, ensemble research.

### Phase 4 — Governance
Forecast eligibility, model registry, blocking rules, audit records, drift and monitoring.

### Phase 5 — Intelligence product
User-facing forecast views, diagnostics, explanations grounded in system evidence.

### Phase 6 — Controlled expansion
Additional competitions, richer information, market research, or downstream decision-support only after evidence and governance review.

## 14. Immediate next step

The next controlled batch should define the **workflow specification** for:

**request → data snapshot → validation → feature snapshot → model selection → forecast → calibration → governance decision → publication/audit → outcome settlement → evaluation**

This workflow should be specified before the domain model and persistence layer are implemented.

## 15. Change discipline

Every material architectural decision should identify the decision, alternatives considered, rationale, consequences, status, and date/version where useful.

When implementation reveals a contradiction, stop and resolve the contract rather than patching around it silently.

## 16. Working principle

MatchLens should become stronger as evidence accumulates.

No component earns permanent status merely because it was implemented first. Models, features, data providers, and architectural choices remain subject to measurement, validation, and controlled replacement.
