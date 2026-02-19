# Appendix A — ζ-Envelope Convergence Verification Under Synthetic Load

---

## A.1 Purpose

This appendix provides a quantitative verification of the ζ-targeted damping mechanism used in the banded cadence controller.

The objective is to demonstrate:

- The gain scheduling logic drives the effective damping ratio toward a specified target.
- The damping parameter remains bounded and rate-limited.
- Convergence occurs under synthetic load perturbations.
- No silent parameter drift or uncontrolled escalation occurs.
- Deterministic replay reproduces the same control trajectory.

---

### Verification Artifacts

- ζ convergence plot  
  `zeta_convergence_episodic.png`

- Episode trace (full data)  
  `zeta_convergence_episodic_trace.csv`

- Summary metrics  
  `zeta_convergence_episodic_metrics.csv`

All artifacts are reproducible under declared configuration and seed.

---

## A.2 System Model (Reference Only)

The synthetic test uses a normalized second-order plant:

