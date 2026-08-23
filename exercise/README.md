# Official Exercise Operations — Scenario 01

This folder defines how the live Scenario 01 exercise is run without leaking attacker ground truth to the defender.

- [`BLIND-EXERCISE-PROTOCOL.md`](BLIND-EXERCISE-PROTOCOL.md) — information separation, execution gates, role boundaries and reveal process.
- [`final-comparison-template.md`](final-comparison-template.md) — post-exercise attacker/detection/AI/SOC/IR comparison.

The live exercise is intentionally **blind from the SOC perspective**. Detection Engineering is frozen before execution, the attacker operates from outside the defender account, and the Project Lead reveals ground truth only after the SOC disposition is locked.
