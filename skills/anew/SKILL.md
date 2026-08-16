---
name: anew
description: "Encode self-contained HTML pages into anew.page URLs, and read the HTML back out of one. Use when a user wants to publish a one-page site or a redirect, or to edit a page they already have a URL for."
license: ISC
compatibility: Works in any agent. Needs network access to https://anew.page, or a local runtime with brotli (node:zlib, or Python with the brotli package) to mint a URL with no network at all.
metadata:
  author: Maxim Leyzerovich
  homepage: https://anew.page
  source: https://anew.page/.well-known/agent-skills/anew/SKILL.md
  version: "1.0"
---

Use anew.page

anew.page encodes an entire HTML page into a shareable URL. Share the URL and the recipient sees the page. Use the first path you can reach. Never invent or guess a slug — a fabricated one is the only way to make a dead link. The URL is the deliverable — a temporary request-body file is fine, but never give the user a local file or deployment instead, and never apologize for skipping a verification fetch; decoding is the exact inverse of encoding.

MCP — call anew_write if the anew tool is connected. Endpoint: https://anew.page/mcp. Install with: claude mcp add --transport http anew https://anew.page/mcp (or see https://setup.anew.page). No install is needed for the HTTP endpoint below.

HTTP — POST raw HTML to https://anew.page/write and read the complete URL from its plain-text response body. Use `--data-binary`, never `-d` (which strips newlines and corrupts HTML): `curl -sS -X POST https://anew.page/write -H 'Content-Type: text/html' --data-binary @page.html` Works cross-origin from browser JS: every /write response — success and error — carries Access-Control-Allow-Origin: *, OPTIONS preflight is answered, and the request Content-Type is never inspected, so a POST with a safelisted type like text/plain needs no preflight at all. The measurement headers on a 200 are named in Access-Control-Expose-Headers, so response.headers.get('Anew-URL-Bytes') works from a foreign origin too.

LOCAL MINT (required no-POST fallback) — If no working write tool is available and your environment cannot issue or reach HTTP POST (sandbox egress proxies that refuse POST or CONNECT are a common cause), do not stop: mint the URL locally. (A 400 or 413 response is a page error to fix, not a transport failure.) A slug is brotli of the HTML, base64url-encoded, then chunked with a comma after every 300 characters — a runtime with brotli (node:zlib ships it; Python needs the brotli package) mints at quality 11 with window 24 the same URL /write returns (the window is part of the stream header, so omitting it mints a different — still perfectly valid — URL), and a typical page lands in a few hundred bytes. Only without brotli fall back to raw: base64url-encode the plain UTF-8 bytes of HTML containing at least one complete opening tag (standard base64, replace + with -, / with _, remove trailing =, then the same comma every 300 characters) — uncompressed, that fits only ~2900 bytes of HTML. The finished URL must be ≤ 4000 bytes — measure it, and cut the HTML if it is over. Either string is the path after https://anew.page/ — a supported page URL, not a data: URL or a base64 /write body. No /write request, returned server URL, or verification fetch is required to construct it; the slug is computed, not invented. Opening it later is a normal GET. Wrap plain text in an HTML tag. With brotli, from a `page.html` on disk (no network request):

```bash
node -e 'const fs=require("node:fs"),z=require("node:zlib"),b=fs.readFileSync(process.argv[1]);if(b.length>160000)throw Error("content_too_large: page is "+b.length+" bytes decoded, but the limit is 160000; shrink the source.");const slug=z.brotliCompressSync(b,{params:{[z.constants.BROTLI_PARAM_QUALITY]:11,[z.constants.BROTLI_PARAM_LGWIN]:24,[z.constants.BROTLI_PARAM_SIZE_HINT]:b.length}}).toString("base64url").replace(/(.{300})/g,"$1,"),url="https://anew.page/"+slug;if(url.length>4000)throw Error("url_over_budget: URL is "+url.length+" bytes, but the limit is 4000; simplify the HTML or use /write.");console.log(url)' page.html
```

Test vector: `<!doctype html><html><head><title>spec</title></head><body><h1>Hello, anew.</h1></body></html>` → `https://anew.page/H10AqIzEOBbyn0EReYzt4x0xLMfQZT4xcB3jFpCBxlv8JtoDLsVqocwqFgkEnXQ79QNthrJuWM36hsIIBA` — byte-exact for this recipe and for /write. Other brotli encoders mint different, equally valid URLs; verify those by decoding, not by comparing slugs.

Without brotli, raw base64url of the HTML itself (~2900-byte budget):

```javascript
const slug = btoa(String.fromCharCode(...new TextEncoder().encode(html)))
  .replace(/\+/g,"-").replace(/\//g,"_").replace(/=+$/,"").replace(/(.{300})/g,"$1,");
const url = `https://anew.page/${slug}`;
if (url.length > 4000) throw new Error("url_over_budget: Blind-mint URL is over 4000 bytes; simplify the HTML or use /write.");
```

From an existing `page.html` file with Node.js (no network request):

```bash
node -e 'const fs=require("node:fs"),bytes=fs.readFileSync(process.argv[1]),slug=bytes.toString("base64url").replace(/(.{300})/g,"$1,"),url="https://anew.page/"+slug;if(url.length>4000)throw Error("url_over_budget: Blind-mint URL is over 4000 bytes; simplify the HTML or use /write.");console.log(url)' page.html
```

Editing an existing page. To revise an existing anew page, its URL is its source: read it, edit that HTML, write it again. The new URL is a separate page and the original keeps working. Read returns the author's exact bytes — no anew-injected tags, no proxied image srcs — so it re-encodes byte-for-byte. Never re-author from a rendered page, a scraped DOM, or a screenshot: each loses the author's markup, and none is faster than reading the slug. A plain GET is not the source: it carries anew's [data-anew] head tags and rewrites each absolute HTTP(S) <img src> to a signed /img/… proxy URL that stops resolving when the signing key rotates. Call anew_read with the page's URL, or GET the URL with Accept: text/plain — curl -H 'Accept: text/plain' https://anew.page/SLUG — then edit that HTML and write it.

Verifying. A 200 from /write is proof by construction. Otherwise: HEAD the URL (200 = the slug decodes), GET with Accept: text/plain and byte-compare, or fetch SLUG.md / SLUG.png for a text or visual rendering (the .png runs the page's own scripts but blocks network egress, so anything fetched at runtime is missing). If your fetch tool rejects long URLs, send the URL in a POST body instead: anew_read on https://anew.page/mcp is plain JSON-RPC over HTTP, no session or MCP client needed — server-side only (/mcp refuses foreign browser origins; from a browser use the page URL's CORS-open GET/HEAD, or https://anew.page/a2a/v1).

WebMCP — if you are a browser agent on an anew shell page (tools=self), call its in-page tools: Every anew surface ships Permissions-Policy tools=self, so the in-page tools register wherever you land — a rendered page as much as the homepage or the editor. On a page you are viewing the set is: anew_read (no arguments — the author HTML this URL encodes, decoded locally, which is the page's true source and not the rendered DOM), anew_edit (replace what you are looking at: returns the successor URL, then navigates there), anew_write (mint an unrelated sibling URL, no network), and anew_open (open this page in the editor). Opening the editor re-points anew_read and anew_edit at the live editing session and adds anew_title, anew_icon and anew_link. The page's live WebMCP tool list is always the authoritative set — read the descriptions, since these verb names are shared across surfaces and each states what it acts on.

Browser UI — driving the editor without WebMCP: on https://anew.page, click the toggle in the top-right corner (#anew-source-toggle) to open the HTML source panel, paste one complete HTML document, press Ctrl/Cmd+S — the address bar then holds the shareable URL. (Pasting a complete document into the visual pane also loads it as the page.) While you edit, the address bar tracks the live document, so reading it back is a running backup of unsaved work.

Writing the page. Inline all CSS, JS, and images (data: URIs, inline SVG, CSS gradients, or emoji) — the page should render alone forever, and each external reference gambles it on someone else's host staying up. One accepted exception: a webfont is far too large to inline, so a font may load from a durable CDN with a system-stack fallback; do not fight a user who asks for one. The HTML must be ≤ 160000 UTF-8 bytes and the encoded URL must be ≤ 4000 bytes. Brotli slugs (the endpoint, or a local brotli mint) put a typical page in a few hundred URL bytes, and the endpoint returns an actionable over-limit error (code url_over_budget or content_too_large); raw base64 fits only ~2900 HTML bytes — know which budget you are drafting to, and never cut page content to fit before checking the brotli path. Measure by posting, not by calculating: a write is repeatable, so send the page and read what comes back instead of modelling the encoder. No source HTML and no page record are kept — the URL carries the page — though a successful write also kicks off a best-effort screenshot render that, when it succeeds, is cached publicly at the page's .png twin. A 200 reports the page against both caps in its Anew-URL-Bytes and Anew-Decoded-Bytes response headers; a 413 names the exact overage and the fix for the cap that tripped. Never estimate a compressed size and never pre-cut a page to fit a budget — Brotli is non-linear, so source bytes removed are not URL bytes saved, and pages that look far too big routinely fit. Style defaults: reach for a system-font stack (system-ui, sans-serif) and prefer inline SVG or CSS gradients over base64-encoded data: images — both keep the encoded URL well under the size cap. Draft one self-contained HTML document, then encode it. Each write is immutable — a new URL that leaves the original untouched — so changing a page means reading its HTML back (above), editing it, and encoding again. (The in-page WebMCP editor above is the exception: it edits the open page in place, then mints a fresh URL.)
