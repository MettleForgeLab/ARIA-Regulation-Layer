# Porosity Map Table — v1.0

---

## A) Porosity Map Table

| Location / Component | Porosity Type | Pathway | Violates Which Contract | Risk Level | Required Hardening |
|----------------------|---------------|---------|-------------------------|------------|--------------------|
| Chest Engine gating “reduce max tokens / slow sampling” | mid-event | Orchestration layer modifies decoding parameters in response to live metrics (“gate behavior”). If applied after decode start, it becomes mid-generation mutation. | Event, Cross-layer | High | Enforce: all gating decisions resolved pre-decode; forbid parameter mutation after decode start; log gating params at commit. |
| Chest Engine “routes or delays tasks based on readiness” | cross-event / external-state | Scheduler state may persist and influence next event without declared telemetry governance. | Kernel, Event, Signal | High | Treat scheduler outputs as explicit declared telemetry: version-pinned, deterministic, logged per event; no hidden carry-over. |
| SQLite “Continuity Buffer” (chest.db) | cross-event | Persistent sessions/turns/metadata can influence routing or readiness unless quarantined. | Kernel, Event, Signal | High | Quarantine chest.db from readiness/gain/mode computation unless declared deterministic artifact; prohibit implicit signal use. |
| Weave Observer file (weave_observer.jsonl) | external-state / cross-event | File tail is a time-series log; reading recent samples injects wall-clock drift unless normalized. | Signal, Event | High | Require deterministic sampling keyed to event_id; pin file version/hash per event; log sample window at commit. |
| Weave observer runner `--period 1.0 --samples 300` | external-state | Periodic wall-clock sampling; not replay-stable unless converted to deterministic artifacts. | Signal, Event | High | Exclude from readiness unless normalized to event-indexed sampling; otherwise classify as non-deterministic telemetry. |
| Elasticity daemon metrics endpoint (`/metrics`) | external-state | Metrics depend on CPU/IO/net timing; may vary under identical input. | Signal, Event | High | Only admissible if deterministic under pinned inputs; otherwise isolate and exclude or mark non-binding. |
| Router “length + tone heuristic” | cross-event / external-state | Routing may depend on prior model outputs; becomes implicit feedback if not logged. | Event, Cross-layer, Signal | Medium–High | Require deterministic routing function + versioned policy; log routing inputs and decision; forbid scalar cross-read. |
| Ache Engine continuous telemetry + time-of-day context | external-state | Time-of-day and seasonal inputs introduce clock dependence. | Signal, Event, Kernel | High | Normalize temporal signals; prohibit uncontrolled wall-clock reads mid-event; log temporal artifacts. |
| Ache computation domain weights (w1–w6) | cross-event | Weight mutation across events without versioning creates hidden state. | Kernel, Signal, Cross-layer | Medium–High | Require version-pinned weights; include parameter hash in commit record. |
| Pacing Engine time-of-day cadence mapping | external-state / mid-event | If cadence changes during decode, becomes mid-generation control mutation. | Event, Signal | High | Resolve pacing pre-decode; forbid cadence mutation mid-generation; normalize temporal inputs. |
| Pacing Engine latency observation (“dual-engine generation”) | mid-event / external-state | Adjusting controls based on observed latency mid-generation. | Event, Signal | High | Prohibit mutable latency reads during decode; no mid-generation control updates. |
| Tone Engine continuous adaptation | mid-event / external-state | Continuous adaptation risks post-decode modification of style constraints. | Event, Signal | Medium–High | Resolve tone constraints pre-decode; prohibit mid-generation tone recalculation. |
| Scroll Compiler exposed runtime surfaces | cross-event / mid-event | Mutable shared objects violate membrane if live-updated or retained across events. | Cross-layer, Event, Kernel | High | Require immutable snapshot per event; prohibit shared mutable references. |
| Scroll Loader runtime activation/deactivation | mid-event / cross-event | Module activation can mutate behavior mid-event or across events without versioning. | Event, Cross-layer, Kernel | High | Allow toggling only at event boundary via versioned config; log module set hash at commit. |
| Scroll Loader emotional/weather state transitions | external-state | Likely non-deterministic unless declared deterministic telemetry artifacts. | Signal, Event | Medium–High | Require deterministic provenance; otherwise exclude from readiness/mode. |
| Consolidation Layer UnifiedStateObject | cross-event / mid-event | Global cooldown persistence implies cross-event state retention. | Kernel, Event, Cross-layer | High | Partition USO by event_id; prohibit carry-over into ARIA kernel; log cooldown state per event. |
| CEIL reduces token count mid-stream | mid-event | Re-gating after decode start becomes mid-generation control mutation. | Event, Cross-layer | High | Finalize gates pre-decode; prohibit re-gating during decoding; log final constraints. |
| CEIL tone modification before fold merge | mid-event | Modifying streams during token emission risks mid-generation rewrite. | Event | Medium–High | Resolve tone constraints pre-decode; apply to prompt/context only. |
| Permission & Boundary Engine cooldown persistence | cross-event / mid-event | Boundary tightening may persist across events or gate mid-stream. | Event, Kernel | Medium–High | Enforce pre-decode boundary decisions; cooldown must be explicit policy and logged. |
| Breath index saves last β | cross-event | Explicit persistence of last β creates cross-event state retention. | Kernel, Event | High | Quarantine from readiness; persistence requires explicit declared telemetry artifact and logging. |

---

## B) Allowed vs Disallowed Summary

### Membrane-Safe (Allowed if Pre-Decode and Logged)

- Deterministic readiness computation using version-pinned signals  
- Pre-decode mode selection and gating resolution  
- Immutable configuration snapshot  
- Commit-time emission of routing, hashes, and telemetry  

### Must Be Quarantined or Disallowed for Readiness/Gain/Mode

- Mid-generation parameter mutation  
- Cross-event persistence influencing readiness  
- Wall-clock sampling pipelines  
- Time-of-day or seasonal inputs without normalization  
- Persistent scalar carry-over (e.g., save_last_β)  
- Shared mutable runtime objects  

---

## C) Minimal Hardening Checklist

### Event Membrane

- Decode start defines event boundary  
- No control mutation after decode start  
- Exactly one atomic commit per event  

### Pre-Decode Resolution

- All gating actions resolved pre-decode  
- Configuration snapshot hashed and frozen  

### Telemetry Discipline

- No wall-clock reads during event  
- Telemetry must be version-pinned and replayable  
- Periodic sampling excluded unless event-indexed  

### Persistence Quarantine

- chest.db and observer logs must not influence readiness unless declared and logged  
- No cross-event scalar carry-over  

### Routing Determinism

- Deterministic routing function  
- Logged routing inputs and decision  
- No cross-model scalar reads  

### Commit Logging Minimum

Each commit SHALL log:

- event_id  
- config version  
- parameter hash  
- telemetry sources (with hashes)  
- routing decision  
- final gating parameters  

### Porosity Monitoring

Any detection of:

- mid-event mutation  
- undeclared telemetry read  
- persistence leakage  

MUST trigger governance escalation.

---

End of Porosity Map — v1.0
