---
actual_minutes: 25
---
<!-- ai-generated: 100% - Antigravity generated the outcome -->

# METR n=1 replication

The original prediction for this feature was 90 minutes. However, with the help of an AI coding assistant, the implementation was completed much faster, taking only 25 minutes.

The AI assistant was able to quickly understand the complex logic required by the `METRIC-SPEC.md` document, including all 21 rules and the 6 edge cases. It generated the entire `metrics.py` calculation logic in a single shot, which correctly parsed the JSON lines log, applied the half-open observation window, extracted changes based on git commit history and revert chains, and computed all five DORA metrics (Deployment Frequency, Change Lead Time, Failed Deployment Recovery Time, Change Fail Rate, and Deployment Rework Rate).

Furthermore, the assistant correctly handled the gaming demonstration by recognizing that empty deployments (without any commits) still count towards the deployment frequency metric, allowing us to artificially inflate it while deliberately moving real deployments to the future to worsen the actual delivery times. This drastically reduced the time needed for trial and error.

Ratio actual/predicted: 0.28
