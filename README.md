# anew

**[anew.page](https://anew.page) encodes a self-contained HTML document into a shareable URL.**
The whole page lives in the link — no account, no database, no deploy step.

This repository holds the plugin, extension and skill manifests that connect agents to the hosted
anew MCP server at `https://anew.page/mcp`. It contains no product source.

## Tools

- `anew_write` — a self-contained HTML document in, an anew.page URL out.
- `anew_read` — an anew.page URL in, the HTML encoded in it out.

Both are read-only. Every write mints a new URL and leaves the original working, so editing a page
is read → edit → write.

## Install

```bash
claude mcp add --transport http anew https://anew.page/mcp
```

```bash
gemini extensions install https://github.com/round/anew.page
```

```bash
npx skills add round/anew.page
```

Cursor, VS Code, Codex, LM Studio, Goose and Kiro take the server URL directly, or use the
one-click links at [anew.page](https://anew.page). No client is required at all — `POST` raw HTML
to `https://anew.page/write` and the response body is the URL. Full reference:
[llms.txt](https://anew.page/llms.txt).

## Contents

```
.claude-plugin/     Claude Code plugin + marketplace manifests
.mcp.json           Claude Code MCP binding          (type: http)
plugin.json         Agent Plugins 1.0.0 manifest
mcp.json            Agent Plugins MCP binding        (type: streamable-http)
.cursor-plugin/     Cursor plugin + marketplace manifests
gemini-extension.json / GEMINI.md   Gemini CLI extension  (httpUrl)
skills/anew/SKILL.md                Agent Skill
server.json         MCP registry entry
```

The three MCP bindings say the same thing three ways because the ecosystems disagree on the key.
All point at the same endpoint.

## Documentation and policies

[Privacy](PRIVACY.md) · [Terms](TERMS.md) · [Reference](https://anew.page/llms.txt)

## License

© 2026 All Rights Reserved
