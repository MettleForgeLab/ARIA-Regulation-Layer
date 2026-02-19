# Verification Standard (ARIA)

**Status:** Normative

**Version:** v1.0.0

**Layer:** ARIA Verification Layer

**Purpose:** Define the minimum requirements for compliance-grade verification artifacts in the ARIA repository. This standard governs *format, traceability, reproducibility,* and *claim discipline* for all verification documents and associated numeric artifacts.

**Scope:** Applies to all files under `verification/`.

**Non-scope:** This standard does not define architecture, philosophy, or runtime behavior. It defines how verification evidence is packaged and audited.

---

## 1. Repository Placement and Immutability

### 1.1 Namespace
All verification artifacts SHALL live under:

```
/verification/
```

### 1.2 Versioned Filenames
Each verification report SHALL be version-suffixed:

```
<artifact_name>_v<MAJOR>_<MINOR>.md
```

Example:

```
zeta_envelope_convergence_v1_0.md
```

Associated artifacts (trace, metrics, plots) SHALL share the same version stem.

### 1.3 Immutability Policy
A versioned verification artifact SHALL be immutable after publication.

- Corrections SHALL be issued as a new version (increment MINOR for clarifications, MAJOR for method changes).
- Previous versions SHALL remain in-repo.

---

## 2. Required Artifact Set

Each verification report SHALL include, at minimum:

1. A report file (`.md`)
2. A reproduction instructions file (`reproduction_instructions.md`) or report-embedded reproduction section
3. A metrics file (`*_metrics.csv` or `.json`)
4. A trace file (`*_trace.csv` or `.json`) when the claim depends on trajectory behavior
5. A plot (`*.png`) when the claim depends on convergence/shape behavior

Omission of any required artifact SHALL be explicitly justified in the report.

---

## 3. Artifact Binding Block (Mandatory)

Each report SHALL begin with an **Artifact Binding Block** containing:

```
ARIA Version: vX.X.X
CRS Version: vX.X.X
Artifact Version: v<MAJOR>.<MINOR>
Script Path: <relative path>
Script Hash (SHA-256): <hex>
Config Path: <relative path or 'inline'>
Config Hash (SHA-256): <hex>
Seed: <integer>
Run Timestamp (UTC): YYYY-MM-DDThh:mm:ssZ
Environment: <python version, OS, key deps>
```

Rules:
- Hashes SHALL be computed over the exact script/config used.
- If configuration is inline, `Config Path: inline` is permitted and the config block SHALL be included verbatim.

---

## 4. Parameter Declaration Block (Mandatory)

Each report SHALL include a **Parameter Declaration Block** listing:

- Targets (e.g., ζ_target)
- Envelopes (min/max)
- Rate limits
- Episode/run length and timestep
- Noise/load definitions and bounds
- Tolerances

Format SHALL be machine-copyable (code block).

Example:

```
zeta_target = 0.70
c_min = 0.05
c_max = 20.0
dc_max = 0.8
episodes = 30
episode_T_s = 8.0
dt_s = 0.002
tolerance = 0.03
seed = 42
```

Parameter blocks SHALL reflect the exact values used in the associated Artifact Binding Block.

External data dependencies MUST be version-pinned or explicitly excluded from verification scope.

---

## 5. Explicit Assumptions (Mandatory)

Each report SHALL state assumptions required for interpreting results. Assumptions MUST be operational, not rhetorical.

Minimum required categories:
- Envelope enforcement assumptions
- Deterministic control expansion assumptions
- Bounded disturbance assumptions
- Measurement/estimation validity assumptions

---

## 6. Claims and Scope Discipline (Mandatory)

Each report SHALL include a **Claims and Scope** section.

Rules:
- Claims SHALL be scoped to the control layers.
- Claims SHALL NOT imply semantic behavior follows physical dynamics unless proven.
- Claims SHALL NOT extend to model token sampling determinism.

Definition (Deterministic Control Expansion):

> Deterministic control expansion refers to deterministic expansion of control logic given identical configuration and seed, independent of probabilistic token sampling.

A required sentence (or equivalent) SHALL be present when using physical reference models:

> This verification does not claim that semantic behavior follows second-order physical dynamics; the reference plant is used to validate envelope behavior only.

---

## 7. Pass/Fail Criteria (Mandatory)

Each report SHALL define pass/fail criteria as explicit inequalities and bounded-time/episode requirements.

Requirements:
- Declare tolerance ε.
- Declare time/episode bound N for convergence.
- Declare envelope checks.
- Declare rate-limit checks.

Example:

- Convergence: |ζ_eff − ζ_target| ≤ ε within N episodes
- Envelope: c ∈ [c_min, c_max] for all steps
- Rate limit: |Δc| ≤ dc_max per update

---

## 8. Deterministic Replay Assertion (Mandatory)

Each report SHALL include a deterministic replay requirement.

Required language:

> Re-running the script with identical parameters, configuration, and seed SHALL produce identical control trajectories and metric outputs within the declared tolerance.

If bitwise identity is not feasible due to platform floating-point variation, the report SHALL:
- Define an acceptable numeric tolerance for replay equivalence,
- Justify the choice, and
- Declare the replay tolerance in the Parameter Declaration Block.

---

## 9. CRS Compliance Mapping (Mandatory)

Each report SHALL map verification elements to CRS compliance checklist items.

Minimum mapping categories:
- ζ-envelope tracking
- Rate limiting
- Deterministic control expansion
- No silent drift (i.e., no parameter or mode changes without explicit logging at commit) / audit completeness

Format: table.

---

## 10. Separation Rule: Architecture vs Verification

Verification artifacts SHALL remain numeric and evidentiary.

Rules:
- Verification files SHALL NOT introduce new architectural invariants.
- Verification files SHALL NOT revise runtime layer responsibilities.
- White papers SHALL NOT embed raw traces; they may link to verification artifacts.

Verification artifacts SHALL not contain rhetorical justification or architectural reinterpretation.

---

## 11. Regeneration Instructions (Mandatory)

Each verification report SHALL include, or reference, regeneration instructions:

- Command(s) to run
- Expected output files
- Expected metric ranges
- Location where artifacts will be written

---

## 12. Review Checklist (For Maintainers)

Before accepting a verification artifact into main:

- [ ] Versioned filename present
- [ ] Artifact Binding Block complete
- [ ] Parameter Declaration Block present
- [ ] Assumptions stated
- [ ] Claims scoped and non-overreaching
- [ ] Pass/fail criteria explicit
- [ ] Deterministic replay assertion included
- [ ] CRS mapping table included
- [ ] Associated artifacts present (trace/metrics/plot)
- [ ] Separation rule respected

---

## 13. Change Control

Changes to this standard require:

- A PR describing the rationale
- Version increment noted in the header
- Review by at least one governance reviewer and one systems reviewer

