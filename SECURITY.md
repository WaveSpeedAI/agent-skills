# Security Policy

## Scope

This repository is an [Agent Plugins](https://agent-plugins.org) package: a set of skills (`skills/*/SKILL.md`) that teach an agent to use the [`@wavespeed/cli`](https://github.com/WaveSpeedAI/wavespeed-cli), and an MCP server declaration (`mcp.json`) that starts the published npm package [`@wavespeed/mcp`](https://github.com/WaveSpeedAI/mcp-server) with `npx`. It contains instructions and manifests only: no executable code and no secrets. Authentication is handled by the CLI's `wavespeed login` or the `WAVESPEED_API_KEY` environment variable; the general `wavespeed` skill instructs the agent never to ask a user to paste a key into the chat.

Vulnerabilities in the MCP server or the CLI themselves should be reported against their own repositories, linked above.

## Reporting a vulnerability

Email **security@wavespeed.ai** with a description, reproduction steps, and the affected version. Please do not open a public issue for security reports.

We acknowledge reports within 3 business days and aim to ship a fix, or a mitigation and disclosure timeline, within 14 days for confirmed issues.

## Supported versions

Only the latest tagged release receives security fixes.
