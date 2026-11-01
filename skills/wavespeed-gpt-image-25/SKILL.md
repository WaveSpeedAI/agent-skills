---
name: wavespeed-gpt-image-25
description: Generate and edit images using OpenAI's GPT Image 2.5, the model behind ChatGPT Images 2.5, via WaveSpeed AI. Two tiers, each with text-to-image and edit endpoints - Sunburst (precision-focused, more time per image for intricate detail) and Flare (fast, balanced, low latency) - with five quality tiers (low to max), 1k / 2k / 4k resolution, 15 aspect ratios and up to 16 reference images for editing. Use when the user wants GPT Image 2.5 to create images from a prompt or edit and combine existing images.
metadata:
  author: wavespeedai
  version: "1.0"
---

# WaveSpeedAI GPT Image 2.5 Image Generation/Editing

Generate and edit images using OpenAI's GPT Image 2.5 (the model behind ChatGPT Images 2.5) via the WaveSpeed AI platform. It comes in two tiers with identical inputs:

- **Sunburst** (`openai/gpt-image-2.5-sunburst/*`): precision-focused; spends more time per image for extra fidelity on intricate detail.
- **Flare** (`openai/gpt-image-2.5-flare/*`): fast, balanced tier for everyday generation at low latency.

## Setup

Install the open-source CLI once and sign in; the CLI stores the key, so never ask the user to paste an API key into the chat:

```bash
npm install -g @wavespeed/cli
wavespeed login          # opens https://wavespeed.ai/accesskey and stores the key
wavespeed status         # confirms you are signed in
```

For CI or one-off shells, `WAVESPEED_API_KEY` in the environment also works.

The same platform is exposed as an MCP server: hosted at `https://api.wavespeed.ai/mcp` (browser sign-in) or locally with `npx -y @wavespeed/mcp@latest`; every example below maps onto `run_model` with the same model id and input fields.

## Quick Start

### Text-to-Image

```bash
OUTPUT_URL=$(wavespeed run openai/gpt-image-2.5-sunburst/text-to-image \
  -p "A minimalist coffee shop logo with clean typography" \
  --json | jq -r '.outputs[0]')
```

### Image Editing

The `images` parameter accepts an array of image URLs (1-16 images). For local files use the `@path` marker; the CLI uploads them for you.

```bash
# Upload a local image to get a URL
OUTPUT_URL=$(wavespeed run openai/gpt-image-2.5-sunburst/edit \
  -i images='["@./photo.png"]' \
  -p "Add warm sunset lighting and lens flare" \
  --json | jq -r '.outputs[0]')
```

Existing URLs work as-is:

```bash
OUTPUT_URL=$(wavespeed run openai/gpt-image-2.5-sunburst/edit \
  -i images='["https://example.com/photo.jpg"]' \
  -p "Add warm sunset lighting and lens flare" \
  --json | jq -r '.outputs[0]')
```

For the Flare tier, replace `sunburst` with `flare` in the model id; the inputs are the same.

### Shortcut commands

`wavespeed generate image "<prompt>" -m gpt-image-2.5` and `wavespeed edit image "<prompt>" --image @./photo.png -m gpt-image-2.5` (MCP `generate_image` / `edit_image` with `model: "gpt-image-2.5"`) use the same model and pick the endpoint automatically (the Sunburst tier).

## API Endpoints

### Text-to-Image

**Model IDs:** `openai/gpt-image-2.5-sunburst/text-to-image`, `openai/gpt-image-2.5-flare/text-to-image`

Generate images from text prompts.

#### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `prompt` | string | Yes | -- | Text description of the image to generate |
| `aspect_ratio` | string | No | `1:1` | One of: `1:1`, `1:2`, `2:1`, `1:3`, `3:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`, `9:21`, `21:9` |
| `resolution` | string | No | `1k` | `1k`, `2k`, `4k` |
| `quality` | string | No | `medium` | Rendering quality tier: `low` (fastest draft), `medium` (balanced), `high`, `xhigh`, `max` (highest fidelity). Higher tiers add detail and cost more. |
| `output_format` | string | No | `png` | `png`, `jpeg`, `webp` |

#### Example

```bash
OUTPUT_URL=$(wavespeed run openai/gpt-image-2.5-sunburst/text-to-image \
  -p "An infographic poster titled 'How Coffee Is Made' with four labeled steps and simple icons" \
  -i aspect_ratio="2:3" \
  -i resolution="2k" \
  -i quality="high" \
  --json | jq -r '.outputs[0]')
```

### Image Editing

**Model IDs:** `openai/gpt-image-2.5-sunburst/edit`, `openai/gpt-image-2.5-flare/edit`

Edit one or more reference images from natural-language instructions (up to 16 images).

#### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `prompt` | string | Yes | -- | Text description of the desired edit |
| `images` | string[] | Yes | -- | URLs of input images (max 16). Must be publicly accessible; use `@path` for local files. |
| `aspect_ratio` | string | No | -- | Same values as text-to-image. If omitted, it is determined automatically from the input image. |
| `resolution` | string | No | `1k` | `1k`, `2k`, `4k` |
| `quality` | string | No | `medium` | `low`, `medium`, `high`, `xhigh`, `max` |
| `output_format` | string | No | `png` | `png`, `jpeg`, `webp` |

#### Example

```bash
OUTPUT_URL=$(wavespeed run openai/gpt-image-2.5-flare/edit \
  -i images='["@./product.png"]' \
  -p "Place the product on a marble countertop in soft morning light" \
  -i quality="medium" \
  -i output_format="jpeg" \
  --json | jq -r '.outputs[0]')
```

#### Multi-Image Editing

```bash
OUTPUT_URL=$(wavespeed run openai/gpt-image-2.5-sunburst/edit \
  -i images='["@./face.png", "@./scene.png"]' \
  -p "Place the person from the first image into the scene from the second image" \
  -i aspect_ratio="16:9" \
  --json | jq -r '.outputs[0]')
```

## Pricing

Estimates from `wavespeed price` (per image, USD); the current estimates are the same for Sunburst and Flare. Prices may change; check `wavespeed price <model-id> ...` or the model pages: [Sunburst text-to-image](https://wavespeed.ai/models/openai/gpt-image-2.5-sunburst/text-to-image), [Sunburst edit](https://wavespeed.ai/models/openai/gpt-image-2.5-sunburst/edit), [Flare text-to-image](https://wavespeed.ai/models/openai/gpt-image-2.5-flare/text-to-image), [Flare edit](https://wavespeed.ai/models/openai/gpt-image-2.5-flare/edit).

Text-to-image:

| `quality` | `1k` | `2k` | `4k` |
|-----------|------|------|------|
| `low` | $0.01 | $0.02 | $0.03 |
| `medium` | $0.024 | $0.04 | $0.07 |
| `high` | $0.09 | $0.15 | $0.27 |
| `xhigh` | $0.16 | $0.27 | $0.48 |
| `max` | $0.36 | $0.60 | $1.00 |

Edit adds about $0.015 per input image to the text-to-image price (1 image, `medium`, `1k`: $0.039; 4 images: $0.084; 1 image, `high`, `2k`: $0.165).

## Prompt Tips

- Write the prompt as plain natural language; describe subject, setting, composition and style in full sentences.
- Put exact text to render in quotes (titles, labels, slogans).
- Start with `quality=low` or `medium` to iterate, then rerun the chosen prompt at `high` or above; cost grows quickly with quality and resolution.
- Set `aspect_ratio` explicitly for text-to-image; for edits leave it unset to keep the input image's shape.
- With several references, say which image supplies what ("the person from the first image", "the background of the second image").

## CLI tips

```bash
# Inspect the live input schema before running (fields, enums, defaults)
wavespeed run openai/gpt-image-2.5-sunburst/text-to-image -h

# Estimate the cost (no generation, no charge)
wavespeed price openai/gpt-image-2.5-sunburst/text-to-image -p "..." -i key=value

# Save outputs to disk instead of only printing URLs
wavespeed run openai/gpt-image-2.5-sunburst/text-to-image -p "..." --json --download "./out/{index}.{ext}"

# Local files: prefix the path with @ and the CLI uploads it and passes the hosted URL
wavespeed run openai/gpt-image-2.5-sunburst/text-to-image -i <field>=@./local-file.png --json

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
