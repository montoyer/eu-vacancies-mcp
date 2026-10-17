# Installing the EU vacancies tracker in an assistant

The server is hosted. You give your assistant one address and there is nothing to download, no account and no key:

```
https://eujobs.montoyer.com/mcp
```

It speaks Streamable HTTP. Every recent client can use the address as it is. The `npx` tools below only write the configuration for you, or bridge a client that can run local servers only.

## The quickest way: one command for any coding agent

[`add-mcp`](https://add-mcp.com) finds the agents installed on your machine and writes the entry in each one's configuration file. It needs Node.js 18 or later.

```bash
npx add-mcp https://eujobs.montoyer.com/mcp --name eu-vacancies -g
```

`-g` installs it for all your projects. Leave it out to add the server to the project in the current folder only, in files such as `.mcp.json` or `.cursor/mcp.json` that you can commit for your team.

To choose the agents yourself, without the questions:

```bash
npx add-mcp https://eujobs.montoyer.com/mcp --name eu-vacancies -g -a claude-code -a cursor -a vscode -y
```

It knows about twenty agents, among them Claude Code, Claude Desktop, Codex, Cursor, Gemini CLI, GitHub Copilot, VS Code, Windsurf and Zed. `npx add-mcp list-agents` prints them all.

## Chat assistants

Nothing to install. Add the address in the settings.

**Claude** (web, desktop and mobile apps). Settings, then Connectors, then "Add custom connector". Give it a name, paste the address and leave the advanced settings empty. A connector added on the web also appears in the apps.

**ChatGPT.** Settings, then "Apps and connectors". Create a connector with the address and choose no authentication. Depending on your plan, ChatGPT may ask you to turn on developer mode first.

## Coding agents, with their own command

If you prefer not to use `add-mcp`, each of these takes the address directly.

**Claude Code**

```bash
claude mcp add --transport http eu-vacancies https://eujobs.montoyer.com/mcp --scope user
```

**Codex**

```bash
codex mcp add eu-vacancies --url https://eujobs.montoyer.com/mcp
```

**Gemini CLI**

```bash
gemini mcp add --transport http eu-vacancies https://eujobs.montoyer.com/mcp
```

**VS Code** (GitHub Copilot in agent mode)

```bash
code --add-mcp '{"name":"eu-vacancies","type":"http","url":"https://eujobs.montoyer.com/mcp"}'
```

**Cursor.** Open this link, and Cursor offers to add the server:

```
cursor://anysphere.cursor-deeplink/mcp/install?name=eu-vacancies&config=eyJ1cmwiOiJodHRwczovL2V1am9icy5tb250b3llci5jb20vbWNwIn0=
```

Or add it to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "eu-vacancies": {
      "url": "https://eujobs.montoyer.com/mcp"
    }
  }
}
```

**Windsurf.** In `~/.codeium/windsurf/mcp_config.json`, the key is `serverUrl`:

```json
{
  "mcpServers": {
    "eu-vacancies": {
      "serverUrl": "https://eujobs.montoyer.com/mcp"
    }
  }
}
```

## Any other client with a configuration file

Most clients read the same shape:

```json
{
  "mcpServers": {
    "eu-vacancies": {
      "type": "http",
      "url": "https://eujobs.montoyer.com/mcp"
    }
  }
}
```

Some call the type `streamable-http` instead of `http`. The client's documentation says which.

## A client that runs local servers only

Older clients, and some that read `claude_desktop_config.json` only, start servers as local programs and cannot open an address. [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) runs on your machine and passes the messages to the hosted server:

```json
{
  "mcpServers": {
    "eu-vacancies": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://eujobs.montoyer.com/mcp"]
    }
  }
}
```

Restart the client after editing the file. The first start takes a few seconds while `npx` downloads `mcp-remote`.

In Claude Desktop, the Connectors setting above is simpler and needs no Node.js.

## Checking that it works

Ask the assistant: "Which EU competitions are open for applications?" It should call `list_competitions` and answer with links to eujobs.montoyer.com and to the official notices.

To test the server without an assistant, ask it for its tools:

```bash
curl -A "Mozilla/5.0" -s https://eujobs.montoyer.com/mcp -H "Content-Type: application/json" -H "Accept: application/json, text/event-stream" -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

The answer lists `list_vacancies`, `list_competitions`, `get_vacancy` and `get_competition`.

## If it does not work

- **403 from curl or wget.** The host turns away the default user-agent of curl and wget, and requests with none. Pass one with `-A`, as above. MCP clients send their own and are let through.
- **`npx: command not found`.** Install Node.js 18 or later from [nodejs.org](https://nodejs.org), or use your client's own command or settings instead.
- **The assistant does not use the tools.** Check that the server is turned on for the conversation. Claude and ChatGPT let you switch each connector on or off, under the "+" or tools menu of the message box.
- **Answers slow down or stop after many requests.** The address is shared by everyone, and a caller that asks too much, too fast, is held back for a while.
- **Behind a company proxy.** The client must be allowed to reach `eujobs.montoyer.com` on port 443.

Something else: <www@montoyer.com>
