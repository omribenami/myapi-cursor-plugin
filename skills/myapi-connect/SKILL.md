---
name: myapi-connect
description: Connect and verify MyApi in Cursor or Grok Bot. Always call myapi_status first; use Quick Connect enroll; diagnose 401/403; never paste long-lived tokens in chat.
---

# MyApi connect

Use this skill whenever the user wants to install, enroll, reconnect, or debug the MyApi MCP server.

## First action

Always call **`myapi_status`** first in a new session (and after any enroll change). It runs a signed probe and returns identity, connected services, memory, and the endpoint catalog. Do not guess whether the connection works.

The MCP server id is **`myapi`** (the key in `mcp.json`).

## Quick Connect (preferred)

1. User mints a one-time **Quick Connect** code in the [MyApi dashboard](https://www.myapiai.com).
2. Code is **single-use** and expires in about **15 minutes**.
3. User pastes it into plugin config as `MYAPI_ENROLL_CODE` (Customize / Settings → Plugins → Configure) — **not into chat**.
4. Call `myapi_status`. The MCP (`npx -y myapi-asc-mcp`) exchanges the code for a pre-approved Ed25519 key. That key is the permanent credential.

If status still fails, ask the user to mint a **fresh** code (the previous one may already be consumed or expired) and put it in plugin config again.

## Optional token fallback

`MYAPI_TOKEN` is supported by `myapi-asc-mcp` but is second-best. Set both fields in Plugins → Configure:

- Default (`MYAPI_AUTH_MODE=asc`): token is used **once** to register the keypair; the user approves the device at https://www.myapiai.com/dashboard/devices.
- `MYAPI_AUTH_MODE=token`: every request uses the token as Bearer.

Requires a paid plan (Pro or Heavy). Prefer Quick Connect so no long-lived secret sits in chat or logs.

## Diagnose

On unexpected **401** or **403** from `myapi_request`, call **`myapi_diagnose`**. It reports clock skew, signature validity, device approval, plan, and a `next_action` line. Follow that instead of retrying blindly.

Scoped credentials: if `myapi_status` reports **SCOPED** access, 403s outside the grant are expected. Tell the user which scopes are missing; do not retry as if it were a transient error.

## Security rules

- Never ask the user to paste a **long-lived / master token** in chat.
- Never print enroll codes, tokens, or raw key material.
- Do not commit secrets into the repo or into `mcp.json` (placeholders only: `${MYAPI_ENROLL_CODE}`, `${MYAPI_TOKEN}`, `${MYAPI_AUTH_MODE}`).
