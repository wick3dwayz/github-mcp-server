# GitHub MCP Server - JSON Configuration Example

This file (`github-mcp-server.example.json`) demonstrates the JSON configuration format.

## Usage

1. Copy the example file:
   ```bash
   cp github-mcp-server.example.json github-mcp-server.json
   ```

2. Edit the file and replace `"ghp_your_token_here"` with your actual GitHub Personal Access Token

3. Run the server:
   ```bash
   github-mcp-server stdio --config github-mcp-server.json
   ```

## Configuration Options

All fields shown in the example file are optional except `personal_access_token`. See [docs/config-file.md](docs/config-file.md) for detailed documentation of all options.

## Note

For configuration files with comments and better readability, consider using YAML format instead (see `github-mcp-server.example.yaml`).
