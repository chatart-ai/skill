# Video Generation Module

Generate videos from images, text prompts, or reference materials.

## Supported Task Types

| Type | Description | Required Params |
|------|-------------|-----------------|
| `i2v` | **Image-to-Video V2** — generate video from first/end frame images, **or** in reference mode from multiple reference images / reference videos / reference voices | `--first-frame`, or at least one of `--ref-images` / `--ref-videos` / `--ref-audios` (the two modes are mutually exclusive) |
| `t2v` | **Text-to-Video** — generate video purely from a text prompt | `--model`, `--prompt` |
| `extend` | **Video Extension** -- generate video from an input video + prompt | `--model`, `--prompt`, `--duration`, `--input_video` |
| `anim` | **image to Animation video** — generate animation from an input image + style + prompt | `--model`, `--prompt`, `--input_image`, `--duration` |
| `a2ls` | **Audio-to-Lip-sync** — generate lip-synced video from an input image + audio + prompt | `--model`, `--input_image`, `--input_sound` |

## Subcommands

| Subcommand | When to use | Polls? |
|------------|-------------|--------|
| `run` | **Default.** New request, start to finish | Yes — waits until done |
| `submit` | Batch: fire multiple tasks without waiting | No — exits immediately |
| `query` | Recovery: resume polling a known `taskId` | Yes — waits until done |
| `list-models` | Check models, constraints, and audio support | No |
| `estimate-cost` | Estimate credit cost before running | No |

## Usage

```bash
python {baseDir}/scripts/video_gen.py <subcommand> --type <i2v|t2v|extend|anim|a2ls> [options]
```

## Examples

### List Models (ALWAYS run first — see § Model Selection)

```bash
python {baseDir}/scripts/video_gen.py list-models --type t2v
python {baseDir}/scripts/video_gen.py list-models --type i2v --json
python {baseDir}/scripts/video_gen.py list-models --type extend --json
python {baseDir}/scripts/video_gen.py list-models --type anim --json
python {baseDir}/scripts/video_gen.py list-models --type a2ls --json
```

### Image-to-Video (i2v)

```bash
python {baseDir}/scripts/video_gen.py run \
  --type i2v \
  --first-frame <fileId_or_local_path> \
  --prompt "A product slowly rotating on a clean white background" \
  --model "<exact display name from list-models>" \
  --resolution 1080 \
  --duration 5 \
  --sound on
```

With first + end frame:

```bash
python {baseDir}/scripts/video_gen.py run \
  --type i2v \
  --first-frame <fileId_or_local_path> \
  --end-frame <fileId_or_local_path> \
  --model "<exact display name from list-models>" \
  --prompt "Smooth transition between scenes" \
  --resolution 1080
```

Multi-image reference (reference mode — do NOT combine with `--first-frame` / `--end-frame`):

```bash
python {baseDir}/scripts/video_gen.py run \
  --type i2v \
  --model "<exact display name from list-models>" \
  --ref-images img1.png img2.png img3.png \
  --prompt "The characters from the reference images dance together on a stage"
```

Reference video + reference voice (supported models only — check the Refs column in `list-models --type i2v`):

```bash
python {baseDir}/scripts/video_gen.py run \
  --type i2v \
  --model "<exact display name from list-models>" \
  --ref-images portrait.png \
  --ref-videos motion.mp4 \
  --ref-audios voice.mp3 \
  --prompt "The person in the image reproduces the motion of the reference video and speaks with the voice from the reference audio"
```

### Text-to-Video (t2v)

```bash
python {baseDir}/scripts/video_gen.py run \
  --type t2v \
  --model "<exact display name from list-models>" \
  --prompt "A futuristic city at night with neon lights reflecting on wet streets" \
  --aspect-ratio "16:9" \
  --resolution 1080 \
  --duration 5 \
  --sound on
```

### Video Extension

```bash
python {baseDir}/scripts/video_gen.py run \
  --type extend \
  --model "<exact display name from list-models>" \
  --prompt " Extent Video Content Description" \
  --input-videos <fileUrl_or_local_path> \
  --duration 5
```

### Ai Animation

```bash
python {baseDir}/scripts/video_gen.py run \
  --type anim \
  --model "<exact display name from list-models>" \
  --prompt "Video Content Description" \
  --input_image <fileUrl_or_local_path> \
  --duration 5 \
  --style "cyberpunk" \
  --resolution 720 
```

### Lip Sync

```bash
python {baseDir}/scripts/video_gen.py run \
  --type a2ls \
  --model "<exact display name from list-models>" \
  --prompt "Scene description e.g singing" \
  --input_image <fileUrl_or_local_path> \
  --input_sound <fileUrl_or_local_path>
```

### Cost Estimation

```bash
python {baseDir}/scripts/video_gen.py estimate-cost \
  --model "<exact display name from list-models>" --resolution 1080 --duration 5 --sound on --count 2
```

### Recovery / Batch

```bash
TASK_ID=$(python {baseDir}/scripts/video_gen.py submit \
  --type t2v --model "<exact display name from list-models>" --prompt "Sunset over ocean" -q)

python {baseDir}/scripts/video_gen.py query \
  --type t2v --task-id <taskId> --timeout 1200
```

## Common Options

| Option | Description                                                                                             |
|--------|---------------------------------------------------------------------------------------------------------|
| `--type` | Task type: `i2v`, `t2v`, `anim`, `extend`, `a2ls` (required)                                            |
| `--model` | Model **display name** — required, **no default**. Get exact names via `list-models` and let the user pick (see § Model Selection) |
| `--prompt` | Text prompt (required)                                                                                  |
| `--aspect-ratio` | Aspect ratio, e.g. `"16:9"`                                                                             |
| `--resolution` | `360`, `480`, `540`, `720`, `768`, `1080`, `1440` (sent to API as `"2K"`, e.g. MiniMax-H3)                                                           |
| `--duration` | Video duration in seconds                                                                               |
| `--input_image` | Input image for animation/a2ls (fileId or local path)                                              |
| `--count` | Number of videos (1-4)                                                                                  |
| `--sound` | Native audio switch for `i2v` / `t2v`: `on` → `sound_effect_switch: 1`, `off` → `sound_effect_switch: 0`. Omit it to keep the API default. Only models with `nativeAudio: Yes` support it. Backend billing ignores this flag (same price on/off) |
| `--timeout` | Max polling time (default: 900)                                                                         |
| `--interval` | Polling interval (default: 30)                                                                          |
| `--output-dir` | Download result videos to directory                                                                     |
| `--json` | Output full JSON response (not used by default; only when the user explicitly requests raw JSON output) |
| `-q, --quiet` | Suppress status messages                                                                                |

### i2v only

Frame mode and reference mode are **mutually exclusive**: use either `--first-frame` / `--end-frame`, or one or more of the `--ref-*` flags. The script rejects combinations and per-model count limits with a hard error.

| Option | Description                                                                                                      |
|--------|------------------------------------------------------------------------------------------------------------------|
| `--first-frame` | First frame image: file Url or local path. E.g. `--first-frame https://photo.png` or `--first-frame photo.png`   |
| `--end-frame` | End frame image: file Url or local path. E.g. `--end-frame https://end.png` or `--end-frame end.jpg`                        |
| `--ref-images` | Reference images (multi-image reference, sent as comma-joined `img_url_list`): one or more fileId/URL/local paths. Max count is model-dependent (see § i2v Reference Capabilities) |
| `--ref-videos` | Reference videos (comma-joined `video_url_list`): one or more fileId/URL/local paths. Max count is model-dependent |
| `--ref-audios` | Reference voices/audios (comma-joined `audio_url_list`): one or more fileId/URL/local paths. Max count is model-dependent |

## i2v Reference Capabilities (mirrored from the frontend model forms)

| Model | --ref-images | --ref-videos | --ref-audios | Media constraints (enforced by the API, not by the script) |
|-------|--------------|--------------|--------------|--------------------------------------------------------------|
| LitAI 5 | — | — | — | First/end-frame only |
| Seedance 2.0 | 9 | 1 | — | Reference video 2–15.4s, at most 1 |
| Seedance 2.0 Fast | 9 | 1 | — | Reference video 2–15.4s, at most 1 |
| Seedance 1.5 Pro | — | — | — | First/end-frame only |
| Kling V3 | 5 | — | — | Native multi-image only (no video/audio reference) |
| LoveAI 1.0 | — | — | — | First/end-frame only |
| MiniMax-H3 | 9 | 3 | 3 | Video ≤3 clips; audio wav/mp3 2–15s each, total ≤15s |
| Seedance 2.5 | 30 | 10 | 10 | Video/audio clips 2–30s each, total ≤30s per kind |

Notes:
- `list-models --type i2v` prints a **Refs(img/vid/aud)** column with these caps; the JSON output includes `refImages` / `refVideos` / `refAudios`.
- Some models (e.g. MiniMax-H3, Seedance 2.5) also accept **video/audio reference without any image**; the script still requires at least one `--ref-*` input, which may be videos/audios only.
- When the user asks for a model beyond this registry (e.g. Kling 3 Omni, Vidu Q2), tell them it is not registered in this skill yet — do not improvise parameters.

### Video Extension only

| Option | Description                                                                                              |
|--------|----------------------------------------------------------------------------------------------------------|
| `--input_video` | input video: file Url or local path. E.g. `--input_video https://video.mp4` or `--input_video video.mp4` |

### Ai Animation only

| Option | Description                                                                                                                                                                                                                                                                  |
|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--style` | Animation style, e.g. `comic`, `anime`, `3d_animation`, `clay`, `cyberpunk` |
| `--input_image` | input image: file Url or local path. E.g. `--input_image https://photo.png` or `--input_image photo.png`   |

### Lip Sync only

| Option | Description                                                                                              |
|--------|----------------------------------------------------------------------------------------------------------|
| `--input_sound` | input sound: file Url or local path. E.g. `--input_sound https://music.mp3` or `--input_sound music.mp3` |
| `--input_image` | input image: file Url or local path. E.g. `--input_image https://photo.png` or `--input_image photo.png` |



## Model Selection (MANDATORY)

> **There is NO default model.** Model names written in docs or examples may be outdated or wrong — never guess, reuse an example name, or let the script fall back to a default.

Before **any** generation (including `estimate-cost`), always follow this flow:

1. Run `list-models --type <i2v|t2v|extend|anim|a2ls>` to get the current supported model list with constraints (aspect ratios, resolutions, durations, native audio).
2. Present the list to the user in plain language (model name + what it supports / is good for), and ask the user to pick one.
3. Pass the **exact display name** returned by `list-models` to `--model`.

If the user already named a model, still verify the name exists via `list-models --type <type>`; if it does not match any listed model, show the list and ask them to choose from it.

**If a model is not found at runtime:** the script aborts **without submitting anything** (no default fallback) and prints the available models for that task type — present that list to the user and re-run with the name they pick.


## Prompt Tips

**Structure:** Subject + Action + Environment + Style + Camera

**Camera keywords:** "static shot", "slow pan left", "dolly forward", "tracking shot", "orbit around", "zoom in", "crane shot rising", "shallow depth of field"
