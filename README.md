# anew

**[anew.page](https://anew.page) makes free webpages that live in the URL.** Give it one self-contained
HTML file and you get back a link that carries the whole page. No account, no hosting, nothing stored.

## Try it

```bash
curl -sS --data-binary @page.html https://anew.page/write
```

The response body is the page's URL. No key, no signup.

## Connect your agent

| Client | Install |
| --- | --- |
| Claude Code | `claude mcp add --transport http anew https://anew.page/mcp` |
| Gemini CLI | `gemini extensions install https://github.com/round/anew.page` |
| Codex | `codex plugin marketplace add round/anew.page` |
| Any Agent Skills client | `npx skills add round/anew.page` |
| Cursor, VS Code, LM Studio, Goose, Kiro | Add `https://anew.page/mcp` as an HTTP MCP server, or use the one-click links at [anew.page](https://anew.page) |

The server exposes two tools: `anew_write` (HTML in, URL out) and `anew_read` (URL in, HTML out).

## Rules of a page

- **Inline everything.** CSS, JS and images go in the file (data: URIs, inline SVG). Webfonts may load from a CDN.
- **4000-byte URL limit.** Pages are Brotli-compressed into the link; past the limit no link is issued and the error says by how much. Post to measure, don't estimate.
- **Links are immutable.** To edit, read the page, change the HTML, write it again. The new link is a new page; the old one keeps working.

## Reference

Full API (HTTP, MCP, A2A, local minting, rate limits): [llms.txt](https://anew.page/llms.txt) ·
[License](LICENSE.md#license) · [Privacy](LICENSE.md#privacy) · [Terms](LICENSE.md#terms)

This repo holds only the plugin, extension and skill manifests and `server.json` (the MCP registry
entry), all pointing at `https://anew.page/mcp`. No product source.
