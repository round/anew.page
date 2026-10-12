# anew

**[anew.page](https://anew.page) makes free webpages that live in the URL.** Give it one self-contained
HTML file and you get back a link that carries the whole page. No account, no hosting, nothing stored.

## Try it

```bash
curl -sS --data-binary @page.html https://anew.page/write
```

The response body is the page's URL. No key, no signup.

## Connect your agent

The MCP server is `https://anew.page/mcp`: HTTP transport, no auth, two tools. `anew_write` takes
HTML and returns a URL; `anew_read` takes a URL and returns the HTML. Add the server URL to any client
that accepts one, or install this repository where your client has a plugin, extension or skill
marketplace. The one-click installs are at [anew.page](https://anew.page).

The [skill](skills/anew/SKILL.md) is the full how-to for an agent writing pages. The
[reference](https://anew.page/llms.txt) covers the HTTP, MCP and A2A interfaces, limits and local
minting.

## Rules of a page

- **Inline everything.** CSS, JS and images go in the file (data: URIs, inline SVG). Webfonts may load from a CDN.
- **4000-byte URL limit.** Pages are Brotli-compressed into the link; past the limit no link is issued and the error says by how much. Post to measure, don't estimate.
- **Links are immutable.** To edit, read the page, change the HTML, write it again. The new link is a new page; the old one keeps working.

## Policies

[License](https://anew.page/license) · [Privacy](https://anew.page/privacy) · [Terms](https://anew.page/terms)

This repo holds the plugin, extension and skill manifests and `server.json` (the MCP registry entry),
all pointing at `https://anew.page/mcp`. No product source.
