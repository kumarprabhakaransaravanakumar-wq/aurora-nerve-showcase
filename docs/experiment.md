# Study design: learned gyro prediction in flight

## Aim

Measure how far ahead a learned model can predict measured rotational motion
during high-speed flight, and whether its uncertainty identifies when its
forecasts fail. The intended output is a reproducible report of error,
availability, uncertainty and remaining lookahead across the conditions actually
flown. Positive, negative and inconclusive outcomes are all reportable.

## Target

The target is the payload's own later three-axis gyro reading. This measures
prediction of *sensor output*. Sensor bias, vibration and filtering effects are
kept in the result, not removed. Claims about vehicle body rate would also need a
verified mounting transformation.

The 0.375 s primary horizon lies in the middle of the proposed range. It is a
research operating point, not a measured latency requirement of any user.

## Questions and methods

| Question | Method |
|---|---|
| Does learning improve prediction? | Compare a compact GRU correction model and a ridge history model with raw persistence, filtered constant rate and causal trend. Choose the decisive conventional comparator on development data. Primary endpoint: paired per-flight gyro vector RMSE difference at 0.375 s on common evaluable origins, before confidence selection. Report per-axis and tail errors and missing forecasts. |
| How much lookahead is actually available? | Evaluate all horizons after measured sensing, filtering and issue delay. Target 90% issuance among sensor-valid, warmed-up origins, and also report availability over every scheduled origin. Compare methods at matched issuance. |
| What accuracy does it support? | Report the longest horizon that meets exploratory per-axis 95th-percentile error bands of 0.1, 0.5 and 1.0 deg/s. These bands are for sensitivity analysis only. Whether a result is useful depends on a user's own tolerance. |
| Is uncertainty informative? | Ensemble or quantile intervals calibrated on separate sessions. Measure coverage, width, confidently wrong cases and refusals against a conventional selector that uses recent causal errors. |

Known motion structure enters through causal rate extrapolation and rotation
kinematics. The learned correction is not presented as an identified aerodynamic
force or turbulence estimate.

## Data discipline

- Development data comes from physical bench sessions and explicitly labelled
  surrogate (JSBSim X-15) trajectories.
- Whole sessions and trajectories are split into fitting, selection, interval
  calibration and held-out evaluation *before* windows are created, so
  correlated windows cannot leak between splits.
- The flight model and protocol are frozen before flight. Flight outcomes supply
  no fitting data.
- Post-flight reference data (Mach, altitude, attitude, dynamic pressure) is
  used only for regime context and conditional independent attitude checks,
  never inside the onboard prediction chain.
- Local monotonic time drives scheduling. Network time is used only to associate
  logs, and alignment uncertainty is estimated and reported, not assumed.

## How results will be read

- Lower primary error on the common evaluation set supports predictive skill.
- Matched-issuance analysis separately assesses whether the system chose well
  *when* to predict.
- Tail regressions or poorly calibrated intervals qualify any headline gain.
- Logging alone is integration evidence, not scientific success.
- Correlated windows from 2–3 flights do not establish population-wide
  reliability, and the report will say so.

## Proposed campaign

2–3 boost-glide flights with an identical frozen configuration. Ground
commissioning comes first. The first flight evaluates the integrated system, the
second measures repeatability, and a third adds exposure if allocated. No special
manoeuvre or fixed supersonic duration is required.
