# AI Image Module

Generate images from text prompts or edit existing images with AI-powered models.

## Supported Task Types

| Type | Description | Required Params |
|------|-------------|-----------------|
| `text2image` | **Text-to-Image** — generate images from a text prompt | `--model`, `--prompt`, `--aspect-ratio` |
| `image_edit` | **Image Edit** — edit images with prompt + reference images | `--model`, `--prompt`, `--aspect-ratio`, `--input-images` |

## Subcommands

| Subcommand | When to use | Polls? |
|------------|-------------|--------|
| `run` | **Default.** New request, start to finish | Yes — waits until done |
| `submit` | Batch: fire multiple tasks without waiting | No — exits immediately |
| `query` | Recovery: resume polling a known `taskId` | Yes — waits until done |
| `list-models` | Check models, constraints, and supported ratios | No |
| `estimate-cost` | Estimate credit cost before running | No |

## Usage

```bash
python {baseDir}/scripts/ai_image.py <subcommand> --type <text2image|image_edit> [options]
```

## Examples

### List Models (ALWAYS run first — see § Model Selection)

```bash
python {baseDir}/scripts/ai_image.py list-models --type text2image
python {baseDir}/scripts/ai_image.py list-models --type image_edit --json
```

### Text-to-Image

Fixed-price model (no resolution):

```bash
python {baseDir}/scripts/ai_image.py run \
  --type text2image \
  --model "<exact display name from list-models>" \
  --prompt "A watercolor painting of a cat" \
  --aspect-ratio "1:1"
```

Required resolution：

```bash
python {baseDir}/scripts/ai_image.py run \
  --type text2image \
  --model "<exact display name from list-models>" \
  --prompt "A futuristic city skyline at dusk, neon lights reflected on wet streets" \
  --aspect-ratio "16:9" \
  --resolution "2K" \
  --count 2
```

### Image Edit

Fixed-price model (no resolution):

```bash
python {baseDir}/scripts/ai_image.py run \
  --type image_edit \
  --model "<exact display name from list-models>" \
  --prompt "Change the background to a snowy mountain landscape" \
  --aspect-ratio "auto" \
  --input-images photo.jpg
```

Required resolution:

```bash
python {baseDir}/scripts/ai_image.py run \
  --type image_edit \
  --model "<exact display name from list-models>" \
  --prompt "Blend the style of both images" \
  --aspect-ratio "1:1" \
  --resolution "2K" \
  --input-images photo.jpg \
  --count 2
```

### Cost Estimation

```bash
python {baseDir}/scripts/ai_image.py estimate-cost \
  --type text2image --model "<exact display name from list-models>" --resolution "2K" --count 2
```

### Download Results

```bash
python {baseDir}/scripts/ai_image.py run \
  --type text2image --model "<exact display name from list-models>" \
  --prompt "Northern lights" --aspect-ratio "16:9" --resolution "2K" \
  --output-dir ./results
```

## Options

| Option | Description                                                                                                                                  |
|--------|----------------------------------------------------------------------------------------------------------------------------------------------|
| `--type` | `text2image` or `image_edit` (required)                                                                                                      |
| `--model` | Model **display name** — required, **no default**. Get exact names via `list-models` and let the user pick (see § Model Selection) |
| `--prompt` | Text prompt (required)                                                                                                                       |
| `--aspect-ratio` | Aspect ratio (required), e.g. `"16:9"`, `"1:1"`                                                                                              |
| `--resolution` | `"1K"`, `"2K"`, `"4K"` — model-dependent                                                                                           |
| `--count` | Number of images (1-4, default: 1)                                                                                                           |
| `--input-images` | Reference image url or local paths, space-separated (image_edit only). E.g. `--input-images https://photo.jpg` or `--input-images photo.jpg` |
| `--timeout` | Max polling time (default: 600)                                                                                                              |
| `--interval` | Polling interval (default: 30)                                                                                                               |
| `--output-dir` | Download results to directory                                                                                                                |
| `--json` | Full JSON response (not used by default; only when the user explicitly requests raw JSON output)                                             |
| `-q, --quiet` | Suppress status messages                                                                                                                     |

## Model Selection (MANDATORY)

> **There is NO default model.** Model names written in docs or examples may be outdated or wrong — never guess, reuse an example name, or assume a recommendation.

Before **any** generation (including `estimate-cost`), always follow this flow:

1. Run `list-models --type <text2image|image_edit>` to get the current supported model list with constraints (aspect ratios, resolutions, reference-image limits).
2. Present the list to the user in plain language (model name + what it supports / is good for), and ask the user to pick one.
3. Pass the **exact display name** returned by `list-models` to `--model`.

If the user already named a model, still verify the name exists via `list-models --type <type>`; if it does not match any listed model, show the list and ask them to choose from it.

**If a model is not found at runtime:** the script aborts **without submitting anything** (no default fallback) and prints the available models for that task type — present that list to the user and re-run with the name they pick.

## Key Notes

- `aspectRatio` is always required;
- `resolution` is required for some models, forbidden for others — check via `list-models`
- Some models may only be available for one task type (`text2image` or `image_edit`) — always check the exact list via `list-models --type <type>`
