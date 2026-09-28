# MatchLens Product Charter

**Status:** Foundation v0.1  
**Product:** MatchLens  
**Category:** Probabilistic football intelligence and forecasting

## 1. Product purpose

MatchLens is intended to become a serious analytical platform for estimating and evaluating football match-outcome probabilities under explicit data, model, calibration, and governance controls.

A useful football forecast is not merely a predicted label; it is a calibrated probability estimate whose evidence, uncertainty, provenance, and validity status can be examined.

## 2. Problem

Football outcomes contain substantial uncertainty and information changes over time.

A forecasting system therefore needs trustworthy historical data, time-correct features, defensible statistical baselines, leakage-safe evaluation, calibrated probabilities, uncertainty and abstention behavior, reproducible model versions, and operational governance.

## 3. Intended users

The initial product direction serves football analysts, quantitative researchers, data scientists, performance and strategy researchers, technically sophisticated individual users, and potentially professional analytical teams later.

## 4. Initial scope

The initial research problem is pre-match football outcome forecasting.

The first outcome space is expected to be 1X2: Home win, Draw, Away win.

The first competition and data-provider set remain open decisions and will be selected based on data availability, quality, licensing, historical depth, and research value.

## 5. Core product capabilities

### Data foundation
- source ingestion
- normalization
- entity resolution
- temporal metadata
- data-quality validation
- provenance

### Forecasting
- statistical baselines
- machine-learning models
- justified ensemble methods
- probability generation
- calibration
- uncertainty characterization

### Governance
- forecast eligibility checks
- model registry and lifecycle states
- compatibility checks
- leakage controls
- drift monitoring
- audit trails
- explicit blocking/abstention

### Evaluation
- chronological or walk-forward validation
- controlled holdout testing
- proper scoring rules
- calibration analysis
- robustness analysis
- baseline comparison
- experiment tracking

### Presentation
- forecast probabilities
- forecast status
- model/version identity
- relevant data-quality and uncertainty indicators
- historical performance and calibration diagnostics

## 6. Forecast status model

| Status | Meaning |
|---|---|
| FORECAST | Required data, model, calibration, and governance checks passed. |
| LOW_CONFIDENCE | A forecast is available, but defined reliability or uncertainty conditions require qualification. |
| NO_FORECAST | Required information is unavailable or insufficient. |
| MODEL_BLOCKED | A governance, compatibility, calibration, data-quality, or monitoring control prevents model use. |

The exact decision rules are implementation contracts and must be defined before production use.

## 7. Non-goals

The initial product is not a guarantee of match outcomes, an autonomous betting system, a system that claims certainty from ML, a replacement for football expertise, a collection of unvalidated models, a scraping-first product, a mechanism for bypassing data-provider restrictions, or a marketing system that reports only favorable performance.

Betting-market analysis may become a downstream research area, but it is not the foundation of MatchLens.

## 8. Product success criteria

Success will be evaluated through:
1. probabilistic validity;
2. calibration;
3. out-of-sample performance using time-aware evaluation;
4. baseline discipline;
5. reproducibility;
6. governance and blocking capability;
7. robustness across meaningful time periods and distribution shifts;
8. operational traceability;
9. honest reporting.

No single metric is sufficient to define product success.

## 9. Research question

Can a governed forecasting system produce calibrated, reproducible, out-of-sample football match probabilities that remain useful across time and changing conditions, while providing demonstrable evidence relative to established statistical baselines?

This question is intentionally falsifiable.

## 10. Product evolution

**Foundation → Data foundation → Statistical baselines → Leakage-safe evaluation → Calibration → ML models → Ensemble research → Governance engine → Monitoring → User-facing intelligence → Controlled expansion**

Expansion requires evidence and explicit scope decisions.

## 11. Commercial posture

The project is initially open in development posture, but the architecture should not assume that all future code, data, models, or interfaces must remain freely redistributable.

Licensing, commercial use, proprietary data, and distribution boundaries are separate governance decisions.

## 12. Product decision rule

When choosing between a faster implementation and a more defensible foundation, MatchLens should prefer the option that preserves reproducibility, auditability, statistical validity, data provenance, controlled change, and clear boundaries.
