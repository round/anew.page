# Security

## Reporting

Report a vulnerability by opening a [security advisory](https://github.com/round/anew-plugin/security/advisories/new)
on this repository. Please do not open a public issue for anything exploitable.

## What this server does

`https://anew.page/mcp` is unauthenticated. It takes a self-contained HTML document and returns
a URL that carries that document, and it takes such a URL and returns the HTML back. There are
no accounts, no sessions, and no stored page content — the URL is the storage. Nothing a caller
sends is retained server-side beyond the request.

A successful write also kicks off a best-effort screenshot render, cached publicly at the page's
`.png` twin. Treat anything you publish as public.

## Scope

Both tools are annotated `readOnlyHint: true`. That is deliberate and worth stating plainly: a
write mints a new URL and never mutates or deletes anything that already exists. There is no
destructive operation in this server's surface.

Pages are rendered on `anew.page` and are subject to that origin's sandboxing. A page you did
not author is untrusted content — the same posture you would take toward any link.
