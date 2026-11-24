# sandfort-mcp

<!-- mcp-name: io.github.malinele/sandfort -->

A stdio [MCP](https://modelcontextprotocol.io) server for a running Sandfort instance. It
exposes sandbox operations as MCP tools, so an agent can create a sandbox, run code and
commands, move files and take snapshots.

Sandfort is a self-hosted sandbox platform for running AI-generated code. The Sandfort
server is source-available (FSL-1.1-Apache-2.0). This package is licensed Apache-2.0.

## Install

```
pip install sandfort-mcp
```

or run it without installing:

```
uvx sandfort-mcp
```

Python 3.12 or newer is required. The package depends on `mcp>=2` and `sandfort-sdk`.

## Configure

Two environment variables are required. The server exits with status 2 and a message on
stderr when either one is missing.

| Variable | Meaning |
|---|---|
| `SANDFORT_BASE_URL` | Base URL of your Sandfort server, for example `http://127.0.0.1:8000` |
| `SANDFORT_API_KEY` | A Sandfort API key (it starts with `sf_`) |

Example entry for an MCP client that takes a JSON config:

```json
{
  "mcpServers": {
    "sandfort": {
      "command": "uvx",
      "args": ["sandfort-mcp"],
      "env": {
        "SANDFORT_BASE_URL": "http://127.0.0.1:8000",
        "SANDFORT_API_KEY": "sf_..."
      }
    }
  }
}
```

## Tools

Sandboxes and snapshots:

- `create_sandbox`: create a sandbox and wait until it is started
- `list_sandboxes`: list live sandboxes (destroyed and deleting ones are left out)
- `delete_sandbox`: delete a sandbox by id
- `create_snapshot`: create a named snapshot from a sandbox

Running code:

- `run_code`: run a code snippet in a sandbox
- `run_command`: run a shell command in a sandbox

Files:

- `upload_text_file`: write a UTF-8 text file
- `download_text_file`: read a UTF-8 text file
- `upload_binary_file`: write a file from base64 content
- `download_binary_file`: read a file as base64
- `list_directory`: list a directory (at most 1000 entries, with a `truncated` flag)
- `stat_path`: describe one file, directory or symlink
- `make_directory`: create a directory, with missing parents by default
- `delete_path`: delete a file or directory (`recursive` is off by default)

Tool errors are returned as text in the form `error: <message>`.

## Limits

- A reachable Sandfort server and an API key are required. This package does not start
  a server.
- File downloads and binary uploads are limited to 1 MiB. Larger files are refused.
  Base64 inflates the payload by about a third.
- The transport is stdio only. There is no HTTP transport.
- The server acts with the scopes of the API key it is given.

## Links

- Website: https://sandfort.dev
- Source: https://github.com/malinele/sandfort-mcp (the Sandfort server lives at https://github.com/malinele/sandfort)
