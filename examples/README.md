# Setup examples

All of these use only the public remote address. None of them need an API key, client secret or token in the config file. The first tool call opens Lilia’s OAuth sign-in in the browser.

Server address: `https://editor.liliaeditor.com/api/mcp`  
Per-app screenshots and steps: <https://editor.liliaeditor.com/connect>  
Technical reference: <https://liliaeditor.com/mcp/docs>

| File | Client |
| --- | --- |
| [`cursor-mcp.json`](cursor-mcp.json) | Cursor — paste into `~/.cursor/mcp.json` (or merge the `mcpServers.lilia` block). The same block is also at the repo root as [`.mcp.json`](../.mcp.json), which Cursor Directory auto-detects. |
| [`vscode-mcp.json`](vscode-mcp.json) | Visual Studio Code — paste into `.vscode/mcp.json`. |
| [`claude-code.sh`](claude-code.sh) | Claude Code — one command that registers the remote server. |
| [`claude-custom-connector.md`](claude-custom-connector.md) | Claude, and other apps with a custom-connector screen (ChatGPT, Grok, and similar). |

There is no local install and no `npx` or `stdio` command. Lilia’s MCP server is hosted; clients talk to it over Streamable HTTP.
