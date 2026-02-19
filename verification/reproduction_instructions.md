# Reproduction Instructions — ζ Envelope Convergence (Episodic)

## Purpose
Reproduce the ζ-envelope convergence artifacts for Appendix A (episodic ringdown demonstration).

## Inputs and Parameters
```
zeta_target = 0.7
tolerance = 0.03
m = 1.0
wn = 6.283185307179586
k = 39.47841760435743
c_min = 0.05
c_max = 20.0
dc_max = 0.8
episodes = 30
episode_T_s = 8.0
dt_s = 0.002
noise_sigma = 0.003
seed = 3
step_amp_schedule = 1.0 +0.6 (episodes 10-15) +0.8 (episodes 20-25)
```

## Environment
```
python = 3.11.2
platform = Linux-4.4.0-x86_64-with-glibc2.36
numpy = 1.24.0
pandas = 2.2.3
matplotlib = 3.7.5
```

## Steps
1. Run a script implementing the episodic ringdown method with the parameter block above.
2. Ensure the RNG seed is set to `seed = 3` and no additional sources of nondeterminism are introduced.
3. Confirm the script writes the following files:
   - `zeta_convergence_episodic.png`
   - `zeta_convergence_episodic_trace.csv`
   - `zeta_convergence_episodic_metrics.csv`

## Deterministic Replay Requirement
Re-running the script with identical parameters and seed SHALL produce identical control trajectories and metric outputs within the declared tolerance (`tolerance = 0.03`).

## Expected Outputs (Metrics)
- target_zeta: 0.7
- tolerance: 0.03
- episodes: 30.0
- episode_T_s: 8.0
- dt_s: 0.002
- first_episode_reaching_tol: 8.0
- first_episode_staying_within_tol: 20.0
- final_zeta_eff: 0.7237049552003549
- max_zeta_eff: 0.7340124448926508
- min_zeta_eff: 0.21366197723675812
- final_c: 9.094344682495862
- dc_max_per_episode: 0.8
- seed: 3.0

## Output File Hashes (SHA-256)
- zeta_convergence_episodic.png: 7e4486a79a08e087669f06807bcfbd85093eb5359d2f52d9987f5d51bf3f6445
- zeta_convergence_episodic_trace.csv: 17996a8891a7408f937970a4eb6964befdc32bd51ae2748b4f41af03fb9fdbdd
- zeta_convergence_episodic_metrics.csv: fe56bc29b55caa8270c100b0cbbb25eb2013a52eb5f4d6d910ac73c607cd0398

## Notes on ζ_hat
`zeta_hat` is computed via peak log-decrement using the first two detected peaks of |x| after step release. Under near-critical damping, peak detection may become sparse; ζ-envelope enforcement is assessed using `zeta_eff` (derived from c).
