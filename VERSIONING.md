# Versioning Policy — ARIA Regulation Layer

ARIA follows semantic versioning:

MAJOR.MINOR.PATCH

## MAJOR

Incremented when:

- Kernel control law changes
- Event membrane semantics change
- Cross-layer contract rules change
- Signal admissibility rules change
- Escalation ladder structure changes

MAJOR changes may require updated verification artifacts.

## MINOR

Incremented when:

- New contracts are added
- Verification artifacts are extended
- New optional modules are introduced
- Documentation clarifies existing behavior without altering control semantics

## PATCH

Incremented when:

- Typographical corrections
- Clarification edits
- Non-functional documentation adjustments
- Verification artifact regeneration under identical parameters

---

## Verification Binding

Each MAJOR or MINOR release SHALL:

- Include updated parameter hashes
- Maintain replay compatibility documentation
- Preserve prior verification artifacts or version them accordingly

---

## Stability Classification

v1.x.x indicates stable governance-layer behavior.

Pre-1.0 releases indicate experimental or draft status.
