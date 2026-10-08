# Aurora NERVE — Learned Motion Prediction in Supersonic Flight

**Aurora NERVE** is a research programme on *trustworthy learned prediction for
high-speed flight*: how far ahead a learned model can predict an aircraft's
rotational motion, whether it beats strong conventional predictors, and whether
it knows when its own predictions should not be trusted.

It was proposed in 2026 as a passive, shadow-mode payload concept for the
**Dawn Aerospace Suborbital Spaceplane Challenge**, flying aboard the Aurora
suborbital spaceplane (up to Mach 3.7).

> This is a public overview repository. The full research framework (simulation,
> world-model training, runtime assurance, recording and scoring software) is
> maintained privately. This repository shares the study design, architecture and
> selected demonstration material only.

![Proposed experiment architecture](assets/architecture-diagram.svg)

## The research question

> Can learning extend useful short-horizon motion prediction beyond strong causal
> baselines, and can the system identify when changing flight conditions make
> those predictions unreliable?

Two things are tested, not one:

1. **Prediction benefit.** Does a learned predictor reduce forecast error against
   the strongest conventional method, not just against a naive one?
2. **Reliability of the decision to trust a prediction.** A model that predicts
   well but is confidently wrong in unfamiliar conditions fails. A model that
   refuses to predict everything also fails.

## The proposed flight experiment

- A **passive payload** records its own three-axis gyro and predicts its future
  readings. It never commands the vehicle and has no connection to its actuators.
- **Primary comparison:** gyro vector RMSE at a **0.375 s** horizon; 0.125 s and
  1 s are secondary.
- **Candidates:** a compact recurrent (GRU) correction model and a regularized
  linear history model.
- **Conventional comparators:** raw persistence, causally filtered constant rate
  and causal trend. The decisive comparator is selected on development data, before
  any flight.
- **Pre-registration by construction:** every prediction, uncertainty interval,
  refusal and issue time is recorded *before* its outcome exists.
- **Frozen evaluation:** models, preprocessing and calibration stay fixed across a
  requested 2–3 flight campaign. Flight data is used only for evaluation, never
  for fitting.
- **Fair scoring:** errors are compared at matched prediction availability, so a
  method cannot look good by refusing the hard cases.

See [docs/experiment.md](docs/experiment.md) for the full study design.

## Runtime assurance: learned output is never trusted on its own

The broader programme also studies model-based reinforcement learning for flight
control, in shadow mode only. A Dreamer-style world-model policy proposes a small
**bounded residual (±0.15)** around an inspectable scheduled baseline controller.
Deterministic, fail-closed gates then decide what *would* have been commanded:

```text
sensor packet
  → state estimator (freshness, health, confidence)
  → versioned observation
  → world-model policy proposes a bounded residual
  → scheduled baseline controller
  → runtime assurance: confidence · ensemble disagreement · physics consistency
                       · finite values · command and rate limits
  → shadow safety gate
  → append-only, hash-chained flight recorder
```

See [docs/runtime-assurance.md](docs/runtime-assurance.md).

## Simulator replay demo

[`demo/replay.html`](demo/replay.html) is a self-contained replay of a simulator
integration run. It shows the runtime making decisions tick by tick, including
estimator-confidence refusals and quarantined ticks. It runs on the stock JSBSim
X-15 surrogate. It is software-integration evidence. It does **not** demonstrate
the proposed learned gyro predictor or any Aurora flight performance.

Download the file and open it in a browser, or view it on the project page if
GitHub Pages is enabled.

## Status: what is established and what is not

This project keeps a strict line between evidence and intent.
[docs/evidence-and-limits.md](docs/evidence-and-limits.md) covers it in full.

**Implemented and tested in software:** versioned sensor and observation
contracts, causal forecast recording, independent offline scoring (shown on
synthetic logs), refusal paths, fail-closed assurance gates and hash-chained
recording. The full test suite has over 1,200 tests.

**Not yet established:** physical sensor capture, the trained flight predictor,
calibrated uncertainty intervals, flight hardware qualification, and any
benefit of learning over strong filtered baselines. No result here is
Aurora-representative. The X-15 simulator is a surrogate. Nothing here is
hypersonic.

## Keywords

learned motion prediction · gyro forecasting · supersonic flight · transonic ·
suborbital spaceplane payload · flight-test instrumentation · runtime assurance ·
shadow-mode flight control · model-based reinforcement learning · world models ·
Dreamer · RSSM · selective prediction · uncertainty quantification · JSBSim ·
safe reinforcement learning · aerospace machine learning

## Author

**Kumar Prabhakaran Saravanakumar**, independent researcher (Canada), the
originator and developer of Aurora NERVE's prediction, simulation, assurance and
recording software.

For collaboration or research enquiries, open an issue on this repository.

## Citation

If you reference this work, see [`CITATION.cff`](CITATION.cff) or use GitHub's
"Cite this repository" button.

## License and notices

Text, diagrams and the demo in this repository are licensed under
[CC BY-NC-ND 4.0](LICENSE). The private framework is not licensed for use.

Aurora is a vehicle of Dawn Aerospace. This repository is an independent
research overview. It is not affiliated with or endorsed by Dawn Aerospace and
contains no Dawn documentation, data or imagery. Selection, flight and any results
are not implied.
