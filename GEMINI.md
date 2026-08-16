# anew.page

anew.page turns a self-contained HTML document into a shareable URL. The URL *is* the
page — the HTML is compressed into the link itself. Nothing is stored server-side.

## Tools

- `anew_write` — HTML in, an anew.page URL out.
- `anew_read` — an anew.page URL in, the HTML encoded in it out.

Both are read-only: writing a page never mutates anything that already exists. Each
encoding path is deterministic, so the same HTML through the same path mints the same
URL; different paths may mint different, equally valid URLs, so compare pages by their
decoded HTML and never by slug.

## Writing a page

Inline everything — CSS, JS, images (data: URIs, inline SVG, CSS gradients, emoji). A page
that reaches out to another host is a page that dies when that host does. The one accepted
exception is a webfont too large to inline; load it from a durable CDN behind a system-stack
fallback.

Limits: the HTML must be at most 160000 UTF-8 bytes, and the encoded URL at most 4000 bytes.
Brotli puts a typical page in a few hundred URL bytes, so pages that look far too large
routinely fit. Never estimate the compressed size and never trim page content to make room
before you have tried the write — Brotli is non-linear, and source bytes removed are not URL
bytes saved. Measure by writing, then read the error if one comes back; it names the exact
overage and the fix.

## Revising a page

A page's URL is its source. Read it, edit the HTML, write it again. The new URL is a separate
page; the original keeps working.

Never invent or guess a slug. A fabricated slug is the only way to produce a dead link.
Deliver the URL exactly as produced, as `[anew.page link](<https://anew.page/SLUG>)` — the
angle brackets stop chat clients bending the slug's `-` into a smart dash.

Full recipe, including the plain-HTTP and local-mint paths: https://anew.page/llms.txt
