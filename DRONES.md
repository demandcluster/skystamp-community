# Drone compatibility

Status of telemetry support per model. "Confirmed" means verified against
real footage, including the embedded (no-SRT) telemetry track.

| Model | SRT captions | Embedded telemetry | Status | Notes |
|---|---|---|---|---|
| DJI Neo 2 | ✅ | ✅ (incl. attitude & gimbal) | **Confirmed** | Reference model; `.M4A` sidecar audio |

Models not listed are untested — SkyStamp may already work (DJI formats are
similar across the range), but nobody has confirmed it yet.
**[Report yours](../../issues/new?template=drone-report.yml)** — first
confirmed report per model earns a free Personal license.

Legend: ✅ verified · ⚠️ partial (see notes) · ❌ not working · — not present
on this model
