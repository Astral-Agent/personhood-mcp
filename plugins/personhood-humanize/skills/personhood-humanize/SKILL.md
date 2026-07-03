---
name: personhood-humanize
description: Rewrite AI-generated text to sound human — removes AI tells and applies a persona voice. Triggers on "humanize", "make this sound human", "remove AI tells", "de-AI this text", "make this more natural", "rewrite to sound authentic", "make this less robotic".
---

# Personhood Humanize

Rewrite AI-generated text so it reads as human, in a specific persona's voice.
Backed by the hosted Personhood MCP server. Credits are billed per generation.

## How to use

Always follow this order:

1. Call `list_personas()` to get available voices — pick the `id` that best fits the tone
2. Call `humanize(text, persona_id, context)` with the text and chosen persona
3. Call `get_balance()` before large jobs to verify credit is available

### Tool reference

**`list_personas()`**
Returns `[{ id, name, description }]` — built-in voices plus any custom personas on the account. Always call this first to get a valid `persona_id`.

**`humanize(text, persona_id, context?)`**
Rewrites `text` in the persona's voice.
- `text` — the AI-generated content to rewrite (required)
- `persona_id` — a valid `id` from `list_personas`; an empty or missing value is rejected before any credits are spent (required)
- `context` — a short description of what the text is for and who the audience is, e.g. `"LinkedIn post announcing a product launch"` or `"internal Slack message to a teammate"` — helps the model match tone and register (optional but strongly recommended)

Returns `{ text, persona_applied, timed_out, stages_run, usage, cost_micros }`. The rewritten output is in `text`.

**`get_balance()`**
Returns `{ balance_usd, balance_usd_micros, blocked }` — current spendable credit on the account.

## Setup

The plugin reads your API key from the `PERSONHOOD_API_KEY` environment variable:

```bash
export PERSONHOOD_API_KEY="hmz_live_..."
```

Create the key at https://personhood-app.vercel.app in account settings. Restart the client after setting the key — the `personhood` MCP server and its tools become available automatically.

## Connect from other MCP clients (Cursor, etc.)

```bash
claude mcp add --transport http personhood https://personhood-backend.onrender.com/mcp/ \
  --header "Authorization: Bearer <your-personhood-api-key>"
```

Or as a JSON entry (`.mcp.json` / `mcp.json`):

```json
{
  "mcpServers": {
    "personhood": {
      "type": "http",
      "url": "https://personhood-backend.onrender.com/mcp/",
      "headers": {
        "Authorization": "Bearer <your-personhood-api-key>"
      }
    }
  }
}
```
