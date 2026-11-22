# Claude and other custom-connector apps

Lilia is a **remote** MCP server. In Claude, ChatGPT, Grok and other apps that offer a custom connector or custom MCP server, you add the address; you do not paste a key.

1. Copy the server address: `https://editor.liliaeditor.com/api/mcp`
2. In the app, add a custom connector (Claude, Grok) or a custom MCP server (ChatGPT and similar).
3. Paste the address and save.
4. When the app asks, sign in to Lilia (or create a free account) and click **Allow** on “Connect … to Lilia?”.

Exact menu names differ by app. Follow the screenshots for your app at <https://editor.liliaeditor.com/connect>.

Do not put an API key, bearer token or client secret in the connector settings. Sign-in is OAuth; the app discovers it from the server.
