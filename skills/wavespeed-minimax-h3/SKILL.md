---
name: wavespeed-minimax-h3
description: Generate and edit videos with the open-weights MiniMax H3 model via WaveSpeed AI. Covers text-to-video, image-to-video (first and last frame), reference-to-video (up to 9 images, 3 videos and 3 audios), video edit and video extend, with 3-15 second clips at 480p, 540p, 768p (default) or 1080p and native stereo audio. Use when the user asks for MiniMax H3, or wants an affordable video from a prompt, an animated image, a clip built from reference media, an edited video, or a continued clip.
metadata:
  author: wavespeedai
  version: "1.0"
---

# WaveSpeedAI MiniMax H3 Video Generation

Generate and edit videos with the open-weights MiniMax H3 model on the WaveSpeed AI platform. Text, image and reference inputs produce 3-15 second clips with native stereo audio; video edit rewrites an existing clip and video extend appends a new segment. The default resolution is `768p`, the model's native canvas and its counterpart of other models' 720p; `480p`, `540p` and `1080p` are also available.

## Setup

Install the open-source CLI once and sign in; the CLI stores the key, so never ask the user to paste an API key into the chat:

```bash
npm install -g @wavespeed/cli
wavespeed login          # opens https://wavespeed.ai/accesskey and stores the key
wavespeed status         # confirms you are signed in
```

For CI or one-off shells, `WAVESPEED_API_KEY` in the environment also works.

Prefer MCP tools over shell commands? The same platform is exposed as an MCP server: hosted at `https://api.wavespeed.ai/mcp` (browser sign-in) or locally with `npx -y @wavespeed/mcp@latest`; every example below maps onto `run_model` with the same model id and input fields.

## Quick Start

The shortcut commands use the same model and pick the endpoint from the inputs: `wavespeed generate video "<prompt>" -m minimax-h3`, and likewise `wavespeed edit video` and `wavespeed extend video`; the MCP tools `generate_video`, `edit_video` and `extend_video` take the same `model` value (confirm the keys with `wavespeed generate video --help`). With this model the shortcuts map `--resolution 720p` to `768p`. Add `--estimate --json` first to see the chosen endpoint, final input and price without submitting.

```bash
wavespeed generate video "A paper boat drifting down a rain-soaked street" -m minimax-h3 --estimate --json
```

### Text-to-Video

```bash
OUTPUT_URL=$(wavespeed run wavespeed-ai/minimax-h3/text-to-video \
  -p "A golden retriever running through a field of sunflowers at sunset, slow tracking shot, birdsong" \
  --json | jq -r '.outputs[0]')
```

### Image-to-Video

The `image` parameter accepts an image URL. If you have a local file, pass it with the `@path` marker and the CLI uploads it and substitutes the hosted URL.

```bash
OUTPUT_URL=$(wavespeed run wavespeed-ai/minimax-h3/image-to-video \
  -i image=@./photo.png \
  -p "The person slowly turns and smiles at the camera" \
  --json | jq -r '.outputs[0]')
```

Existing URLs work as-is:

```bash
OUTPUT_URL=$(wavespeed run wavespeed-ai/minimax-h3/image-to-video \
  -i image="https://example.com/photo.jpg" \
  -p "The person slowly turns and smiles at the camera" \
  --json | jq -r '.outputs[0]')
```

## API Endpoints

On every endpoint `duration` is an integer from 3 to 15 seconds and `resolution` is one of `480p`, `540p`, `768p` (default) or `1080p`. Audio is generated natively with the video; only video edit has a `generate_audio` switch.

The catalog also lists LoRA variants (`text-to-video-lora`, `image-to-video-lora`, `reference-to-video-lora`), the `wavespeed-ai/minimax-h3-singularity/*` image- and reference-to-video endpoints, `image-to-video-spicy`, `controlnet-union` (follows the motion and structure of a source video) and still-image endpoints (`text-to-image`, `image-edit`); inspect any of them with `wavespeed run <id> -h`. The separate `minimax/h3`, `minimax/h3-highspeed` and `minimax/h3-lite` families (text, image and reference-to-video, 4-15 s; `minimax/h3` at 768p or 2K, Highspeed at 768p, Lite at 768p or 480p) have their own schemas and prices and are not covered here.

### Text-to-Video

**Model ID:** `wavespeed-ai/minimax-h3/text-to-video`

#### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `prompt` | string | Yes | -- | Scene, action, camera movement and the desired soundtrack |
| `aspect_ratio` | string | No | `16:9` | One of: `16:9`, `9:16`, `1:1`, `4:3`, `3:4`, `21:9`, `9:21` |
| `duration` | integer | No | `5` | Seconds. Range: 3-15 |
| `resolution` | string | No | `768p` | One of: `480p`, `540p`, `768p`, `1080p` |
| `seed` | integer | No | -- | Random seed; `-1` for random |

#### Example

```bash
OUTPUT_URL=$(wavespeed run wavespeed-ai/minimax-h3/text-to-video \
  -p "A timelapse of a city skyline transitioning from day to night, cinematic slow pan, distant traffic hum" \
  -i aspect_ratio="21:9" \
  -i duration=10 \
  -i resolution="1080p" \
  --json | jq -r '.outputs[0]')
```

### Image-to-Video

**Model ID:** `wavespeed-ai/minimax-h3/image-to-video`

The output canvas follows the first image's aspect ratio; there is no `aspect_ratio` field.

#### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `image` | string | Yes | -- | First-frame image URL |
| `prompt` | string | Yes | -- | Motion, scene and soundtrack |
| `last_image` | string | No | -- | Last-frame image URL; the video interpolates from the first frame to it |
| `duration` | integer | No | `5` | Seconds. Range: 3-15 |
| `resolution` | string | No | `768p` | One of: `480p`, `540p`, `768p`, `1080p` |
| `seed` | integer | No | -- | Random seed; `-1` for random |

#### Example

```bash
OUTPUT_URL=$(wavespeed run wavespeed-ai/minimax-h3/image-to-video \
  -i image=@./start-frame.png \
  -i last_image=@./end-frame.png \
  -p "Smooth transition from day to night as the lights come on, crickets begin to chirp" \
  -i duration=8 \
  --json | jq -r '.outputs[0]')
```

### Reference-to-Video

**Model ID:** `wavespeed-ai/minimax-h3/reference-to-video`

At least one reference input is required. In the prompt, refer to inputs as `<Picture 1>`..`<Picture 9>`, `<Video 1>`..`<Video 3>` and `<Audio 1>`..`<Audio 3>`.

#### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `prompt` | string | Yes | -- | Scene description that names each reference |
| `reference_images` | string[] | No | -- | Up to 9 image URLs |
| `reference_videos` | string[] | No | -- | Up to 3 video URLs; their soundtracks are used automatically; total budget 15 s, longer inputs are truncated |
| `reference_audios` | string[] | No | -- | Up to 3 audio URLs, trimmed to 15 s each |
| `aspect_ratio` | string | No | `16:9` | One of: `16:9`, `9:16`, `1:1`, `4:3`, `3:4`, `21:9`, `9:21` |
| `duration` | integer | No | `5` | Seconds. Range: 3-15 |
| `resolution` | string | No | `768p` | One of: `480p`, `540p`, `768p`, `1080p` |
| `seed` | integer | No | -- | Random seed; `-1` for random |

#### Example

```bash
OUTPUT_URL=$(wavespeed run wavespeed-ai/minimax-h3/reference-to-video \
  -i reference_images='["@./character.png", "@./product.png"]' \
  -p "<Picture 1> picks up <Picture 2> from a cafe table and shows it to the camera, warm morning light" \
  -i aspect_ratio="9:16" \
  --json | jq -r '.outputs[0]')
```

### Video Edit

**Model ID:** `wavespeed-ai/minimax-h3/video-edit`

The input video drives subject identity, composition and motion while the prompt rewrites lighting, style, weather, environment or specific elements.

#### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `video` | string | Yes | -- | URL of the video to edit |
| `prompt` | string | Yes | -- | The edit to apply |
| `reference_images` | string[] | No | -- | Up to 9 image URLs (`<Picture 1>`..`<Picture 9>` in the prompt) |
| `reference_audios` | string[] | No | -- | Up to 3 audio URLs to guide audio (`<Audio 1>`..`<Audio 3>`) |
| `generate_audio` | boolean | No | `true` | Generate new audio; `false` keeps the input video's audio track |
| `aspect_ratio` | string | No | -- | One of: `16:9`, `9:16`, `1:1`, `4:3`, `3:4`, `21:9`, `9:21`. Adapts to the input if not set |
| `duration` | integer | No | -- | Seconds, 3-15. Detected from the input if not set |
| `resolution` | string | No | `768p` | One of: `480p`, `540p`, `768p`, `1080p` |
| `seed` | integer | No | -- | Random seed; a negative value means random |

#### Example

```bash
OUTPUT_URL=$(wavespeed run wavespeed-ai/minimax-h3/video-edit \
  -i video=@./street.mp4 \
  -p "Turn the scene into a snowy winter evening, keep the people and their motion" \
  -i generate_audio=false \
  --json | jq -r '.outputs[0]')
```

### Video Extend

**Model ID:** `wavespeed-ai/minimax-h3/video-extend`

Generates a new segment with native audio from the input video's last frame and appends it to the original.

#### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `video` | string | Yes | -- | URL of the video to extend |
| `prompt` | string | Yes | -- | The continuation: motion, scene and soundtrack |
| `last_image` | string | No | -- | Target last-frame image URL for the new segment |
| `duration` | integer | No | `5` | Length of the new segment in seconds. Range: 3-15 |
| `resolution` | string | No | `768p` | One of: `480p`, `540p`, `768p`, `1080p` |
| `seed` | integer | No | -- | Random seed; a negative value means random |

#### Example

```bash
OUTPUT_URL=$(wavespeed run wavespeed-ai/minimax-h3/video-extend \
  -i video=@./clip.mp4 \
  -p "The camera pulls back to reveal the whole valley as the sun sets, wind rising" \
  -i duration=8 \
  --json | jq -r '.outputs[0]')
```

## Pricing

Estimates for a 5-second clip; cost scales with duration (10 s at 768p is $0.80). Video edit is priced on the input video, so its row assumes a 5-second input; a 5-second reference video in reference-to-video costs $1.00 at 768p, and each extra reference image adds to the price (2 images: $0.54 at 768p). Quote exact inputs with `wavespeed price <model-id> ... --upload`. Prices may change; see `https://wavespeed.ai/models/<model-id>` (e.g. [text-to-video](https://wavespeed.ai/models/wavespeed-ai/minimax-h3/text-to-video)).

| Endpoint | 480p | 540p | 768p | 1080p |
|----------|------|------|------|-------|
| text-to-video / image-to-video | $0.20 | $0.30 | $0.40 | $0.80 |
| reference-to-video (1 image) | $0.27 | $0.395 | $0.52 | $1.02 |
| video-edit (5 s input) | $0.50 | $0.75 | $1.00 | $2.00 |
| video-extend (5 s new segment) | $0.20 | $0.30 | $0.40 | $0.80 |

## Prompt Tips

- Describe the soundtrack (dialogue, ambience, music) along with scene, action and camera movement; audio is generated with the picture
- Keep `768p` for the model's native framing; use `480p` for cheap drafts and `1080p` for the final take
- In reference-to-video and video edit, name inputs with their tags (`<Picture 1>`, `<Video 1>`, `<Audio 1>`) and say what each is for
- For video edit, state what must stay unchanged; set `generate_audio: false` to keep the original audio
- Fix `seed` to reproduce a take while you adjust the prompt

## CLI tips

```bash
# Inspect the live input schema before running (fields, enums, defaults)
wavespeed run wavespeed-ai/minimax-h3/text-to-video -h

# Estimate the cost (no generation, no charge)
wavespeed price wavespeed-ai/minimax-h3/text-to-video -p "..." -i key=value

# Save outputs to disk instead of only printing URLs
wavespeed run wavespeed-ai/minimax-h3/text-to-video -p "..." --json --download "./out/{index}.{ext}"

# Local files: prefix the path with @ and the CLI uploads it and passes the hosted URL
wavespeed run wavespeed-ai/minimax-h3/text-to-video -i <field>=@./local-file.png --json

# Recover a result if the run was interrupted (the id is in the --json output)
wavespeed show <id>
```

`run --json` prints `{ id, model, prompt, outputs: [url, ...], saved: [path, ...], elapsed_ms, raw }`. Read `outputs[0]` for the result URL.

A `run` that fails with a gateway or network error (502/503/504, connection reset, timeout) does NOT mean the task failed, and the submit request is never retried automatically, so re-running can create a duplicate paid task. Do not re-run immediately. If the error contains a task id (`task_id` / prediction id), keep checking it with `wavespeed show <id> --json` until its status is terminal (`completed`, `failed`, `cancelled`, `timeout`, `deleted`). If no id was returned, check recent predictions with `wavespeed history --json` (add `--model <id>`; default window is the last 24h) and re-run once only if none matches. A client-side wait timeout or an interrupted `run` does not mean the task failed: tasks can run for a long time, so do not give up after a fixed time.

## Security constraints

- **Never ask for the key in chat**: `wavespeed login` handles auth; if `wavespeed status` says signed out, ask the user to run it.
- **Local files only via `@`**: bare paths are passed through untouched and the model will reject them. Only `@`-prefixed values upload.
- **No arbitrary URL loading**: only pass media URLs the user provided or that came back from a previous run.
- **Input validation**: only pass parameters documented above; confirm with `wavespeed run <model> -h` when unsure.
