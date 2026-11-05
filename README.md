# WaveSpeed Agent Skills

Agent Skills for generating and editing AI media — image, video, audio, 3D — on the [WaveSpeed](https://wavespeed.ai) platform. One general skill teaches the agent the image and video shortcuts and the find → inspect → run workflow, on the open-source [`wavespeed` CLI](https://github.com/WaveSpeedAI/wavespeed-cli) or the WaveSpeed MCP tools. The bundled [`@wavespeed/mcp`](https://github.com/WaveSpeedAI/mcp-server) server exposes the same platform as MCP tools.

The skill follows the [Agent Skills](https://agentskills.io) `SKILL.md` format, and the repo root is an [Agent Plugins 1.0](https://agent-plugins.org) package (`plugin.json` + `mcp.json` + `skills/`), so it installs as-is into Kiro, Codex, Qwen Code, OpenHands and any other Agent Plugins consumer.

## Skill

`wavespeed` covers generating and editing images, generating, editing and extending videos with the shortcuts (the same default models as the WaveSpeed /chat page), and every other model in the catalog through one generic run: search models, read a model's schema, quote the price, run, upload local files with `@path`.

## Install

**Any agent that reads `.agents/skills/` or `.claude/skills/`** (Claude Code, Codex, Cursor, OpenCode, Kimi Code, Antigravity, Copilot CLI, Amp, Droid, ...):

```bash
npx skills add WaveSpeedAI/agent-skills
```

**Kiro** — Powers panel → *Add Custom Power* → *Import power from GitHub* → `https://github.com/WaveSpeedAI/agent-skills`.

**Qwen Code / Codex / Kimi Code** — the repo is an Agent Plugins package: `qwen extensions install WaveSpeedAI/agent-skills`, or the dedicated packages below.

**OpenClaw** — on ClawHub under `@wavespeed`: `clawhub install wavespeed`.

**Dedicated packages** (same skill, native packaging):

- Claude Code: [WaveSpeedAI/claude-plugins](https://github.com/WaveSpeedAI/claude-plugins)
- Codex: [WaveSpeedAI/codex-plugins](https://github.com/WaveSpeedAI/codex-plugins)
- Kimi Code CLI: [WaveSpeedAI/wavespeed-kimi-plugin](https://github.com/WaveSpeedAI/wavespeed-kimi-plugin)
- Gemini CLI: [WaveSpeedAI/wavespeed-gemini-extension](https://github.com/WaveSpeedAI/wavespeed-gemini-extension)
- DeepSeek Harness: [WaveSpeedAI/wavespeed-dsh-skill](https://github.com/WaveSpeedAI/wavespeed-dsh-skill)

## Setup

```bash
npm install -g @wavespeed/cli
wavespeed login        # opens https://wavespeed.ai/accesskey and stores the key
```

`WAVESPEED_API_KEY` in the environment also works, and the MCP server shares the CLI's stored login. The skill tells the agent never to ask a user to paste a key into the chat.

## MCP

`mcp.json` starts `@wavespeed/mcp` over stdio with `npx`. Tools: `list_models`, `get_model_schema`, `get_price`, `upload_file`, `run_model`, `generate_image`, `edit_image`, `generate_video`, `edit_video`, `extend_video`, `get_prediction`, `list_predictions`, `delete_predictions`, `get_balance`, `get_usage`, `list_billings`. The skill's CLI commands map one-to-one onto these tools.

## Recommended models

Images: Seedream 5 Pro (`wavespeed generate image` / `edit image`). Video: Seedance 2.5 (`wavespeed generate video`, `edit video`, `extend video`), with Kling 4.0, Wan 3.0 and MiniMax H3 (cheap, native audio, 768p) as alternatives. Everything else is one `wavespeed models <query>` away.

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
