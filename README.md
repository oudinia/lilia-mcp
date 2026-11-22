# Lilia Editor — MCP connector

[Lilia Editor](https://liliaeditor.com/) is a collaborative editor for papers, theses and other typeset documents in science and education. Authors work in a structured block editor; underneath, the document is real LaTeX. This repository is **documentation only**. It describes Lilia’s remote MCP server so that Cursor, Claude, ChatGPT and other MCP clients can connect to it.

The server itself is not in this repository. It is hosted at:

```
https://editor.liliaeditor.com/api/mcp
```

Use that address as a custom connector or custom MCP server, then sign in with OAuth when the app asks. There is no API key to paste, and nothing to install locally.

Full technical reference: [liliaeditor.com/mcp/docs](https://liliaeditor.com/mcp/docs)  
Per-app setup: [editor.liliaeditor.com/connect](https://editor.liliaeditor.com/connect)

## What the connector does

Once you sign in, the client works in **your** Lilia account. It can:

- Create and revise structured documents (headings, paragraphs, equations, theorems, tables, figures, bibliographies and more)
- Paste or import existing LaTeX, an Overleaf project zip, Word, Markdown, and other formats the importer supports
- Compile the document with Lilia’s TeX engine and report errors and warnings by block
- Export a PDF, a single `.tex` file, an Overleaf-style project zip, or Word
- Format a document for a publisher venue (ACM, IEEE, LNCS, Elsevier, APS, AMS, SIAM and others)
- Read comments, leave comments, and restore an earlier version

It sees only documents you can open, and it changes only documents you own or can edit. It cannot share a document or delete one. Every change can be undone in the editor, and versions are kept. You can disconnect an app at any time: Settings → Connected apps → Disconnect.

Lilia is built to work alongside existing LaTeX workflows, including [Overleaf](https://www.overleaf.com/): you can export a project zip and open it there, or import an Overleaf zip into Lilia.

## Connect

Transport is Streamable HTTP (`POST`). Sign-in is OAuth 2.1 with PKCE (S256). Dynamic client registration and client ID metadata documents are supported, so you do not need a client id in advance. Authorization server: `https://auth.liliaeditor.com`. Scopes: `documents:read` and `documents:write` (plus `openid`, `email`, `profile`, `offline_access`).

Without a token the server answers `401` and names its protected-resource metadata, which is how apps discover the sign-in.

### Cursor

Merge this into `~/.cursor/mcp.json`, then reload Cursor and sign in when prompted:

```json
{
  "mcpServers": {
    "lilia": {
      "url": "https://editor.liliaeditor.com/api/mcp"
    }
  }
}
```

The same file lives at the root of this repository as [`.mcp.json`](.mcp.json) (used by [Cursor Directory](https://cursor.directory/)) and as [`examples/cursor-mcp.json`](examples/cursor-mcp.json).

### Claude (custom connector)

In Claude, add a custom connector and paste `https://editor.liliaeditor.com/api/mcp`. Sign in to Lilia when asked, and click **Allow**. Step-by-step screens for Claude and other connector apps are in [`examples/claude-custom-connector.md`](examples/claude-custom-connector.md) and at [editor.liliaeditor.com/connect](https://editor.liliaeditor.com/connect).

### Claude Code

```sh
claude mcp add --transport http lilia https://editor.liliaeditor.com/api/mcp
```

See [`examples/claude-code.sh`](examples/claude-code.sh).

### VS Code

Paste [`examples/vscode-mcp.json`](examples/vscode-mcp.json) into `.vscode/mcp.json`.

### Other clients

ChatGPT, Grok, Gemini CLI, Codex and similar apps that accept a remote MCP URL use the same address. Follow the steps for your app at [editor.liliaeditor.com/connect](https://editor.liliaeditor.com/connect). Do not put a key or token in the config.

## Tools

The server currently exposes **33 tools** (14 read-only, 19 that write; three of the writes are marked destructive and can be undone). Every tool has a title and a read-only or destructive hint. Names and summaries below follow the live connector catalog.

There is no `whoami` tool yet. The connection is always the signed-in user.

### Find and read

| Tool | What it does |
| --- | --- |
| `list_documents` | List the author’s documents (owned and shared), most recently changed first. |
| `get_outline` | A document’s title, kind and class, heading outline, and every block in order as id, type and snippet. Call this first; other tools use these block ids. |
| `get_document` | The whole document in order: each block’s id, type and content. Long documents are paged. For the LaTeX, use `get_latex`. |
| `get_block` | One block’s full type and content by id. |
| `search_document` | Find text in one document; returns matching blocks. |
| `get_latex` | The LaTeX Lilia’s emitter produces for one block or the whole document (what actually compiles here). |
| `get_document_settings` | Page setup: paper, orientation, margins, columns, font, spacing, header, footer and custom preamble. |
| `list_versions` | Saved versions (History), newest first. |
| `list_comments` | Comments on a document, as the editor’s Comments panel shows them. |
| `import_status` | Where an import started with `start_import`, or a PDF or image sent to `import_document`, currently stands. |
| `check_document` | Compile the document with Lilia’s TeX engine and report whether it compiles and prints correctly, with errors and warnings by block. |
| `export_document` | A short-lived download link for PDF, a single `.tex`, an Overleaf-style zip, or Word. |
| `search_help` | Search Lilia’s help articles. |
| `get_help` | One help article by slug. |

### Write and revise

| Tool | What it does |
| --- | --- |
| `create_document` | Create a new document (article, report or book), optionally with a first draft in LaTeX. |
| `add_block` | Insert content: a paragraph, equation, list, table, figure, theorem, quote, code, or any other block the editor supports. |
| `add_section` | Add a section, subsection or subsubsection heading. |
| `edit_block` | Replace one block’s content (and optionally its type) by id. |
| `paste_content` | Paste LaTeX, Markdown, plain text or Lilia text into a document, then check that it still compiles. |
| `reorder_blocks` | Put the document’s blocks in a given order. |
| `remove_block` | Delete one block by id (destructive; can be undone). |
| `rewrite_document` | Replace the whole document body in one step from LaTeX (destructive; can be undone). For a section, use `paste_content`; for small changes, `edit_block`. |
| `set_title` | Set the title, and optionally authors, date, keywords and venue-specific front matter. |
| `set_document_settings` | Change the page setup. Pass only the fields to change. |
| `set_venue` | Format the document for a publisher venue in one step (owner only). |
| `add_reference` | Add a bibliography entry from a DOI, arXiv id or ISBN. |
| `add_figure_image` | Put a PNG, JPEG or PDF image in a figure. |
| `restore_version` | Restore a document to a saved version (destructive; the current state is saved first). |

### Import

| Tool | What it does |
| --- | --- |
| `import_document` | Import a file you already have as a new document: `.tex` or `.md` as text; `.docx`, an Overleaf project zip, a PDF, or a scanned page (`.jpg` or `.png`) as base64. |
| `start_import` | Return a short-lived Lilia drop-page link so the author can upload a file without sending it through the conversation. Then poll `import_status`. |

### Comments

| Tool | What it does |
| --- | --- |
| `add_comment` | Comment on one block, in the signed-in author’s name. |
| `reply_to_comment` | Reply to an existing comment. |
| `resolve_comment` | Mark a comment resolved (comment author or document owner). |

### Prompts

The server also offers six ready-made prompts: `write_paper`, `format_for_venue`, `fix_compile`, `add_references`, `export_files` and `import_and_tidy`.

## Plans

The connector works on a signed-in Lilia account. You can create a **Free** account during sign-in. **Pro** raises the daily connector allowance and the document limit, and unlocks features such as co-authors who can edit.

This listing does not publish prices. Current plans and limits: [liliaeditor.com/#pricing](https://liliaeditor.com/#pricing).

Help tools do not count toward the daily connector allowance. Past the day’s actions, a tool replies with a message your app can read out. There is also a short per-account rate limit; past that the server answers `429` with `Retry-After`.

## Privacy

- Your documents stay in your Lilia account. The connector reads and writes them only when your app calls a tool, as you.
- Sign-in is OAuth. You can disconnect an app at any time under Settings → Connected apps.
- Privacy policy: [editor.liliaeditor.com/legal/privacy](https://editor.liliaeditor.com/legal/privacy)
- Terms of service: [editor.liliaeditor.com/legal/terms](https://editor.liliaeditor.com/legal/terms)

## Support

Help centre: [editor.liliaeditor.com/help](https://editor.liliaeditor.com/help)  
Connect-your-AI guide: [editor.liliaeditor.com/help/connect-claude](https://editor.liliaeditor.com/help/connect-claude)  
Contact form: [editor.liliaeditor.com/help/contact](https://editor.liliaeditor.com/help/contact)

## Registry metadata

[`server.json`](server.json) is Lilia's entry for the [official MCP Registry](https://modelcontextprotocol.io/registry/quickstart) (schema `2025-12-11`), name `com.liliaeditor/lilia`. It describes a remote Streamable HTTP server only: no installable package, no environment variables.

## License

This documentation, the examples and the listing metadata are licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/): see [`LICENSE`](LICENSE). The hosted Lilia server is not part of this repository and is not covered by this license.

## Security

See [`SECURITY.md`](SECURITY.md). This repository must stay free of keys, tokens, private keys and server source.
