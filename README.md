# Spyglass MCP Server

An MCP server for interacting with the Spyglass AI agent to analyze OpenTelemetry data.

[![Install MCP Server](https://cursor.com/deeplink/mcp-install-dark.svg)](cursor://anysphere.cursor-deeplink/mcp/install?name=spyglass-AI&config=eyJlbnYiOnsiU1BZR0xBU1NfQVBJX0tFWSI6InlvdXIta2V5LWhlcmUifSwiY29tbWFuZCI6InV2IHJ1biBzcHlnbGFzcy1tY3AifQ%3D%3D)

#### ~/.cursor/mcp.json
```json
{
  "mcpServers": {
    "spyglass-ai": {
      "command": "uv",
      "args": ["run", "spyglass-mcp"],
      "env": {
        "SPYGLASS_API_KEY": "your-key-here"
      }
    }
  }
}
```

For local testing, add the `SPYGLASS_AGENT_ENDPOINT` environment variable:
```json
{
  "mcpServers": {
    "spyglass-ai": {
      "command": "uv",
      "args": ["run", "spyglass-mcp"],
      "env": {
        "SPYGLASS_API_KEY": "your-key-here",
        "SPYGLASS_AGENT_ENDPOINT": "http://localhost:8080"
      }
    }
  }
}
```

## Overview

This MCP server provides a simple interface for LLMs to query the Spyglass AI agent. The agent analyzes your telemetry data and provides intelligent insights about application performance, errors, and bottlenecks.

## Installation

```bash
cd spyglass-mcp
uv sync
```

## Usage

The server requires a valid API Key for authentication via the `SPYGLASS_API_KEY` environment variable. You can get one by logging in to spyglass-ai.com and going to the Account tab.

### Set Your API Key

**Option 1: Environment variable**
```bash
export SPYGLASS_API_KEY='your-jwt-token'
```

**Option 2: .env file**
Create a `.env` file in the `spyglass-mcp` directory:
```bash
SPYGLASS_API_KEY=your-jwt-token
```

### Basic Usage (stdio transport)

```bash
uv run spyglass-mcp
```

### HTTP Transport

```bash
uv run spyglass-mcp --transport http --port 8000
```

### Custom Endpoint

For local testing, you can configure a custom endpoint using either an environment variable or command line argument:

**Option 1: Environment variable**
```bash
export SPYGLASS_AGENT_ENDPOINT=http://localhost:8080
uv run spyglass-mcp
```

**Option 2: .env file**
```bash
SPYGLASS_AGENT_ENDPOINT=http://localhost:8080
```

**Option 3: Command line argument**
```bash
uv run spyglass-mcp --endpoint http://localhost:8080
```

Note: Command line arguments take precedence over environment variables.

## Available Tools

### `call_spyglass_agent`

Calls the Spyglass AI agent with a natural language query about your telemetry data.

**Parameters:**
- `query` (string, required): Natural language query about telemetry data

**Example queries:**
- "What are the slowest endpoints in the last hour?"
- "Show me all errors in the checkout service"
- "Which services have the highest error rate?"
- "What's causing high latency in my API?"

**Returns:**
- Natural language analysis of the telemetry data


## Configuration

### Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `SPYGLASS_API_KEY` | Yes | N/A | API Key for authentication with Spyglass agent |
| `SPYGLASS_AGENT_ENDPOINT` | No | `https://agent.spyglass-ai.com` | Agent endpoint URL (useful for local testing) |

### Command Line Arguments

| Argument | Required | Default | Description |
|----------|----------|---------|-------------|
| `--endpoint` | No | `https://agent.spyglass-ai.com` | Spyglass agent endpoint URL |
| `--transport` | No | `stdio` | Transport type (stdio or http) |
| `--port` | No | `8000` | Port for HTTP transport |

## Example: Using with an MCP Client

```python
import asyncio
from fastmcp import Client

client = Client("http://localhost:8000/mcp")

async def analyze():
    async with client:
        result = await client.call_tool("call_spyglass_agent", {
            "query": "What are the slowest endpoints?"
        })
        print(result)

asyncio.run(analyze())
```

## Logging

The MCP server logs to both stderr (captured by Cursor) and a file for debugging:

**Log file location:**
```
~/.spyglass/logs/mcp-server-YYYYMMDD.log
```

**View logs in real-time:**
```bash
tail -f ~/.spyglass/logs/mcp-server-$(date +%Y%m%d).log
```

**Check Cursor's MCP logs:**
```bash
# macOS
tail -f ~/Library/Logs/Cursor/mcp*.log

# Or browse all logs
ls -la ~/Library/Logs/Cursor/
```

The logs include:
- Server startup and configuration
- Incoming queries and responses
- API call details (with truncated tokens for security)
- Error messages and stack traces

## Development

### Project Structure

```
spyglass-mcp/
├── .github/
│   └── workflows/
│       └── publish.yaml
├── src/
│   └── spyglass_mcp/
│       ├── __init__.py
│       └── main.py
├── CHANGELOG.md
├── env.example
├── LICENSE
├── pyproject.toml
├── README.md
└── uv.lock
```
