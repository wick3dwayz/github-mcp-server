# Configuration File Guide

The GitHub MCP Server supports loading configuration from YAML or JSON files, in addition to command-line flags and environment variables.

## Configuration Priority

Configuration is loaded in the following order (later sources override earlier ones):

1. Default values
2. Configuration file (if present)
3. Environment variables (prefixed with `GITHUB_`)
4. Command-line flags

## Config File Locations

If you don't specify a config file with the `--config` flag, the server will automatically search for `github-mcp-server.yaml` (or `.yml`) in these locations:

1. Current working directory: `./github-mcp-server.yaml`
2. Home directory: `~/github-mcp-server.yaml`
3. Home config directory: `~/.config/github-mcp-server/github-mcp-server.yaml`
4. System config directory: `/etc/github-mcp-server/github-mcp-server.yaml`

The server will use the first config file it finds, or operate without a config file if none is found.

## Specifying a Custom Config File

Use the `--config` flag to specify a custom config file path:

```bash
github-mcp-server stdio --config /path/to/my-config.yaml
```

## Supported Formats

Both YAML and JSON formats are supported:

- YAML: `config.yaml` or `config.yml`
- JSON: `config.json`

## Example Configuration Files

See the example configuration files in the repository root:

- `github-mcp-server.example.yaml` - YAML format with detailed comments
- `github-mcp-server.example.json` - JSON format

## Quick Start

1. Copy the example config file:
   ```bash
   cp github-mcp-server.example.yaml ~/.config/github-mcp-server/github-mcp-server.yaml
   ```

2. Edit the file and set your GitHub token:
   ```yaml
   personal_access_token: "ghp_your_actual_token"
   ```

3. Configure your desired toolsets and options:
   ```yaml
   toolsets:
     - issues
     - pull_requests
     - repos
   read_only: false
   ```

4. Run the server (it will automatically find and use your config file):
   ```bash
   github-mcp-server stdio
   ```

## Environment Variable Mapping

Configuration file keys map to environment variables by:
1. Prefixing with `GITHUB_`
2. Converting to uppercase
3. Replacing hyphens with underscores

Examples:
- `personal_access_token` → `GITHUB_PERSONAL_ACCESS_TOKEN`
- `read-only` or `read_only` → `GITHUB_READ_ONLY`
- `gh-host` or `host` → `GITHUB_HOST`
- `repo-access-cache-ttl` → `GITHUB_REPO_ACCESS_CACHE_TTL`

## Configuration Options

### Required

- `personal_access_token` - GitHub Personal Access Token

### Optional

- `toolsets` - Array of toolset names to enable (default: `[context, issues, pull_requests, repos, users]`)
- `tools` - Array of individual tool names to enable
- `features` - Array of feature flags to enable
- `dynamic_toolsets` - Enable dynamic toolset discovery (default: `false`)
- `read_only` - Restrict to read-only operations (default: `false`)
- `lockdown_mode` - Limit content from non-collaborators in public repos (default: `false`)
- `insiders` - Enable experimental features (default: `false`)
- `host` - GitHub Enterprise hostname (optional)
- `log_file` - Path to log file (optional)
- `enable_command_logging` - Log all commands and responses (default: `false`)
- `export_translations` - Export i18n translations to JSON (default: `false`)
- `content_window_size` - Maximum content window size (default: `5000`)
- `repo_access_cache_ttl` - Repository access cache TTL, e.g., `5m`, `1h` (default: `5m`)

### HTTP Mode Only

These options only apply when running `github-mcp-server http`:

- `port` - HTTP server port (default: `8082`)
- `base_url` - Public server URL for OAuth metadata
- `base_path` - External base path for OAuth metadata
- `scope_challenge` - Enable OAuth scope challenges (default: `false`)

## Mixing Configuration Sources

You can mix configuration sources. For example:

1. Set your token in an environment variable:
   ```bash
   export GITHUB_PERSONAL_ACCESS_TOKEN="ghp_..."
   ```

2. Configure toolsets in a config file:
   ```yaml
   # github-mcp-server.yaml
   toolsets:
     - issues
     - pull_requests
   ```

3. Override read-only mode via command line:
   ```bash
   github-mcp-server stdio --read-only
   ```

## Security Considerations

- **Never commit config files with real tokens to version control**
- Config files in `.` are ignored by `.gitignore` (except `.example` files)
- Consider using environment variables for sensitive values like tokens
- Set appropriate file permissions on config files: `chmod 600 ~/github-mcp-server.yaml`

## Troubleshooting

### Config file not being used

Run with verbose output to see which config file is being loaded:

```bash
github-mcp-server stdio 2>&1 | grep "Using config file"
```

If you see "Using config file: ..." then your config is being loaded.

### Invalid configuration

If your config file has syntax errors or invalid values, the server will fail to start with an error message. Check:

- YAML/JSON syntax is valid
- All array values are properly formatted
- Duration values use valid units (e.g., `5m`, `1h`, `30s`)
- Boolean values are `true` or `false` (not `yes`/`no` in JSON)

### Configuration not taking effect

Remember the priority order:
1. Command-line flags override everything
2. Environment variables override config file
3. Config file overrides defaults

If a value isn't taking effect, check if it's being overridden by a flag or environment variable.
