---
name: personhood-humanize
description: Rewrite AI-generated text to sound human — removes AI tells and applies a persona voice. Triggers on "humanize", "make this sound human", "remove AI tells", "de-AI this text".
---

# Personhood Humanize

Rewrite AI-generated text so it reads as human, optionally in a specific persona's
voice. Backed by the hosted Personhood MCP server (`humanize`, `list_personas`,
`get_balance`). Credits are billed per generation on your Personhood account.

## Setup

This plugin bundles the Personhood MCP connector (a remote streamable-HTTP server
at `https://personhood-backend.onrender.com/mcp`). It authenticates with your
personal API key via the `PERSONHOOD_API_KEY` environment variable — no secret is
stored in the plugin.

1. Create a live API key in Personhood settings (it starts with the `hmz_live_`
   prefix).
2. Set it in the environment where Claude Code / Cowork runs:
   ```bash
   export PERSONHOOD_API_KEY="<your-personhood-api-key>"
   ```
3. Restart the client so the connector picks up the key. The `personhood` MCP
   server and its tools become available automatically.

> One-click OAuth for Claude web/desktop connectors is coming (Phase 2). Until
> then, connect with an API key as above.

## Connect from other MCP clients (Cursor, etc.)

Add a remote streamable-HTTP MCP server pointing at the hosted endpoint with your
key in the `Authorization` header:

```bash
claude mcp add --transport http personhood https://personhood-backend.onrender.com/mcp \
  --header "Authorization: Bearer <your-personhood-api-key>"
```

Or, as a JSON entry (`.mcp.json` / `mcp.json`):

```json
{
  "mcpServers": {
    "personhood": {
      "type": "http",
      "url": "https://personhood-backend.onrender.com/mcp",
      "headers": {
        "Authorization": "Bearer <your-personhood-api-key>"
      }
    }
  }
}
```

## Usage

The server exposes three tools:

- `list_personas()` → `[{ id, name, description }]` — the built-in voices plus
  your own custom personas.
- `humanize(text, persona_id?, context?)` → `{ text, persona_applied, timed_out,
  stages_run, usage, cost_micros }` — the humanized rewrite. Omit `persona_id` for
  a neutral rewrite; pass a `persona_id` from `list_personas` to apply a voice.
- `get_balance()` → `{ balance_usd, balance_usd_micros, blocked }` — your current
  spendable credit.

Typical flow:

1. Call `list_personas` and pick a `persona_id` (skip for a neutral rewrite).
2. Call `humanize(text, persona_id, context)` with the text to rewrite.
3. Check `get_balance` before large jobs — each generation spends credit.
