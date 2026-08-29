# Privacy

*Last updated 16 August 2026*

anew.page turns a self-contained HTML document into a shareable URL. There are no accounts, no
sign-in, and no database of pages. The URL *is* the page — the document is compressed into the link
itself — so there is nothing on the server to look up, hand over, or lose.

## What is collected

- **Nothing you write.** Page content is not stored server-side. It travels in the URL and is
  decoded again when someone opens it.
- **Operational request logs.** Ordinary web-server telemetry for each request: the requested URL,
  the referring page, a shortened user-agent string, and the country your network resolves to.
  Because an anew URL carries the page, the URL recorded for a page you create contains that page's
  content.
- **No cookies, no trackers, no third-party analytics, no advertising identifiers.**

## Why

The logs exist to keep the service working — debugging failed writes, sizing limits, and spotting
abuse. They are not used to build a profile, and they are never sold, rented, or shared for
advertising.

## Who else sees it

anew.page runs on Cloudflare Workers. Cloudflare processes and stores requests on our behalf as our
hosting provider. No other processor receives this data.

## How long it is kept

- Request logs are retained for **three months**, then deleted by Cloudflare.
- A successful write may render a preview screenshot of the page, cached publicly at that page's
  `.png` address for up to **30 days**. Anyone holding the URL can fetch it.
- Page content itself is retained for **no time at all** — it is never written down.

## Your controls

- **Privacy mode** puts the page in the URL fragment, which browsers never transmit. The server does
  not receive the page, so it cannot appear in any log.
- **Deletion** is inherent: stop sharing the URL and the page is unreachable. There is no copy to
  request the removal of.
- **Questions, or a request about your data:** [open an issue](https://github.com/round/anew.page/issues). There is no address to
  write to, and no data held about you for one to be needed.

## Treat published pages as public

Anyone with the URL can open the page and read its source, and the preview screenshot is served
without authentication. Do not put secrets in a page.

## Changes

Material changes will be reflected in the date at the top of this document.
