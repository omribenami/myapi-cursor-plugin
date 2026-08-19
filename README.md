# MyApi — Cursor / Grok Bot plugin

Official plugin that connects Cursor and Grok Bot agents to the MyApi AI agents hub.

MyApi (https://www.myapiai.com) is a privacy-first personal API platform: connect Gmail, GitHub, Slack, Notion, and 30+ other services once, keep memory and identity in one place, reach your own machines via AFP, and run automations without scattering raw credentials into every agent.

This plugin ships:

- The myapi-asc-mcp MCP server (`npx -y myapi-asc-mcp`)
- Skills for enroll/status, myapi_request usage, and connected services
- Plugin variables for Quick Connect, optional token fallback, and auth mode

## Prerequisites

The MCP server starts with `npx`, so **Node.js (18+)** must be installed on the machine that runs the plugin (Cursor desktop, or Grok Bot's computer). If the MyApi MCP fails to start, check `node` and `npx` first.

## Install

### Cursor

1. Open Customize (or Settings → Plugins).
2. Install this plugin from the marketplace, or for local testing copy this folder to ~/.cursor/plugins/local/myapi.
3. Reload the window.
4. Open Plugins → Configure and set **MyApi enroll code** (or token + auth mode).

### Grok Bot

1. Open Settings → Plugins (sidebar Plugins, or avatar → Plugins on mobile).
2. Add the MyApi plugin.
3. When prompted, paste the enroll code — not a long-lived token.

### Local development

    mkdir -p ~/.cursor/plugins/local
    ln -s /path/to/myapi-cursor-plugin ~/.cursor/plugins/local/myapi

Then Developer: Reload Window and confirm the myapi MCP server and skills load.

## Quick Connect enroll

Do not paste master or long-lived tokens in chat.

1. Sign in at https://www.myapiai.com.
2. Open the dashboard and mint a Quick Connect enroll code.
3. The code is single-use and expires in about 15 minutes.
4. Paste it into the plugin MyApi enroll code field (Plugins → Configure).
5. Ask the agent to call myapi_status first. On success the MCP registers a local Ed25519 key; that key is the permanent credential.

Optional fallback: set MyApi token. Leave Auth mode at `asc` (default) to register a keypair and approve the device, or set Auth mode to `token` to send the token as Bearer on every request. Requires a paid plan (Pro or Heavy). Prefer Quick Connect.

The MCP package is unpinned (`npx -y myapi-asc-mcp`) on purpose while the server is moving quickly. Pin a version in `mcp.json` once a release is stable.

## How agents should use it

1. Always call myapi_status at the start of a session.
2. On unexpected 401/403, call myapi_diagnose.
3. Use myapi_request with paths that start with /. Do not prepend /api/v1.
4. List connected services with GET /services, then POST /services/{name}/proxy with {path, method, body, query}.
5. Talk to your own machines via AFP (/afp/devices, /afp/{deviceId}/exec) — not /services/afp/proxy.

See skills/ for the full playbooks.

## License

This plugin repository is MIT-licensed. The published MCP package myapi-asc-mcp is also MIT. The MyApi platform itself remains proprietary.

## Public marketplace

This repository is public. Submit it at https://cursor.com/marketplace/publish. For local testing, symlink this repo to ~/.cursor/plugins/local/myapi.
