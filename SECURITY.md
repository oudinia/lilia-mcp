# Security

This repository holds only public documentation, client setup examples and listing metadata for Lilia Editor’s remote MCP server. It must never contain server source code, API keys, tokens, private keys or environment files.

## Reporting a vulnerability

Please do **not** open a public GitHub issue for a security report.

Report it through Lilia’s help contact form:

- <https://editor.liliaeditor.com/help/contact>

If you can, say that the report is about the MCP connector or this documentation repository, and avoid putting secrets in the form.

Once this repository exists, GitHub’s private vulnerability reporting (Security advisories) can be turned on as a second path. Add that link here when it is available.

## What this server does with access

The connector is a remote MCP server. A client signs the user in with OAuth 2.1 (PKCE) and then acts as that signed-in Lilia account: it can only see documents the user can open, and can only change documents the user owns or can edit. You can disconnect an app at any time in Lilia: Settings → Connected apps → Disconnect.

## This repository

If you find a credential, private key or internal URL in this repository, treat it as a security report and follow the contact path above. Maintainers should rotate anything that was exposed and remove it from git history before the repository is public.
