# anew.page — documentation

anew.page encodes a complete, self-contained HTML document into a shareable URL. The whole page
lives in the link: no account, no database, no deploy step. Open the link and the page is there.
Read the link back and you get the HTML that made it.

## Connecting

The MCP server is at `https://anew.page/mcp` — streamable HTTP, JSON-RPC 2.0, **no
authentication**. In Claude Code:

```bash
claude mcp add --transport http anew https://anew.page/mcp
```

Every other MCP client takes the same URL. One-click install links for Cursor, VS Code, LM Studio
and others are at [anew.page](https://anew.page).

## The two tools

| Tool | In | Out |
|---|---|---|
| `anew_write` | one self-contained HTML document | the anew.page URL carrying it, plus the page's size against both limits |
| `anew_read` | an anew.page URL | the HTML encoded in it |

Both are non-destructive. Every write mints a new URL and leaves any existing one working, so
editing a page is read → edit → write. The original link never breaks.

## Without a client

Nothing has to be installed. Post the HTML and read the URL out of the response body:

```bash
curl -sS -X POST https://anew.page/write -H 'Content-Type: text/html' --data-binary @page.html
```

Use `--data-binary`, not `-d` — `-d` strips newlines and corrupts HTML. To read a page back, `GET`
its URL with `Accept: text/plain`.

## Writing a page that works

- **Self-contained.** Inline the CSS, the scripts, and the images (data URIs, inline SVG, gradients,
  emoji). Anything fetched from elsewhere is a bet on someone else's host.
- **Sized.** The HTML must fit 160,000 bytes and the finished URL 4,000 bytes. Compression is
  non-linear, so do not trim a page before trying it — pages that look far too large routinely fit.
- **Measured, not estimated.** A write is free to repeat. Send the page and read the size headers
  back rather than modelling the encoder.

## Troubleshooting

- **413 on write.** The response names which limit was exceeded and by how much. If it is the URL
  limit, cut markup rather than text; if it is the decoded limit, the page itself is too big.
- **The page renders blank.** Something in it is loading from another origin and was blocked.
  Inline it.
- **A tool call fails from a browser.** `/mcp` refuses foreign browser origins by design. Read the
  page URL directly instead — `GET` and `HEAD` are open cross-origin.

## Full reference

The complete specification, including the offline recipe for environments that cannot issue `POST`,
is at [llms.txt](https://anew.page/llms.txt).
