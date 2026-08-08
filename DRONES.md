# Drone compatibility

Status of telemetry support per model. **Confirmed** means verified against
real footage — a full clip, both the `.SRT` captions and the embedded
(no-SRT) telemetry track, with an export actually rendered.

Anything short of that is listed honestly below, because a model marked
confirmed stops getting samples, and samples are the only way this list
grows.

## Two telemetry sources, and they differ in how portable they are

SkyStamp reads a clip two ways, and the distinction decides what we can
promise without your footage:

- **`.SRT` captions** — text, written only when Video Captions was enabled.
  There are about six known layouts across the DJI range and we now parse
  all of them, so a new model's captions usually work on arrival.
- **Embedded telemetry** — DJI's `djmd` data stream inside the MP4, present
  whether or not captions were on, and richer (attitude, gimbal). This one
  is **a different binary schema per model** (`dvtm_NEO2.proto`,
  `dvtm_Air3s.proto`, …), and DJI publishes none of them. Field positions
  have to be reverse-engineered and flight-verified per model — which is
  why a real `.MP4` is worth far more to this list than a pasted `.SRT`.

So "the caption format parses" is a much weaker claim than "the model is
supported", and the table keeps them apart.

## Status

| Model | SRT captions | Embedded telemetry | Status | Notes |
|---|---|---|---|---|
| **DJI Neo 2** | ✅ | ✅ incl. attitude & gimbal | **Confirmed** | Reference model; `.M4A` sidecar audio |
| DJI Neo (first generation) | ❓ untested | ❓ untested | Needs sample | **Not the Neo 2.** No Neo footage or caption sample has reached this project at all — see below |
| DJI Mini 3 Pro / Mini 4 Pro | ⚠️ format parsed | ❓ untested | Needs sample | Bracket layout; aperture written ×100 (`170` = f/1.7) |
| DJI Mavic Air 2 / DJI FPV | ⚠️ format parsed | ❓ untested | Needs sample | Same bracket layout as above |
| DJI Mavic 3 / Air 2S / Air 3 | ⚠️ format parsed | ❓ untested | Needs sample | `SrtCnt` counter; aperture ×100, focal length ×10 |
| DJI Mavic 3E (Enterprise) | ❓ untested | ❓ untested | Needs sample | Enterprise body, and no caption or `djmd` sample has reached this project. Not covered by the Mavic 3 row above |
| DJI Air 3S | ⚠️ format parsed | ❓ untested | Needs sample | Decimal aperture; HLG colour mode; MP4 does carry `djmd`, schema unverified |
| DJI Mini 5 Pro | ⚠️ format parsed | ❓ untested | Needs sample | Decimal aperture and focal length |
| DJI Avata 360 | ⚠️ format parsed, incl. gimbal | ❓ untested | **Blocked** | Ships `.OSV` + `.LRF`, neither ingested yet — see below |
| DJI Avata 2 | ⚠️ format parsed | ❓ untested | Needs sample | Legacy `GPS(lat,lon,alt)` + `BAROMETER` |
| DJI Mavic Pro / Phantom 4 | ⚠️ format parsed | ❓ untested | Needs sample | Legacy `GPS(...)` layout |
| DJI Matrice 300 RTK | ⚠️ format parsed | ❓ untested | Needs sample | Legacy layout with `M` unit suffix |
| DJI Matrice 4TD | ❓ untested | ❓ untested | Needs sample | Nothing seen here — captions or `djmd`. The Matrice 300 row above says nothing about this model |
| DJI Phantom 4 RTK / P4P | ⚠️ format parsed | ❓ untested | Needs sample | Compact single-line (`F/5.6, SS 400, ISO 100, …`) |
| DJI Osmo Action 4 / 5 / 6 | — | ⚠️ decoder written, unverified | Needs sample | Not a drone, but embeds GPS from the Bluetooth remote in the same stream |

Legend: ✅ verified against real footage · ⚠️ partial (see notes) ·
❓ untested · ❌ not working · — not present on this model

**What "format parsed" actually means:** the caption layout is handled and
covered by a regression test, in most cases built from a short redacted
sample (a few frames) contributed publicly rather than a full flight. It has
not been checked against a real clip end to end, no export has been rendered
for it, and its embedded telemetry has not been read at all. Treat it as
"should work, unproven" — which is exactly what a report would settle.

Models not listed are simply unknown. Don't assume similarity across the
range: the layouts vary more than they look, and the legacy `GPS(...)` models
were parsing coordinates in the wrong order until a contributed sample
exposed it.

**Three rows are listed only to stop the obvious misreading** — the original
Neo, the Mavic 3E and the Matrice 4TD. Every other unknown model is absent from
this table; these three are here because the row above each one ("DJI Neo 2 ✅",
"DJI Mavic 3 ⚠️", "DJI Matrice 300 RTK ⚠️") is easy to read as covering them,
and none of them does. They are different drones, the embedded telemetry is a
different binary schema per model, and for all three neither the captions nor
the `djmd` stream has been seen here. They may well work on arrival; nobody here
knows, and guessing is the one thing this table exists to avoid.

## Known gaps

- **`.OSV` / `.LRF` (Avata 360, 360 models)** — this footage arrives as
  `.OSV` (360 video) plus an `.LRF` low-resolution proxy instead of `.MP4`,
  and neither is ingested today. Its captions parse (gimbal angles included),
  so the remaining work is container support, plus deciding what an overlay
  should even mean on equirectangular 360 footage. Reports still welcome —
  they tell us whether to prioritise it.
- **Embedded telemetry on any model but the Neo 2** — the decoder holds one
  verified field map (the Neo 2) and one written from published findings but
  never run against a real file (the Osmo Action 4/5/6). Every other model's
  stream is a different layout, and the app says so rather than guessing: an
  unrecognised schema is reported as unrecognised, which is what makes a
  reported sample worth something.

## Report your drone

**[Report yours](../../issues/new?template=drone-report.yml)** — the first
three confirmed reports per model each earn a **Contributor license — never
expires, lifetime updates**.

**Most wanted:** the models nothing has been seen from at all — the **DJI Neo**
(first generation), the **DJI Mavic 3E** and the **DJI Matrice 4TD** — and the
**DJI Osmo Action 4/5/6**, whose decoder is written but has never met a real
file. Every other row marked *Needs sample* counts the same.

Most useful, in order:

1. **A short untrimmed-quality `.MP4`** (a few seconds, copied without
   re-encoding — the template has the command). This is the only thing that
   can verify embedded telemetry, and it is where the real gaps are.
2. **The `.SRT`**, if your drone wrote one — quick to check, and it confirms
   the caption layout on real firmware rather than a snippet.
3. **`ffprobe` output**, which tells us which data streams the file carries
   even before anyone looks at the telemetry.

Privacy: replace lat/long digits with zeros before pasting anything — we
need the format, not your location. See the README for what happens to
sample footage.

File formats: `.MP4` and `.MOV` are both supported and both welcome.
