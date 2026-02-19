# MINIMUM_LAFORGE_SUBSTRATE_GUARANTEES_FOR_ARIA_HOSTING_v1.0

**Status:** Normative

**Scope:** Substrate-level guarantees required to host ARIA-compliant control execution on LaForge.

**Non-scope:** This document does not define gain scheduling, switching logic, signal admissibility, envelope mathematics, model architecture, or hardware implementation details.

---

## 1. Definitions

### 1.1 Decode Start

Decode start SHALL be defined as the first token emission from the selected model(s), not the invocation call.

### 1.2 Event Membrane

The event membrane SHALL span from decode start through atomic commit record write, inclusive.

### 1.3 Control Parameters

Control parameters include, at minimum: gain, readiness thresholds, routing decision, configuration snapshot, parameter block hash, seed, model selection, and gating variables.

---

## 2. Event Membrane Enforcement

- Decode start SHALL define the membrane boundary of the bounded event.
- No control parameter mutation SHALL occur after decode start.
- Exactly one atomic commit SHALL occur per event.
- No post-commit control mutation SHALL occur for that event.

---

## 3. Pre-Decode Configuration Freeze

- Configuration snapshot SHALL be resolved, hashed, and frozen prior to decode start.
- Configuration SHALL NOT mutate during event execution.
- Configuration hash SHALL be included in the commit record.

---

## 4. Seed Governance

- Seed SHALL be explicitly declared.
- Seed SHALL be version-pinned and logged at commit.
- Seed SHALL NOT be time-derived or auto-generated without explicit declaration and logging.
- Seed SHALL NOT mutate during event execution.
- If any seed derivation function exists, it SHALL be declared and versioned.

---

## 5. Deterministic Telemetry Discipline

- Telemetry used in readiness computation SHALL be deterministic under identical input and configuration.
- Identical input SHALL include: input text, configuration hash, parameter block hash, seed, and model versions.
- Wall-clock sampling SHALL NOT feed readiness unless normalized to event index and logged.
- Non-deterministic telemetry SHALL be classified and excluded from readiness unless explicitly declared, bounded, and logged as non-binding.

---

## 6. Persistence Quarantine

- Persistent stores (e.g., SQLite buffers, observer logs, unified state objects) SHALL NOT influence readiness, gain, or mode unless treated as explicit deterministic event artifacts.
- Any persistent artifact admitted as telemetry MUST be version-pinned and MUST have a hash logged at commit.
- Readiness (A) and gain (G) MUST NOT be inherited across events.

---

## 7. No Concurrent Mutation

- No shared mutable control state across threads or async loops is permitted during event execution.
- No concurrent mutation of control parameters is permitted during decode.
- All control computation SHALL occur in deterministic pre-decode phase.

---

## 8. Routing Determinism

- Routing logic SHALL be deterministic under identical input/configuration/seed.
- Routing decision and inputs SHALL be logged at commit.
- No model SHALL read another model’s internal scalar state.
- No routing decision SHALL be influenced by transient runtime performance metrics (e.g., instantaneous latency, token throughput) unless explicitly declared and logged as part of the configuration snapshot.

---

## 9. Commit Logging Minimum

Each commit record SHALL include, at minimum:

- Unique event identifier
- Timestamp (UTC)
- Model identifiers and model version metadata
- Configuration hash
- Parameter block hash
- Seed
- Final gating parameters (resolved pre-decode)
- Telemetry sources used (with hashes where applicable)
- Routing decision (if any)

---

## 10. Escalation Binding

- Any membrane violation (post-decode control mutation, undeclared telemetry read, persistence leakage into readiness) SHALL trigger governance escalation.
- Replay mismatch beyond tolerance SHALL escalate to Level 3 or above.
- Parameter mutation without version declaration SHALL escalate to Level 5 (Hard Halt).

---

## 11. Deterministic Replay Requirement

- Identical input, configuration hash, parameter block hash, seed, and model versions SHALL produce identical routing decision and identical gating parameters.
- Control outputs SHALL reproduce within declared tolerance.
- Token output MAY vary only as a consequence of probabilistic sampling and seed-controlled randomness; such variability is outside the control determinism guarantee.

---

End of Contract.

