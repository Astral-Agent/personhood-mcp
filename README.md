# Personhood Claude Code plugin + marketplace

This repo is a self-contained Claude Code **plugin marketplace**. It ships one
plugin, `personhood-humanize`, which bundles:

- the hosted **Personhood MCP connector** (remote streamable-HTTP server at
  `https://personhood-backend.onrender.com/mcp`), and
- a **skill** that triggers on humanize intents and documents usage.

## Layout

```
.
├── .claude-plugin/
│   └── marketplace.json                     # marketplace catalog (lists the plugin)
├── plugins/
│   └── personhood-humanize/
│       ├── .claude-plugin/
│       │   └── plugin.json                   # plugin manifest (references .mcp.json)
│       ├── .mcp.json                         # bundled remote MCP connector
│       └── skills/
│           └── personhood-humanize/
│               └── SKILL.md                  # the humanize skill
└── README.md
```

## Install

```shell
/plugin marketplace add Astral-Agent/personhood-mcp
/plugin install personhood-humanize@personhood
```

Or from a local checkout:

```shell
/plugin marketplace add .
/plugin install personhood-humanize@personhood
```

## Authentication

The bundled connector reads your API key from the `PERSONHOOD_API_KEY` environment
variable (no secret is stored in the plugin):

```bash
export PERSONHOOD_API_KEY="<your-personhood-api-key>"
```

Create the key (it starts with the `hmz_live_` prefix) in Personhood settings, then
restart the client. See the skill's `SKILL.md` for tool usage and manual setup for
other MCP clients (Cursor, etc.).
