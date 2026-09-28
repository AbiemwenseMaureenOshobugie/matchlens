# MatchLens Project Constitution

**Status:** Foundation v0.1  
**Role:** Non-negotiable project principles and governance rules

## 1. Purpose

MatchLens exists to develop a serious, reproducible, and governed system for probabilistic football forecasting and intelligence.

The project distinguishes between estimating probabilities, evaluating whether those probabilities are reliable, and deciding whether the system is permitted to issue a forecast.

A model producing a number does not, by itself, authorize publication of that number as a valid forecast.

## 2. Probabilistic integrity

MatchLens shall represent uncertainty explicitly. Probabilistic forecasts are not guarantees or certainties.

For mutually exclusive 1X2 outcomes, published probabilities must form a valid distribution within defined numerical tolerances.

Point predictions may be displayed for usability, but must never replace the underlying probability distribution.

## 3. Calibration is first-class

MatchLens shall evaluate probabilistic accuracy, calibration, sharpness where appropriate, discrimination where appropriate, and stability across time.

Proper scoring rules such as Log Loss and Brier Score are expected to be central evaluation measures.

Calibration procedures must themselves be versioned and evaluated out of sample.

## 4. Temporal integrity and leakage prevention

Training, validation, calibration, feature construction, and testing shall respect information available at the forecast timestamp.

Future information must not enter historical features, labels, preprocessing, model selection, calibration, or evaluation.

Suspected leakage is release-blocking until investigated and resolved.

Random train/test splitting is not an acceptable default for final time-dependent evaluation.

## 5. Baseline discipline

Complexity must be justified by evidence.

Before a complex ML model is accepted as a meaningful improvement, MatchLens shall establish appropriate simpler references, potentially including historical frequency, Elo-style ratings, Poisson goal models, Dixon-Coles-style models, and regularized statistical classification.

A sophisticated model that does not improve materially over a strong baseline remains a valid result.

## 6. Abstention and fail-closed behavior

MatchLens shall be allowed to refuse to issue a forecast.

Blocking conditions may include missing or stale data, schema or semantic failure, unresolved identity, temporal leakage, incompatible versions, failed calibration controls, unacceptable drift, or incomplete provenance.

When a blocking condition exists, the system shall prefer an explicit blocked state over silently producing a forecast.

## 7. Data governance

Every production-quality input must have traceable provenance.

Where appropriate, the system shall retain source identity, retrieval or observation time, effective time of underlying information, transformation history, schema/version information, and data-quality status.

Raw data, normalized records, derived features, and model-ready inputs are separate artifacts with separate provenance requirements.

## 8. Reproducibility

Material research results shall be traceable to code version, data version or immutable reference, feature definition, model configuration, training window, evaluation window, calibration method, and relevant environment information.

If a result cannot be reproduced to the defined standard, it must not be treated as fully verified.

## 9. Model governance

Every governed model shall have an identifiable version and lifecycle state: candidate, evaluated, approved for a defined use, blocked, or retired.

Approval for one competition, horizon, or data regime does not automatically imply approval for another.

## 10. Evaluation integrity

Model selection and tuning should use designated development and validation procedures. A final holdout period should be treated as a controlled research artifact.

Performance claims must identify the population, time period, forecast horizon, and evaluation methodology.

## 11. Monitoring and drift

Historical performance does not guarantee future validity.

MatchLens shall eventually monitor relevant changes in data quality, feature distributions, outcome distributions, calibration, scoring performance, missingness, source behavior, and model compatibility.

## 12. Auditability

Material forecast events should be auditable: what was forecast, when, for which match, what information was available, which model/version produced it, which data and feature versions were used, what probabilities were produced, what calibration was applied, and which governance checks passed or failed.

## 13. Separation of concerns

Data ingestion, validation, feature construction, modeling, calibration, governance, presentation, and downstream decision support should have explicit boundaries.

No user interface should bypass model or governance controls.

Generated explanations must not invent evidence or override deterministic controls.

## 14. AI and machine learning

Machine learning is a means, not the definition of the project.

Every ML component must have a defined research purpose, valid baseline comparison, leakage-safe evaluation, documented inputs and outputs, versioned configuration, known limitations, and a governance state.

Generative AI, if introduced, must not override deterministic data, statistical, risk, or governance controls.

## 15. Human agency and downstream use

MatchLens provides analytical information. It does not determine what a user should do with that information.

If betting or other consequential decision support is added later, it must be a separately governed layer.

## 16. Security, privacy, and legal constraints

Credentials and tokens must never be committed to the repository.

Data acquisition and use must respect applicable provider terms, licensing restrictions, privacy requirements, and other legal constraints.

Technically accessible data is not automatically permissible to use or redistribute.

## 17. Change control

Material changes to project purpose, scope, governance posture, or core methodological principles must be documented explicitly.

Implementation must not silently alter the constitution's intent.

## 18. Scientific honesty

MatchLens shall not hide weak results, cherry-pick favorable periods, present in-sample performance as out-of-sample evidence, imply causality from predictive association, use unsupported confidence language, or claim superiority without an appropriate comparison.

Negative results, failed models, blocked forecasts, and discovered data problems are useful evidence when documented correctly.

## 19. Priority

When implementation convenience conflicts with these principles, implementation must yield.

When a requirement is undefined, the project must identify the decision explicitly rather than inventing an implicit rule.

This constitution governs the project until formally superseded by a documented revision.
