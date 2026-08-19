---
name: myapi-request
description: Call MyApi via myapi_request. Paths start with /; do not prepend /api/v1. Proxy connected services with POST /services/{name}/proxy. AFP uses /afp/* routes, not /services/afp/proxy.
---

# myapi_request

All authenticated MyApi API calls go through the **`myapi_request`** MCP tool (server id typically `user-myapi` or `myapi`).

## Rules

- **`path` must start with `/`.**
- **Do NOT prepend `/api/v1`.** The MCP adds it automatically. Double prefix causes `ROUTE_NOT_FOUND`.
- Always call **`myapi_status`** first in a new session before issuing requests.
- On unexpected 401/403, call **`myapi_diagnose`** instead of guessing.

## Tool arguments

| Field | Required | Notes |
| --- | --- | --- |
| `method` | yes | `GET` `POST` `PUT` `PATCH` `DELETE` |
| `path` | yes | API path starting with `/` |
| `body` | no | JSON object for POST/PUT/PATCH |
| `query` | no | Query string as an object |

## Connected services (OAuth / Composio)

1. `GET /services` — list everything. Use the **exact `id`** from that list (plain toolkit name for Composio, e.g. `gmail`, `notion`, `github`).
2. Optional: `GET /services/{name}/methods` before first use.
3. Proxy any provider call:

```text
POST /services/{name}/proxy
body: { "path": "<provider-native REST path>", "method": "<HTTP>", "body": {}, "query": {} }
```

Examples: `POST /services/github/proxy`, `POST /services/gmail/proxy`. `path` is the **provider** path (e.g. `/repos/owner/repo`), not a MyApi path.

## AFP — the user's own machines

AFP is **not** a connected OAuth service. Do **not** use `/services/afp/proxy`.

| Action | Call |
| --- | --- |
| List machines | `GET /afp/devices` |
| Run a shell command | `POST /afp/{deviceId}/exec` body `{ "cmd": "...", "cwd": optional, "timeout": optional }` |
| List a directory | `GET /afp/{deviceId}/ls` query `{ "path": "..." }` |
| Read a file | `GET /afp/{deviceId}/read` query `{ "path": "..." }` |
| Write a file | `POST /afp/{deviceId}/write` body `{ "path": "...", "content": "..." }` |

Use AFP whenever the user asks about their computer, server, Docker, files, or logs.

## Forbidden over ASC

Do not call `/auth/login`, `/auth/logout`, `/auth/2fa/*`, `/auth/session-token`, or `/oauth/*`. Browser-session paths like `/auth/me` and `/users/me` are rewritten to `/identity`.
