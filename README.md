# Claudio Startrek Bridge Soundpack

Star Trek bridge console sounds for [Claudio](https://claudio.click).

## Install

```bash
claudio soundpack add gh:ctoth/claudio-soundpack-startrek-bridge --name startrek-bridge --default
```

Already installed? Pull the latest:

```bash
claudio soundpack update startrek-bridge
```

## What Is In It

87 sounds answering for 132 sound keys.

- Every sound sits at -18 LUFS (measured spread: 0.6 LU) with true peaks
  under -1 dBTP, so one volume setting suits all of them.
- Nothing starts late: silence before each sound is trimmed.
- Sounds are as long as how often they fire allows. Loading sounds are under
  1 s, success under 1.5 s, errors under 2 s. The longest sound is the 5.8 s
  session start.
- 48 kHz 16-bit WAV, the player's own format.

## Layout

```text
soundpack.json   built: sound key -> sounds/...wav
sounds/          built: mastered WAVs
source/          raw material and the manifest the pack is built from
```

`soundpack.json` and `sounds/` are build products. Do not edit them by
hand; change `source/` and rebuild.

## Changing The Pack

Needs a `claudio` with the `soundpack audit` and `soundpack master`
commands.

1. Add or replace a raw file under `source/` (WAV, MP3 or AIFF, any level or
   length), or edit which key maps to which file in `source/soundpack.json`.
2. Rebuild and check:

   ```bash
   rm -rf sounds soundpack.json
   claudio soundpack master source/soundpack.json --out .
   claudio soundpack audit soundpack.json --strict
   claudio soundpack validate soundpack.json
   ```

3. If `master` reports a sound as `truncated`, it was longer than its
   category allows and lost its end. Cut the raw file to the part worth
   keeping (see `source/README.md` for how the existing cuts were chosen)
   and rebuild.

CI runs the same audit on every push, so a sound that is too long, too loud
or padded with silence fails the build.

To find the next sounds worth adding, ask Claudio which keys it looked for
and did not find:

```bash
claudio analyze missing --preset all-time --limit 50
```

## Source Notice

The sounds were assembled from the Claudio `soundpacks/startrek-bridge`
directory. The original in-repo manifest describes the source as
TrekCore.com.
