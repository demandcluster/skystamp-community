# SkyStamp community

Public home for [SkyStamp](https://skystamp.net) support and drone
compatibility work. The app itself is closed-source; this repo is where
issues live and where drone telemetry support gets built, model by model.

## Report your drone, earn a license

SkyStamp reads the telemetry DJI embeds in the video file — but field
layouts differ per model, and each one needs verification against real
footage. That's where you come in.

**`.MOV` footage is welcome too** (some models, like the Mini 4K, record
`.MOV` with telemetry as a subtitle track) — the app supports both formats;
samples still drive per-model verification.

**The first three confirmed reports for each drone model earn a Contributor
license — it never expires and includes lifetime updates.** "Confirmed"
means we've verified SkyStamp's telemetry parsing against your sample.
Independent samples from different pilots are what make a confirmation
solid, so redundancy is rewarded, not just speed. Substantial help on a
tricky model (maneuver clips, field verification flights) is rewarded the
same way.

### Most wanted right now

Any model is welcome, but two would change the code the day they arrive:

- **DJI Neo** (the first-generation one, not the Neo 2) — nothing from this
  drone has ever reached the project: no clip, not even a caption sample. It
  is also one of the most likely drones for someone reading this to own, so it
  is the single biggest hole in the list.
- **DJI Osmo Action 4 / 5 / 6** — the decoder for these already exists, written
  from published findings and never once run against a real file. One five-
  second clip either confirms it or shows us what to fix.

Everything marked *Needs sample* in [DRONES.md](DRONES.md) counts the same. If
your model is missing from the verified list and you report it, you have earned
the licence — you are by definition among the first for it.

**Privacy:** issues are public. SRT entries and file metadata may contain
your takeoff GPS — usually your home. Replace lat/long digits with zeros
before pasting; we only need the format, not your location.

To contribute, open a [drone report](../../issues/new?template=drone-report.yml)
with:

- Drone model and firmware version
- `ffprobe` output of a clip — `.MP4` or `.MOV` (the template shows the command)
- The `.SRT` file, if your drone recorded one
- A short sample clip — see the template for a copy command that trims a
  few seconds **without** re-encoding, which preserves the embedded
  telemetry track. Re-encoded or editor-exported clips lose it and can't
  be used.

Current status per model: [DRONES.md](DRONES.md).

## Bugs and questions

- [Bug report](../../issues/new?template=bug-report.yml)
- [Question](../../issues/new?template=question.yml)
- Discord: https://discord.gg/whkyQwMXAc (quick questions, show-and-tell)
- Docs: https://skystamp.net/docs/
- Email: support@skystamp.net (license/purchase matters especially)

## What happens with sample footage

Samples are used solely to verify telemetry parsing and build fixtures for
regression tests. Trimmed fixture files (a few seconds, no audio) may be
committed to the app's private test suite. If you'd rather your clip not be
kept at all after verification, say so in the issue — verification works
either way.
