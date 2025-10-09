# Claude Code MCP (Model Context Protocol)

## Overview

The Model Context Protocol (MCP) is an open-source standard that enables Claude Code to connect with external tools, databases, APIs, and services. It provides a unified way to extend Claude's capabilities by integrating with hundreds of data sources and tools.

## What is MCP?

MCP allows developers to:
- Connect Claude Code to external tools and data sources
- Implement features from issue trackers
- Analyze monitoring data from services
- Query databases directly
- Integrate design tools
- Automate complex workflows across multiple services

## Key Capabilities

- **Hundreds of integrations**: Connect to popular services and tools
- **Natural language interface**: Interact with external services through conversation
- **Flexible architecture**: Support for remote and local servers
- **Security**: OAuth 2.0 support and granular permission control
- **Team collaboration**: Share configurations across projects

## Server Types

### 1. Remote HTTP Servers
- Hosted externally
- Access via HTTP/HTTPS
- Support OAuth authentication
- Example: Notion, Linear, Sentry

### 2. Remote SSE Servers (Deprecated)
- Server-Sent Events protocol
- Being phased out in favor of HTTP

### 3. Local Stdio Servers
- Run locally on your machine
- Communicate via standard input/output
- Useful for local databases, file systems, custom tools
- Example: PostgreSQL, SQLite, local scripts

## Installation Methods

### Remote HTTP Server

```bash
claude mcp add --transport http <name> <url>
```

Example:
```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

### Local Stdio Server

```bash
claude mcp add --transport stdio <name> [--env KEY=VALUE] -- <command>
```

Example:
```bash
claude mcp add --transport stdio airtable \
  --env AIRTABLE_API_KEY=YOUR_KEY \
  -- npx -y airtable-mcp-server
```

### With Environment Variables

```bash
claude mcp add --transport stdio postgres \
  --env DATABASE_URL=postgresql://user:pass@localhost/db \
  -- npx -y @modelcontextprotocol/server-postgres
```

## Configuration Scopes

MCP servers can be configured at different levels:

### Local Scope
- **Location**: `.mcp.local.json` (project-specific, private)
- **Use case**: Personal API keys, local development settings
- **Not version controlled**

### Project Scope
- **Location**: `.mcp.json` (shared team configuration)
- **Use case**: Team-wide integrations, shared services
- **Version controlled**

### User Scope
- **Location**: `~/.claude/mcp.json` (cross-project)
- **Use case**: Personal tools used across all projects
- **User-specific**

## Authentication

### OAuth 2.0

For remote HTTP servers that require OAuth:

1. Add the server:
```bash
claude mcp add --transport http github https://mcp.github.com
```

2. Authenticate:
```bash
/mcp
```
Then follow the authentication flow in your browser.

### API Keys

For servers requiring API keys, pass them as environment variables:

```bash
claude mcp add --transport stdio service \
  --env API_KEY=your_key_here \
  -- npx -y service-mcp-server
```

## Practical Examples

### Monitor Errors with Sentry

```bash
claude mcp add --transport http sentry https://mcp.sentry.io/mcp
```

Then ask Claude:
```
Show me the recent errors in our production Sentry project
```

### GitHub Code Reviews

```bash
claude mcp add --transport http github https://mcp.github.com
```

Then ask Claude:
```
Review PR #123 in the main repository
```

### Query PostgreSQL Database

```bash
claude mcp add --transport stdio postgres \
  --env DATABASE_URL=postgresql://localhost/mydb \
  -- npx -y @modelcontextprotocol/server-postgres
```

Then ask Claude:
```
Show me all users who signed up in the last 7 days
```

### Notion Integration

```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp
/mcp  # Authenticate with Notion
```

Then ask Claude:
```
Create a new page in my Tasks database with title "Complete MCP setup"
```

## Managing MCP Servers

### List Servers

```bash
claude mcp list
```
or
```bash
/mcp
```

### Remove a Server

```bash
claude mcp remove <server-name>
```

### Test a Server

After adding a server, test it:
```
Can you list the available tools from the [server-name] MCP server?
```

## Configuration File Format

### HTTP Server Example

`.mcp.json`:
```json
{
  "mcpServers": {
    "notion": {
      "transport": "http",
      "url": "https://mcp.notion.com/mcp"
    }
  }
}
```

### Stdio Server Example

`.mcp.json`:
```json
{
  "mcpServers": {
    "postgres": {
      "transport": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://localhost/mydb"
      }
    }
  }
}
```

### Multiple Servers

`.mcp.json`:
```json
{
  "mcpServers": {
    "github": {
      "transport": "http",
      "url": "https://mcp.github.com"
    },
    "postgres": {
      "transport": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "${DATABASE_URL}"
      }
    },
    "sentry": {
      "transport": "http",
      "url": "https://mcp.sentry.io/mcp"
    }
  }
}
```

## Environment Variable Expansion

Use `${VAR_NAME}` to reference environment variables:

```json
{
  "mcpServers": {
    "service": {
      "transport": "stdio",
      "command": "npx",
      "args": ["-y", "service-mcp"],
      "env": {
        "API_KEY": "${SERVICE_API_KEY}",
        "API_URL": "${SERVICE_URL}"
      }
    }
  }
}
```

## Enterprise Configuration

### Centralized Management

Administrators can:
- Manage MCP servers centrally
- Control which servers employees can access
- Set organization-wide configurations
- Monitor MCP usage

### Disabling MCP

Organizations can disable MCP functionality entirely through enterprise policy settings.

## Security Considerations

1. **Vet MCP servers carefully**: Only install servers from trusted sources
2. **Review permissions**: Understand what data a server can access
3. **Use environment variables**: Don't hardcode sensitive credentials
4. **Limit scope**: Use project-scoped configs when possible
5. **Monitor usage**: Track which servers are being used and how
6. **Regular updates**: Keep MCP servers updated for security patches

## Advanced Configuration

### Output Limits

Control how much output MCP servers can return:

```json
{
  "env": {
    "MAX_MCP_OUTPUT_TOKENS": "10000"
  }
}
```

### Custom Server Development

Developers can create custom MCP servers using the MCP SDK:
- Implement the MCP protocol
- Define custom tools and resources
- Deploy as HTTP or stdio server
- Share with team or community

## Troubleshooting

### Server Not Starting

Check:
- Command path is correct
- Required dependencies are installed
- Environment variables are set
- Permissions are correct

### Authentication Issues

For OAuth:
- Use `/mcp` command to authenticate
- Check browser allows popups
- Verify network connectivity

For API keys:
- Ensure environment variables are set correctly
- Check key has proper permissions
- Verify key hasn't expired

### Connection Errors

- Verify server URL is correct
- Check network/firewall settings
- Confirm server is running and accessible
- Review logs with `claude --debug`

## Popular MCP Servers

- **GitHub**: Pull requests, issues, code search
- **Notion**: Create and query pages and databases
- **Sentry**: Monitor errors and performance
- **PostgreSQL**: Query and update databases
- **Slack**: Send messages and manage channels
- **Linear**: Manage issues and projects
- **Airtable**: Access and update tables
- **Google Drive**: Read and write documents
- **MongoDB**: Database operations

## Best Practices

1. **Start small**: Begin with one or two key integrations
2. **Document setup**: Include MCP setup in project README
3. **Share configs**: Check `.mcp.json` into version control
4. **Use secrets management**: Store sensitive data securely
5. **Test thoroughly**: Verify integrations work before relying on them
6. **Monitor costs**: Some MCP services may have usage costs
7. **Update regularly**: Keep servers and dependencies up to date

## Next Steps

- Explore available MCP servers at [MCP directory]
- Install your first MCP server
- Create a custom MCP server for your specific needs
- Share MCP configurations with your team
- Learn about MCP protocol specification for advanced use cases
