# Security

*Last updated 31 August 2026*

anew.page turns a self-contained HTML document into a URL that carries it. There are no accounts,
no credentials, and no stored pages — the URL is the page, so there is no data at rest to breach.

## Reporting

Report vulnerabilities through [private vulnerability reporting](https://github.com/round/anew.page/security/advisories/new)
on this repository. It is the only channel. Reports are read; no response time, disclosure
timeline, or bounty is promised.

## Scope

The service itself: what anew.page serves, its API, MCP, and A2A endpoints, and the editor. Only
the currently deployed service exists — there are no versions to support. Pages are author
content, not the service; a page hosting phishing or malware is abuse, reportable through the
same channel.

## No expectation of security

That is the nature of the service. Pages travel in public URLs: anyone holding a URL holds the
page. Nothing is encrypted at rest because nothing rests. The service is provided as is, with no
security commitment of any kind. Do not put secrets in a page.
