# Privacy

*Last updated 31 August 2026*

anew.page turns a self-contained HTML document into a URL that carries it. There are no accounts,
no sign-in, and no database of pages — the URL is the page.

## What the server sees

**Normal mode.** Every request — page views, writes, API, MCP, and A2A calls alike — is logged:
the requested URL, the referring page, a shortened user-agent string, the country the network
resolves to, and a timestamp. Because an anew URL carries its page, a logged URL contains that
page's content.

**Privacy mode.** The document travels in the URL fragment, which browsers never send over the
network. The page does not reach the server and cannot appear in any log — enforced by the design,
not by a promise. Only the request for the empty app shell does.

## What the data is used for

Security and performance — spotting abuse, debugging failures, sizing limits — and nothing else.
There is no profiling, no advertising, no sale or sharing of data, no third-party analytics, and
no tracking cookie. The service runs on Cloudflare, which as hosting provider sees the traffic —
IP addresses included — and keeps its own edge logs; anew's logs do not record IPs. No other party
receives the data.

## Retention

Logs sit in Cloudflare's analytics store, which expires them on its own schedule (about three
months at this writing). A successful write may render a screenshot of the page, cached publicly
at the page's `.png` address. Nothing else is stored, and no retention or deletion timeline is
promised.

## No expectation of privacy or security

That is the nature of the service. Anyone holding a URL can open the page and read its source.
URLs leak — into browser history, referrer headers, chat transcripts, and other people's server
logs. The screenshot is served without authentication. No security of any kind is promised, in
either mode. Do not put secrets in a page.

There is nothing to request the deletion of: no account, no stored page, no profile. Stop sharing
a URL and its page is unreachable.

## Contact

The [issue tracker](https://github.com/round/anew.page/issues) of this repository. No response is
promised.

## Changes

Material changes move the date at the top of this document.
