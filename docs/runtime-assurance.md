# Runtime assurance for learned flight software

*Part of the [Aurora NERVE overview](../README.md): learned motion prediction for the Aurora spaceplane.*

Aurora NERVE's software principle is that **learned output is never trusted
without a deterministic gate.** Everything runs in shadow mode. The system
records what it *would* have commanded and has no actuator connection.

## Command construction

```text
recorded command = scheduled baseline + confidence-scaled learned residual
```

- **Scheduled baseline:** an inspectable, gain-scheduled conventional controller.
  It is a two-loop longitudinal cascade with rate-limited, clamped, anti-windup
  integrators.
- **Learned residual:** a small correction proposed by a Dreamer-style world-model
  (RSSM) policy, hard-bounded to **±0.15** of normalized authority. The learned
  component can never become the sole source of a command.

## Independent, fail-closed gates

Each learned proposal is adjudicated on one path, which the runtime and the
simulator evaluation share:

| Gate | Rejects when |
|---|---|
| Sensor health and freshness | inputs are stale, missing or flagged unhealthy |
| Estimator confidence | the state estimate is not trustworthy enough |
| Latent confidence and ensemble disagreement | the world model is outside what it knows |
| Physics consistency | standard identities disagree (e.g. `q = ½ρV²` vs `q = (γ/2)pM²`, ISA relations, specific energy) |
| Finite-value | any output is NaN or infinite |
| Command and rate limits | the composed command exceeds bounds or slews too fast |

Each gate is independent and fails closed. Every decision records the proposal,
gate result, reason and final hypothetical command.

## Behaviour that matters in practice

- **Unobservable is a valid answer.** When payload-local sensing cannot supply a
  complete state (IMUs do not measure Mach, dynamic pressure, angle of attack or
  sideslip), the runtime refuses. It never manufactures the missing fields.
- **Recovery after gaps.** A data gap ends the recurrent latent segment instead of
  folding an arbitrary interval into one transition. A restarted latent stays
  refused until it has re-absorbed history, under its own refusal reason.
- **Qualification on serving authority.** Training is also evaluated through the
  same adjudication the runtime uses, so a policy is never promoted on authority
  the payload would not grant it.
- **Evidence integrity.** Raw packets and per-tick decisions go to an append-only,
  hash-chained recorder.
- **Versioned contracts.** Observation and reward versions are checked on resume.
  A mismatch invalidates checkpoints rather than silently mixing data.

## Timing

On a development host (Windows 11, 12-core Intel CPU, PyTorch on CPU), a whole
decision tick costs about **7.3 ms median and 36.7 ms worst case** against a
25 ms (40 Hz) period. The tick covers estimation, observation, policy, baseline,
gates and recording. The learned policy path is only about 1.6–2.3 ms of that.
This is development-host evidence, not a guarantee on flight hardware.

## Scope

The physics checks test identities, plausible ranges and trends. They are not an
aerodynamic model of any vehicle. Simulator command comparisons are diagnostics
and do not measure agreement with any real pilot or autopilot.
