# SkyStamp community

Public home for [SkyStamp](https://skystamp.net) support and drone
compatibility work. The app itself is closed-source; this repo is where
issues live and where drone telemetry support gets built, model by model.

## Report your drone, earn a license

SkyStamp reads the telemetry DJI embeds in every MP4 — but field layouts
differ per model, and each one needs verification against real footage.
That's where you come in:

**The first three confirmed reports for each drone model earn a Contributor
license — it never expires and includes lifetime updates.** Independent
samples from different pilots are what make a confirmation solid, so
redundancy is rewarded, not just speed. Substantial help on a tricky model
(maneuver clips, field verification flights) is rewarded the same way.

To contribute, open a [drone report](../../issues/new?template=drone-report.yml)
with:

- Drone model and firmware version
- `ffprobe` output of an MP4 (the template shows the command)
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
