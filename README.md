# anew

**[anew.page](https://anew.page) encodes a self-contained HTML document into a shareable URL.**
The whole page lives in the link — no account, no database, no deploy step. Open the link and
the page is there. Read the link back and you get the HTML that made it.

This repository holds the plugin, extension and skill manifests that connect agents to the
hosted anew MCP server. It contains no product source — the server is a hosted endpoint.

## Install

**Claude Code**

```
claude mcp add --transport http anew https://anew.page/mcp
```

**Cursor, VS Code, Codex, LM Studio, Goose, Kiro** — add the remote server URL
`https://anew.page/mcp`, or use the one-click links at [anew.page](https://anew.page).

**Gemini CLI**

```
gemini extensions install https://github.com/round/anew-plugin
```

**Agent Skills**

```
npx skills add round/anew-plugin
```

The skill also installs straight from the site, with no repository involved:
`npx skills add https://anew.page`

## Tools

| Tool | In | Out |
|---|---|---|
| `anew_write` | a self-contained HTML document | the anew.page URL that carries it |
| `anew_read` | an anew.page URL | the HTML encoded in it |

Both are read-only. Writing mints a new URL and never mutates anything that already exists —
nothing is stored server-side, so there is nothing to overwrite. Every write is immutable, which
makes editing a page a read → edit → write cycle that leaves the original URL working.

## No tool required

The MCP server is a convenience, not a dependency. Any HTTP client can publish:

```bash
curl -sS -X POST https://anew.page/write -H 'Content-Type: text/html' --data-binary @page.html
```

The 200 response body is the finished URL. Reading back is a plain `GET` with
`Accept: text/plain`. Full reference, including an offline minting recipe for environments that
cannot issue POST, is at [anew.page/llms.txt](https://anew.page/llms.txt).

## Agent-facing endpoints

| | |
|---|---|
| MCP | `https://anew.page/mcp` — streamable HTTP, JSON-RPC, no auth |
| A2A | [`/.well-known/agent-card.json`](https://anew.page/.well-known/agent-card.json) → `/a2a/v1` |
| OpenAPI | [`/openapi.json`](https://anew.page/openapi.json) |
| Agent Skills | [`/.well-known/agent-skills/index.json`](https://anew.page/.well-known/agent-skills/index.json) |
| llms.txt | [`/llms.txt`](https://anew.page/llms.txt) |
| API catalog | [`/.well-known/api-catalog`](https://anew.page/.well-known/api-catalog) (RFC 9727) |

Rendered anew pages additionally expose in-page WebMCP tools via `document.modelContext`.

## Contents

```
.claude-plugin/plugin.json   Claude Code plugin manifest
.claude-plugin/marketplace.json
.mcp.json                    Claude Code MCP server binding  (type: http)
plugin.json                  Agent Plugins 1.0.0 manifest
mcp.json                     Agent Plugins MCP binding       (type: streamable-http)
.cursor-plugin/              Cursor plugin + marketplace manifests
gemini-extension.json        Gemini CLI extension            (httpUrl)
GEMINI.md                    Gemini CLI context file
skills/anew/SKILL.md         Agent Skill
server.json                  MCP registry entry
```

The three MCP bindings say the same thing three ways because the ecosystems disagree on the
key: Claude wants `"type": "http"`, Agent Plugins and Cursor want `"type": "streamable-http"`,
and Gemini wants `"httpUrl"`. All three point at the same endpoint.

## Security

The server is unauthenticated by design: it accepts HTML and returns a URL, holds no accounts,
and stores no page content. Report anything that looks wrong to
[the issue tracker](https://github.com/round/anew-plugin/issues) — see [SECURITY.md](SECURITY.md).

## License

ISC © Maxim Leyzerovich
