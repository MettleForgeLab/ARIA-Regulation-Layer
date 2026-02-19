# RUNTIME_SIGNAL_PROVENANCE_STANDARD_v1.0

---

## 1. Purpose

This standard defines the structural and governance requirements for any signal (s_i) used in ARIA readiness computation.

This document governs instrumentation discipline only. It introduces no new invariants and does not define control law behavior.

---

## 2. Definition of Signal

A signal (s_i) is a scalar measurement used as input to the ARIA readiness computation:

A = Σ w_i |s_i|

A signal is defined as a bounded, normalized, externally attributable measurement derived from declared runtime inputs.

---

## 3. Signal Admissibility Criteria

A signal SHALL be admissible only if it:

- Is externally observable or computable from explicitly logged event artifacts or declared telemetry sources.
- Is bounded within a declared range.
- Is normalized to a declared closed interval.
- Is deterministic under identical input and configuration.
- Is version-pinned.
- Declares its update frequency.
- Declares its failure behavior.

A signal SHALL NOT:

- Be derived directly or indirectly from readiness (A) or gain (G).
- Encode internal psychological states.
- Depend on non-normalized system clock values.
- Depend on stochastic external input unless explicitly declared and isolated.
- Mutate silently across events.
- Be derived from non-adjacent layer internals.

---

## 4. Required Signal Declaration Block

Each signal SHALL declare the following metadata:

```
Signal Name: s_i
Unit:
Source Layer:
Sampling Method:
Normalization Function:
Range:
Update Frequency:
Determinism Classification:
Trust Classification (SHALL be drawn from a predefined, versioned trust taxonomy):
Failure Mode:
Tolerance (SHALL define maximum permitted deviation under deterministic replay):
Version:
```

All fields are mandatory.

---

## 5. Normalization and Scaling Requirements

- Each signal SHALL define its normalization function explicitly.
- The normalization function SHALL produce output within a declared closed interval.
- The declared range SHALL match the normalization output range.
- Saturation behavior SHALL be defined.
- Normalization SHALL be deterministic under identical input.

Signals contributing to readiness SHALL be scaled consistently with declared weight governance.

---

## 6. Determinism and Replay Requirements

- Signal computation SHALL be deterministic under identical input, configuration, and seed.
- Replay under identical input SHALL reproduce identical signal values within declared tolerance.
- Any stochastic dependency SHALL be classified and logged.
- Non-deterministic telemetry SHALL be excluded from readiness computation unless explicitly declared and governed.

---

## 7. Telemetry Isolation and Time Dependency Rules

- Telemetry sources SHALL be version-pinned.
- Time-dependent telemetry SHALL declare normalization strategy.
- External clock dependencies SHALL be normalized and logged.
- Signals SHALL not depend on uncontrolled system load, wall-clock time, or network variability unless explicitly declared and bounded.
- Telemetry ingestion SHALL not expose non-adjacent layer internals.

---

## 8. Failure Handling and Dropout Policy

Each signal SHALL define:

- Failure Mode (e.g., zero-fill, hold-last, reject-event)
- Dropout detection criteria
- Tolerance thresholds

Failure handling SHALL be deterministic.

Silent signal disappearance is prohibited.

---

## 9. Prohibited Signal Categories

The following signal types are prohibited:

- Signals encoding internal cognitive or psychological inference.
- Signals derived from undocumented intermediate state.
- Signals dependent on unbounded stochastic processes.
- Signals computed from non-versioned external services.
- Signals that bypass declared normalization.

---

## 10. Logging and Provenance Requirements

At commit, the following SHALL be logged for each signal:

- Signal Name
- Version
- Normalized Value
- Raw Value (if permissible)
- Source Layer
- Determinism Classification
- Trust Classification
- Tolerance

Signal provenance SHALL be included in commit record.

No signal contribution may be hidden.

---

## 11. Compliance Checklist

A signal is compliant if:

- Declaration block is complete.
- Normalization function is explicit and bounded.
- Determinism classification is declared.
- Replay reproducibility holds within tolerance.
- Failure behavior is declared and deterministic.
- No prohibited categories are present.
- Provenance is logged at commit.

---

End of Standard.

