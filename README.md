# MatchLens

**MatchLens** is a governed probabilistic football intelligence and forecasting platform focused on reliable, calibrated, and reproducible predictions.

The project treats football forecasting as a statistical and engineering problem rather than a certainty-producing prediction task. Its central output is a probability distribution accompanied by data-quality, calibration, uncertainty, and governance status.

## Current status

MatchLens is in **Foundation v0.1**. The project is establishing its governing principles, product contract, terminology, and authoritative working context before implementation begins.

No forecasting model or production application has been implemented yet.

## Core principles

- Probabilities, not guarantees.
- Calibration is as important as discrimination.
- Strong statistical baselines must be established before complex ML is justified.
- Evaluation must respect time ordering and prevent information leakage.
- The system must be able to abstain when data, model, or governance conditions are not satisfactory.
- Every material forecast must be reproducible and auditable.
- Model versions, data versions, feature definitions, evaluation windows, and calibration procedures must be traceable.
- Complexity must earn its place through measurable evidence.
- A failed hypothesis is a valid research result.

## Initial direction

The first research scope is football match outcome forecasting, initially centered on the 1X2 outcome space: Home win, Draw, Away win.

The exact competition, data providers, feature set, and model portfolio remain controlled design decisions.

## Governance posture

MatchLens is not initially designed as an autonomous betting or wagering system.

The system should be capable of returning states such as:

- **FORECAST** — required controls passed.
- **LOW CONFIDENCE** — a forecast is available, but reliability or uncertainty conditions warrant qualification.
- **NO FORECAST** — required information is unavailable or insufficient.
- **MODEL BLOCKED** — a data-quality, calibration, compatibility, drift, or governance control has failed.

## Project documentation

- [PROJECT_CONSTITUTION.md](docs/PROJECT_CONSTITUTION.md) — non-negotiable principles and governance rules.
- [PRODUCT_CHARTER.md](docs/PRODUCT_CHARTER.md) — product purpose, scope, users, non-goals, and success criteria.
- [MASTER_CONTEXT.md](docs/MASTER_CONTEXT.md) — authoritative working context, decisions, terminology, and current project state.

## Research posture

MatchLens will be judged by out-of-sample probabilistic performance, calibration, robustness, reproducibility, and governance quality—not by isolated headline accuracy.

If a sophisticated model fails to improve on simpler statistical baselines, that result will be recorded rather than hidden.

## License

No open-source license has been selected yet. Licensing and future commercialization are deliberate governance decisions.
