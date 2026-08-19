---
name: myapi-services
description: Overview of MyApi hub capabilities — connected services (GET /services), identity, memory, and automations/triggers. Use after myapi_status when the user asks what is connected or how to automate.
---

# MyApi services hub

Call **`myapi_status`** first. Then use **`myapi_request`**. Paths start with `/`; do not prepend `/api/v1`.

## Connected services

`GET /services` returns native OAuth and Composio-backed services with status. Use the exact id from that list.

Proxy a call with `POST /services/{name}/proxy` and body `{path, method, body, query}` (see the `myapi-request` skill). For GitHub, Gmail, Notion, Slack, calendars, etc., always consult the live list — do not invent toolkit names.

Related:

- `GET /connectors` — OAuth connector status
- `GET /vault/tokens` — stored external API keys
- `GET /vault/credentials` — stored username/password credentials

## Identity

- `GET /identity` — name, email, timezone, bio
- `PUT /identity` — update fields
- `GET /gateway/context` — identity + memory + services + endpoints in one shot
- `GET /personas` — AI personas; the active persona shapes responses

## Memory and knowledge

- `POST /memory` — write a note that persists across sessions
- `GET /brain/knowledge-base` — knowledge-base documents
- `GET /skills` — available MyApi skills (platform skills, not this Cursor plugin folder)

## Automations / triggers

MyApi can run proactive tasks on its own (Pro/Heavy).

- `GET /triggers` — list automations
- `POST /triggers` — create one. Recommended shape:

```json
{
  "name": "Morning briefing",
  "kind": "schedule",
  "schedule": { "type": "daily", "atHour": 7, "atMinute": 0 },
  "timezone": "America/Chicago",
  "actionType": "ai_prompt",
  "action": {
    "prompt": "plain-English task",
    "services": ["gmail", "googlecalendar"]
  }
}
```

`schedule.type` is one of `once` | `interval` | `daily` | `weekly` | `monthly`. `ai_prompt` runs an agent over the owner's **connected** services (e.g. sends mail from their mailbox).

- `POST /triggers/{id}/run` — run now (test)
- `GET /triggers/{id}/runs` — history
- `PATCH /triggers/{id}` / `DELETE /triggers/{id}` — edit / delete

## Other useful endpoints

- `GET /notifications`
- `GET /export` — export all user data
- AFP machines: use `/afp/*` (see `myapi-request`), not `/services/afp/proxy`
