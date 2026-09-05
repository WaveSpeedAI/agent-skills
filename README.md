# WaveSpeed Agent Skills

Agent Skills for generating and editing AI media — image, video, audio, 3D — on the [WaveSpeed](https://wavespeed.ai) platform. One general skill teaches the agent the find → inspect → run workflow on the open-source [`wavespeed` CLI](https://github.com/WaveSpeedAI/wavespeed-cli); thirteen per-model skills carry each model's inputs, pricing, and prompt tips. The bundled [`@wavespeed/mcp`](https://github.com/WaveSpeedAI/mcp-server) server exposes the same platform as MCP tools.

Every skill follows the [Agent Skills](https://agentskills.io) `SKILL.md` format, and the repo root is an [Agent Plugins 1.0](https://agent-plugins.org) package (`plugin.json` + `mcp.json` + `skills/`), so it installs as-is into Kiro, Codex, Qwen Code, OpenHands and any other Agent Plugins consumer.

## Skills

| Skill | What it covers |
|---|---|
| `wavespeed` | The whole catalog: search models, read a model's schema, quote the price, run, upload local files with `@path`. Start here. |
| `wavespeed-seedream-45` | ByteDance Seedream 4.5 text-to-image and image editing |
| `wavespeed-nano-banana-pro` | Google Nano Banana Pro text-to-image and editing, native 4K |
| `wavespeed-nano-banana-2` | Google Nano Banana 2 text-to-image and editing |
| `wavespeed-seedance-15-pro` | ByteDance Seedance 1.5 Pro text-to-video and image-to-video |
| `wavespeed-veo-31-fast` | Google Veo 3.1 Fast text-to-video, image-to-video, video extend |
| `wavespeed-wan-26` | Alibaba Wan 2.6 text-to-video and image-to-video |
| `wavespeed-wan-22-animate` | Wan 2.2 Animate character animation and replacement |
| `wavespeed-infinitetalk-avatar` | InfiniteTalk talking-head video from a portrait and audio |
| `wavespeed-minimax-speech-26` | MiniMax Speech 2.6 text-to-speech |
| `wavespeed-image-upscaler` | Image upscaling to 2K / 4K / 8K |
| `wavespeed-ultimate-video-upscaler` | Video upscaling to 720p / 1080p / 2K / 4K |
| `wavespeed-face-swapper` | Image and video face swap, with consent and misuse guardrails |
| `wavespeed-watermark-remover` | Watermark and overlay removal for media you own, with rights guardrails |

## Install

**Any agent that reads `.agents/skills/` or `.claude/skills/`** (Claude Code, Codex, Cursor, OpenCode, Kimi Code, Antigravity, Copilot CLI, Amp, Droid, ...):

```bash
npx skills add WaveSpeedAI/agent-skills            # all skills
npx skills add WaveSpeedAI/agent-skills --skill wavespeed
```

**Kiro** — Powers panel → *Add Custom Power* → *Import power from GitHub* → `https://github.com/WaveSpeedAI/agent-skills`.

**Qwen Code / Codex / Kimi Code** — the repo is an Agent Plugins package: `qwen extensions install WaveSpeedAI/agent-skills`, or the dedicated packages below.

**OpenClaw** — every skill is on ClawHub under `@wavespeed`: `clawhub install wavespeed`.

**Dedicated packages** (same skills, native packaging):

- Claude Code: [WaveSpeedAI/claude-plugins](https://github.com/WaveSpeedAI/claude-plugins)
- Codex: [WaveSpeedAI/codex-plugin-wavespeed-cli](https://github.com/WaveSpeedAI/codex-plugin-wavespeed-cli)
- Kimi Code CLI: [WaveSpeedAI/wavespeed-kimi-plugin](https://github.com/WaveSpeedAI/wavespeed-kimi-plugin)
- Gemini CLI: [WaveSpeedAI/wavespeed-gemini-extension](https://github.com/WaveSpeedAI/wavespeed-gemini-extension)
- DeepSeek Harness: [WaveSpeedAI/wavespeed-dsh-skill](https://github.com/WaveSpeedAI/wavespeed-dsh-skill)

## Setup

```bash
npm install -g @wavespeed/cli
wavespeed login        # opens https://wavespeed.ai/accesskey and stores the key
```

`WAVESPEED_API_KEY` in the environment also works, and the MCP server shares the CLI's stored login. The skills tell the agent never to ask a user to paste a key into the chat.

## MCP

`mcp.json` starts `@wavespeed/mcp` over stdio with `npx`. Tools: `search_models`, `get_model_schema`, `get_price`, `upload_file`, `run_model`, `get_prediction`, `get_balance`. Each skill's CLI examples map one-to-one onto `run_model` with the same model id and inputs.

## Recommended models

Images: `bytedance/seedream-v5.0-pro`. Video: `wavespeed-ai/minimax-h3/*` as the cheap open-weights starting point, `bytedance/seedance-2.5/*` for the highest quality. Everything else is one `wavespeed models <query>` away.

## Support, privacy, terms

- Support: support@wavespeed.ai · [GitHub issues](https://github.com/WaveSpeedAI/agent-skills/issues)
- Privacy policy: https://wavespeed.ai/static/privacy
- Terms of service: https://wavespeed.ai/static/terms
- Security: see [SECURITY.md](SECURITY.md)

## License

[MIT](LICENSE) — same as the CLI and the MCP server.

---

**[WaveSpeedAI](https://wavespeed.ai/)** — AI image & video generation platform.
Try it in the browser: **[Image generator](https://wavespeed.ai/image-generator)** · **[Video generator](https://wavespeed.ai/video-generator)**
