# Source Material

`soundpack.json` here maps each sound key to a raw file in this directory.
The published pack at the repository root is built from it:

```bash
claudio soundpack master source/soundpack.json --out .
```

Many keys share a file. The original pack shipped 107 files of which 20 were
byte-for-byte copies; here each sound exists once and the manifest does the
sharing.

## Edited Files

Mastering only trims silence and sets the level. These files needed a
decision about which part to keep, made by reading spectrograms
(`ffmpeg -i in.mp3 -lavfi showspectrumpic=s=1200x300:scale=log:fscale=log out.png`).
The `.mp3` originals of these are in git history at commit `0bac473`.

| File | Original | Edit | Why |
| --- | --- | --- | --- |
| `system/session-start.wav` | 227.7 s bridge ambience loop | 21.7 s to 27.5 s, faded in and out | Keeps one cluster of console beeps over the hum instead of four minutes of it. |
| `system/system.wav` | 186.7 s bridge ambience loop | 0.6 s to 4.2 s, faded | Two beep clusters. |
| `interactive/notification.wav` | 5.3 s: one burst repeated four times | first 2.15 s (two bursts) | Two is enough to notice. |
| `loading/tool-start.wav` | 1.5 s of console chatter | first 0.60 s | The generic loading sound plays before most tool calls; the chatter repeats, so the start is representative. |
| `loading/file-reading.wav` | 1.5 s of chatter | first 0.65 s | Same. |
| `loading/grep-start.wav` | 3.1 s of repeating blips | first 0.65 s | Same. |
| `loading/loading.wav` | 1.3 s: pulsing tone, then a held tone | first 1.0 s, 0.25 s fade | The pulses are intact; only the held tail is shortened. |
| `loading/processing.wav` | 1.6 s | first 1.0 s | Loses a closing figure in the last 0.3 s. |
| `loading/connect.wav` | 1.1 s: sweep, then five beeps | sped up 8% (pitch kept) | Fits the whole gesture under 1 s instead of fading out the last beep. |
| `success/mcp-success.wav` | 1.6 s: two tones, then a second of noise floor | first 0.62 s | The tail was hiss. |
| `success/tool-complete.wav` | 3.5 s of ambience with two beep clusters | 0.15 s to 0.90 s, high-passed at 200 Hz | Keeps the first cluster. Without the high-pass the rumble, not the beeps, set the level. |

Commands, for the record:

```bash
ffmpeg -ss 21.7 -t 5.8 -i session-start.mp3 -af "afade=t=in:d=0.15,afade=t=out:st=5.2:d=0.6" -c:a pcm_s16le session-start.wav
ffmpeg -ss 0.6 -t 3.6 -i system.mp3 -af "afade=t=in:d=0.15,afade=t=out:st=3.1:d=0.5" -c:a pcm_s16le system.wav
ffmpeg -t 2.15 -i notification.mp3 -af "afade=t=out:st=2.05:d=0.1" -c:a pcm_s16le notification.wav
ffmpeg -t 0.60 -i tool-start.mp3 -af "afade=t=out:st=0.54:d=0.06" -c:a pcm_s16le tool-start.wav
ffmpeg -t 0.65 -i file-reading.mp3 -af "afade=t=out:st=0.59:d=0.06" -c:a pcm_s16le file-reading.wav
ffmpeg -t 0.65 -i grep-start.mp3 -af "afade=t=out:st=0.59:d=0.06" -c:a pcm_s16le grep-start.wav
ffmpeg -t 1.0 -i loading.mp3 -af "afade=t=out:st=0.75:d=0.25" -c:a pcm_s16le loading.wav
ffmpeg -t 1.0 -i processing.mp3 -af "afade=t=out:st=0.85:d=0.15" -c:a pcm_s16le processing.wav
ffmpeg -i connect.mp3 -af "atempo=1.08" -c:a pcm_s16le connect.wav
ffmpeg -t 0.62 -i mcp-success.mp3 -af "afade=t=out:st=0.56:d=0.06" -c:a pcm_s16le mcp-success.wav
ffmpeg -ss 0.15 -t 0.75 -i tool-complete.mp3 -af "highpass=f=200,afade=t=out:st=0.69:d=0.06" -c:a pcm_s16le tool-complete.wav
```

## Keys Added In 2.0.0

The first version filed its stop sounds under `interactive/`, where the
`Stop` and `SubagentStop` events never look, so every subagent stop fell back
to the main completion sound. `completion/subagent-complete.wav` and
`completion/subagent-stop.wav` now map to the short low tone in
`completion/stop.mp3`.

Other keys were added because usage tracking showed Claudio asking for them
often and finding nothing: `git-start`, `rg-*`, `gh-start`, `uv-start`,
`cd-*`, `apply-patch-*`, `get-content-*`, `powershell-*`, `agent-*`,
`subagent-start`, `permission-request`, `post-compact`, and
`success/tool-batch`. Each reuses the existing sound closest in meaning.
