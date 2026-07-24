# Drone compatibility

Status of telemetry support per model. "Confirmed" means verified against
real footage, including the embedded (no-SRT) telemetry track.

| Model | SRT captions | Embedded telemetry | Status | Notes |
|---|---|---|---|---|
| DJI Neo 2 | ✅ | ✅ (incl. attitude & gimbal) | **Confirmed** | Reference model; `.M4A` sidecar audio |

Models not listed are untested — SkyStamp may already work (DJI formats are
similar across the range), but nobody has confirmed it yet.
**[Report yours](../../issues/new?template=drone-report.yml)** — the first
three confirmed reports per model each earn a **Contributor license —
never expires, lifetime updates**.

Legend: ✅ verified · ⚠️ partial (see notes) · ❌ not working · — not present
on this model

File formats: `.MP4` and `.MOV` samples both welcome — both fully supported by the app.
