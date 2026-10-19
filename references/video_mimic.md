# Character Replace Module

Character Replace from image, video, or reference materials.

## Supported Task Types

| Type | Description | Required Params |
|------|-------------|-----------------|
| `Full_Scene` | **Full Scene Replace** — Use the whole image's person and  background in the video | `--input-image` , `--input-video` |
| `Body_Only` | **Only Body Replace** — Use the image's person in the video, keep the video's background | `--input-image` , `--input-video` |

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
python {baseDir}/scripts/video_mimic.py <subcommand> --type <Full_Scene|Body_Only> [options]
```

## Examples

### List Models (ALWAYS run first — see § Model Selection)

```bash
python {baseDir}/scripts/video_mimic.py list-models --type Full_Scene
python {baseDir}/scripts/video_mimic.py list-models --type Body_Only --json
```

### Full Scene Replace (Full_Scene)

```bash
python {baseDir}/scripts/video_mimic.py run \
  --type Full_Scene \
  --model "<exact display name from list-models>" \
  --resolution 720 \
  --input-image <fileUrl> \
  --input-video <fileUrl> 
```

### Only Body Replace (Body_Only)

```bash
python {baseDir}/scripts/video_mimic.py run \
  --type Full_Scene \
  --model "<exact display name from list-models>" \
  --resolution 720 \
  --input-image <fileUrl|local_file> \
  --input-video <fileUrl|local_file> 
```

### Cost Estimation

```bash
python {baseDir}/scripts/video_mimic.py estimate-cost \
  --model "<exact display name from list-models>" --resolution 720 --input-video "C:\\video.mp4"
```

### Recovery / Batch

```bash
TASK_ID=$(python {baseDir}/scripts/video_mimic.py submit \
  --type Full_Scene \
  --model "<exact display name from list-models>" \
  --resolution 720 \
  --input-image <fileUrl|local_file> \
  --input-video <fileUrl|local_file> 

python {baseDir}/scripts/video_mimic.py query \
  --task-id <taskId> --timeout 1200
```

## Common Options

| Option | Description                                                                                                                                  |
|--------|----------------------------------------------------------------------------------------------------------------------------------------------|
| `--type` | Task type: `Full_Scene`, `Body_Only` (required)                                                                                              |
| `--model` | Model **display name** — required, **no default**. Get exact names via `list-models` and let the user pick (see § Model Selection)             |
| `--resolution` | `480`or `720`                                                                                                                                |
| `--input-image` | input image(image url or local path). E.g. image url: `--input-image "https//photo.jpg"` or local file: `--input-image "/path/to/photo.jpg"` |
| `--input-video` | input video(video url or local path). E.g. video url: `--input-video "https//video.mp4"` or local file: `--input-video "/path/to/video.mp4"` |
| `--timeout` | Max polling time (default: 600)                                                                                                              |
| `--interval` | Polling interval (default: 60)                                                                                                               |
| `--output-dir` | Download result videos to directory                                                                                                          |
| `--json` | Output full JSON response (not used by default; only when the user explicitly requests raw JSON output)                                      |
| `-q, --quiet` | Suppress status messages                                                                                                                     |

## Body_Only only

- Only some models support `--type Body_Only` — check via `list-models --type Body_Only`

## Model Selection (MANDATORY)

> **There is NO default model.** Model names written in docs or examples may be outdated or wrong — never guess, reuse an example name, or let the script fall back to a default.

Before **any** generation (including `estimate-cost`), always follow this flow:

1. Run `list-models --type <Full_Scene|Body_Only>` to get the current supported model list with constraints (resolutions, supported task types).
2. Present the list to the user in plain language (model name + what it supports / is good for), and ask the user to pick one.
3. Pass the **exact display name** returned by `list-models` to `--model`.

If the user already named a model, still verify the name exists via `list-models --type <type>`; if it does not match any listed model, show the list and ask them to choose from it.

**If a model is not found at runtime:** the script aborts **without submitting anything** (no default fallback) and prints the available models for that task type — present that list to the user and re-run with the name they pick.

