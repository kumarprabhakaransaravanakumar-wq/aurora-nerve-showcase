# Evidence and limits

*Part of the [Aurora NERVE overview](../README.md): learned motion prediction for the Aurora spaceplane.*

This project treats "what we can show" and "what we hope to show" as different
categories and keeps them apart.

## Demonstrated in software

- Versioned sensor, observation, replay, step-audit and flight-log contracts.
- Causal forecast recording: predictions are stored before their outcomes.
- Independent offline trajectory scoring, with separate alignment and scoring
  intervals, demonstrated on complete synthetic logs.
- Refusal when a complete state is not observable from payload-local sensing.
- Independent, fail-closed assurance gates (confidence, ensemble disagreement,
  physics, finite-value, command and rate).
- Local monotonic scheduling, with network time kept out of scheduling decisions.
- Append-only, hash-chained recording of raw packets and per-tick decisions.
- Three interchangeable simulation plants behind one contract: an analytical
  surrogate, a 6-DoF JSBSim X-15, and a provenance-gated CFD-table plant.
- A test suite of over 1,200 tests, run with lint, type-checking and an
  environment-contract check before every change.

## Measured, with honest results

- **Simulator gyro-history study:** learned linear forecasts beat naive
  persistence on the X-15 surrogate. A benefit over *strong filtered* baselines is
  **not** established, and the project does not claim one.
- **A direct forecasting benchmark was recorded as failing its fixed criterion.**
  The failure was kept, not tuned away.
- **The learned control policy has not yet beaten the zero-residual baseline**
  (the scheduled controller acting alone). The project reports this openly.
- An evaluation-scenario defect was found that had made controller outcomes
  independent of the controller. It was fixed, and earlier results were declared
  non-comparable rather than reused.

## Not yet established

- Physical sensor capture under a qualified acquisition path.
- The trained flight predictor and calibrated uncertainty intervals.
- Flight hardware integration and environmental qualification.
- Any Aurora-representative aerodynamic or flight-dynamics model.
- Any flight benefit, pilot comparison, or performance above Mach 3.7.

## Why this matters

The value proposition for flight-test and instrumentation teams is evidence they
can trust when deciding whether a learned predictor deserves integration. That
includes the conditions where simpler methods do better.
