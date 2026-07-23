# OmniRoute

Unified AI proxy for Home Assistant. Route any LLM through one endpoint — 250+ providers, 90+ free tiers.

Built on [OmniRoute](https://github.com/diegosouzapw/OmniRoute), this addon provides a local proxy that lets any AI tool (Claude Code, Codex, Cursor, etc.) connect to multiple LLM providers through a single API endpoint.

## Features

- **250+ AI providers** — OpenAI, Anthropic, Google, DeepSeek, Groq, and more
- **90+ free tiers** — Many providers available at no cost
- **Auto-fallback** — Quota runs out? Next provider takes over automatically
- **Smart routing** — 18 routing strategies (priority, cost-optimized, weighted, etc.)
- **Token compression** — RTK + Caveman saves 15–95% tokens
- **Encrypted storage** — All API keys and credentials stored encrypted in SQLite

## Installation

1. Add the repository: `https://github.com/grunjol/hassio-repository`
2. Find "OmniRoute" in the App Store and click "Install"
3. Configure the options below and start the addon

## Configuration

### Option: `PORT`

The port OmniRoute listens on. Default is `20128`.

### Option: `AUTH_COOKIE_SECURE`

Set to `true` when running behind an HTTPS reverse proxy (Nginx, Caddy, etc.). Default is `false`.

### Option: `OMNIROUTE_ALLOW_PRIVATE_PROVIDER_URLS`

Set to `true` to connect to local/self-hosted providers like Ollama, vLLM, or LM Studio. Default is `false` (blocks private IPs for security).

### Option: `OMNIROUTE_MEMORY_MB`

Maximum Node.js heap memory in megabytes. Default is `1024` (1 GB). Increase if you experience out-of-memory errors with large prompts or many concurrent connections.

## First-time setup

1. Start the addon
2. Open the OmniRoute dashboard via **Ingress** (OPEN WEB UI button) or directly at `http://homeassistant.local:20128`
3. Login with the default password: `CHANGEME`
4. **Change the password immediately** from Settings → Security
5. Go to **Providers** and add your API keys for OpenAI, Anthropic, Google, etc.
6. All secrets (JWT, encryption keys) are auto-generated on first boot and persisted in `/app/data/`

## Connecting your tools

Once configured, point any OpenAI-compatible tool to:

```
http://homeassistant.local:20128/v1
```

Use `auto` as the model name to let OmniRoute pick the best provider automatically.

### Supported tools include:
- Claude Code, Codex, Cursor, Cline, Copilot, Antigravity
- Any tool that supports custom OpenAI-compatible endpoints
- Home Assistant voice assistants and AI integrations

## Data location

| Data | Path |
|------|------|
| Database (SQLite) | `/app/data/storage.sqlite` |
| Auto-generated secrets | `/app/data/server.env` |
| Encrypted API keys | Stored inside `storage.sqlite` |

## Upgrading

The addon version follows the upstream OmniRoute version. To upgrade:

1. Check for updates in the Home Assistant App Store
2. Install the new version
3. Your data, keys, and configuration are preserved in `/app/data/`

## Support

- [OmniRoute Documentation](https://github.com/diegosouzapw/OmniRoute)
- [OmniRoute Discord](https://discord.gg/EkzRkpzKYt)
- [App Repository](https://github.com/grunjol/hassio-repository)
