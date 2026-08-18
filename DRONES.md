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
  There are about seven known layouts across the DJI range and we now parse
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
| **DJI Mini 4 Pro** | ✅ verified on real firmware | ✅ verified, incl. attitude & gimbal | **Confirmed** | Two full flights from **Justin (Exphantomflyer)**, lab work by **CallMarcus**, against [#1](../../issues/1). `dvtm_Mini4_Pro.proto` mapped field by field and cross-checked against ExifTool 13.59, export rendered — shipped in **0.7.3**. **0.7.2 and earlier report this model's yaw wrongly** — it names its flight-controller section and puts its gimbal where the Neo 2 keeps its flight controller, which our index fallback misread; see below. Firmware `01.0011.00` writes a **decimal** aperture (`[fnum: 1.7]`); other firmware writes it ×100 (`170`). Both are accepted. Carries `djmd` + `dbgi`, one sample per video frame, 1:1 with the captions |
| DJI Mini 3 Pro | ⚠️ format parsed | ❓ untested | Needs sample | **Not the Mini 4 Pro** — the row above is a different model and a different `djmd` schema. Bracket layout; aperture ×100 (`170` = f/1.7) on the firmware seen so far |
| DJI Mavic Air 2 / DJI FPV | ⚠️ format parsed | ❓ untested | Needs sample | Same bracket layout as above |
| DJI Mavic 3 / Air 2S / Air 3 | ⚠️ format parsed | ❓ untested | Needs sample | `SrtCnt` counter; aperture ×100, focal length ×10 |
| DJI Mavic 3E (Enterprise) | ❓ untested | ❓ untested | Needs sample | Enterprise body, and no caption or `djmd` sample has reached this project. Not covered by the Mavic 3 row above |
| **DJI Air 3S** | ⚠️ format parsed | ✅ verified, incl. attitude & gimbal | **Telemetry confirmed, captions unproven** | Embedded schema `dvtm_Air3s.proto` reverse-engineered and flight-verified from a full clip **brianm365** contributed against [#3](../../issues/3), export rendered — shipped in 0.7.2. It mounts its sections one index lower than the Neo 2 and writes coordinates in radians, both of which SkyStamp read wrongly before. Captions were off on that flight, so the `.SRT` side is still only a public sample: decimal aperture, HLG colour mode |
| DJI Mini 5 Pro | ⚠️ format parsed | ❓ untested | Needs sample | Decimal aperture and focal length |
| DJI Avata 360 | ⚠️ format parsed, incl. gimbal | ❓ untested | **Blocked** | Ships `.OSV` + `.LRF`, neither ingested yet — see below |
| DJI Avata 2 | ⚠️ format parsed | ❓ untested | Needs sample | Legacy `GPS(lat,lon,alt)` + `BAROMETER` |
| **DJI Mavic Pro** | ✅ verified on real firmware | — none in the footage seen | **Confirmed** (captions are all this footage carries) | Full flight, export rendered — shipped in **0.7.6**. The caption layout follows the **app**, not the drone: paired with the DJI GO era app it leads with `HOME(...)` and reverses the tuple to `GPS(longitude, latitude, satellites)`. **0.7.5 and earlier read those coordinates mirrored** — a UK flight lands in the Indian Ocean, and silently, because both values sit inside latitude range so no range check can catch it — **and stamp the satellite count as altitude**; the real altitude is `BAROMETER:`. Re-export from 0.7.6 to correct either. The clip seen here carries a single video stream: no `djmd`, no audio track |
| **DJI Phantom 3 Pro / Advanced** | ✅ verified on real firmware | — pre-`djmd` generation | **Confirmed** (captions are everything this generation writes) | One full flight from each model, exports rendered — shipped in **0.7.6**. Same DJI GO caption layout as the Mavic Pro row, same 0.7.5-and-earlier mirrored-coordinates / satellites-as-meters fault, same fix. The two models are indistinguishable in their captions (same fixed f/2.8 lens, same app version string) |
| **DJI Phantom 3 Standard** | ✅ verified on real firmware | — pre-`djmd` generation | **Confirmed** (captions are everything this generation writes) | Full flight, export rendered — shipped in **0.7.6**. Same layout with minor spellings of its own: `Fnum:2.8` without the `F` prefix, `EV: 0` with a space |
| DJI Phantom 4 | ⚠️ format parsed | ❓ untested | Needs sample | Legacy `GPS(...)` layout. Split from the old "Mavic Pro / Phantom 4" row — the Mavic Pro footage above says nothing about this model, and the Mavic Pro turned out to write a different tuple order than the Phantom 4 sample this row's parser was built from |
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
- **Embedded telemetry on any model but the Neo 2, the Air 3S and the Mini 4
  Pro** — the decoder holds three flight-verified field maps (those three) and
  one written from published findings but never run against a real file (the
  Osmo Action 4/5/6).
  Every other model's stream is a different layout, and the app says so rather
  than guessing: an unrecognised schema is reported as unrecognised, which is
  what makes a reported sample worth something.

  The Air 3S is what that costs and what it buys. One contributed clip moved it
  from "carries `djmd`, schema unverified" to a full field map in a week — and
  it took a clip to do it, because the model differed from the Neo 2 in two
  ways no amount of reading captions would have shown: its sections sit at
  different indices, and its coordinates are in radians rather than degrees.
  Guessing either would have put a real flight in the Gulf of Guinea.
- **DJI Mini 4 Pro — what the two flights cost us, and bought.** The clips did
  not just add a model, they exposed an assumption. This drone announces its
  flight-controller section with an explicit type tag and then parks its
  **gimbal** in the slot the Neo 2 and the Osmo use for their flight
  controller. Dispatch fell back to the slot number, so the gimbal was decoded
  as a second flight controller: it overwrote the aircraft's roll, pitch and
  yaw with the camera's — a steady ~13° yaw error, ExifTool reading 48.3° where
  SkyStamp showed 61.5° on the same frame — and the real gimbal group was never
  read at all.

  Fixed in **0.7.3**. A section that states its type is now taken at its word
  wherever it sits, and only an anonymous section is read by its slot — so the
  next model to rearrange itself cannot reproduce this. **Every release up to
  and including 0.7.2 has the yaw fault**, and a clip exported with one of them
  is stamped with it; re-export from 0.7.3 to correct it.

  The verification is the strongest this list has, because for the first time
  there was an outside reference to check against rather than our own bytes:
  **ExifTool 13.59** decodes this schema, and its per-field sample counts over
  3483 packets match ours exactly. Vertical speed was flight-checked against
  the altitude trace (correlation 0.978, mean error 0.17 m/s).

  One correction back to the reference: ExifTool's `DroneRoll` and `DronePitch`
  are **swapped** on this model. Flight physics settles it — the field ExifTool
  calls roll shows no relationship to bank angle (−0.01), while the other
  tracks the bank a coordinated turn predicts at +0.75 across 2260 samples.
  SkyStamp already read that pair the opposite way from ExifTool's table, from
  a maneuver clip flown deliberately on the Neo 2; the Mini 4 Pro agrees by a
  different method entirely.

  Gimbal roll is present at `3-4-3-2` and still not decoded, so no mount is
  claimed for it.

## Report your drone

**[Report yours](../../issues/new?template=drone-report.yml)** — the first
three confirmed reports per model each earn a **Contributor license — never
expires, lifetime updates**.

**Most wanted:** the models nothing has been seen from at all — the **DJI Neo**
(first generation), the **DJI Mavic 3E** and the **DJI Matrice 4TD** — and the
**DJI Osmo Action 4/5/6**, whose decoder is written but has never met a real
file. Every other row marked *Needs sample* counts the same.

Most useful, in order:

1. **A full original `.MP4`, untrimmed.** This is the only thing that can
   verify embedded telemetry, and it is where the real gaps are. Please don't
   cut it down first: on any model carrying `djmd`, a stream-copied trim either
   fails outright (`Could not find tag for codec none`) or, remuxed to `.MOV`,
   rewrites the data track's sample description so the bytes survive and the
   meaning doesn't. A trimmed clip is the one thing that can't do this job.
   If the file is too large to attach, say so in the report and we'll arrange
   a transfer.
2. **The `.SRT`**, if your drone wrote one — quick to check, and it confirms
   the caption layout on real firmware rather than a snippet.
3. **`ffprobe` output**, which tells us which data streams the file carries
   even before anyone looks at the telemetry.

Privacy: replace lat/long digits with zeros before pasting anything — we
need the format, not your location. See the README for what happens to
sample footage.

File formats: `.MP4` and `.MOV` are both supported and both welcome.
