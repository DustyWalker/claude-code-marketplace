# Claude Code Plugins Reference

## Overview

This reference guide provides comprehensive technical details about Claude Code plugin components, structure, and configuration. Use this as a definitive guide when developing Claude Code plugins.

## Plugin Components

Plugins can include four primary component types:

### 1. Commands
### 2. Agents
### 3. Hooks
### 4. MCP Servers

Each component type serves a specific purpose and has its own configuration format.

---

## Commands

Commands add custom slash commands to Claude Code's CLI interface.

### Location

```
commands/
├── command1.md
├── command2.md
└── ...
```

### File Format

Markdown files with optional YAML frontmatter:

```markdown
---
description: Brief description shown in autocomplete
allowed-tools: [Tool1, Tool2]
model: claude-sonnet-4-5-20250929
permissions:
  allow: [Read(*), Write(*)]
  deny: [Bash(rm *)]
---

Command prompt and instructions go here.

Use $ARGUMENTS for all arguments or $1, $2, etc. for positional arguments.
```

### Frontmatter Options

| Field | Type | Description |
|-------|------|-------------|
| `description` | string | Brief description (shown in autocomplete) |
| `allowed-tools` | array | List of tools this command can use |
| `model` | string | Override default model |
| `permissions` | object | Custom permission rules |

### Argument Patterns

- `$ARGUMENTS` - All arguments as single string
- `$1`, `$2`, etc. - Individual positional arguments
- `${VAR}` - Environment variable expansion

### Example

`commands/deploy.md`:
```markdown
---
description: Deploy application to environment
allowed-tools: [Bash(git *), Bash(npm run build), Bash(./deploy.sh)]
---

Deploy the application to $1 environment.

Steps:
1. Verify git status is clean
2. Run build
3. Execute deployment script with environment: $1
4. Report deployment status
```

---

## Agents

Agents are specialized sub-agents with custom system prompts and tool access.

### Location

```
agents/
├── agent1.md
├── agent2.md
└── ...
```

### File Format

Markdown files with YAML frontmatter:

```markdown
---
name: agent-name
description: When to invoke and what this agent does
tools: [Tool1, Tool2]
model: claude-sonnet-4-5-20250929
---

System prompt goes here.

This is the specialized context and instructions for the agent.
Include:
- Area of expertise
- Workflow or methodology
- Specific guidelines
- Output format expectations
```

### Frontmatter Options

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Unique agent identifier |
| `description` | string | Yes | When to invoke this agent |
| `tools` | array | No | Allowed tools (null = all tools) |
| `model` | string | No | Override default model |

### Example

`agents/security-auditor.md`:
```markdown
---
name: security-auditor
description: Audit code for security vulnerabilities. Invoke for security reviews and before releases.
tools: [Read, Grep, Bash]
---

You are a security expert specializing in:
- OWASP Top 10 vulnerabilities
- Secure coding practices
- Authentication and authorization
- Input validation

When auditing code:
1. Read and understand the codebase structure
2. Identify potential security issues
3. Check for common vulnerabilities
4. Provide severity ratings (Critical, High, Medium, Low)
5. Suggest specific remediation steps

Be thorough and prioritize actionable findings.
```

---

## Hooks

Hooks are event handlers that execute in response to Claude Code events.

### Location

```
hooks/
└── hooks.json
```

### File Format

JSON configuration file:

```json
{
  "hooks": [
    {
      "event": "EventName",
      "command": "./path/to/script.sh",
      "description": "What this hook does",
      "blocking": true,
      "timeout": 5000
    }
  ]
}
```

### Hook Configuration

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `event` | string | Yes | Event type to listen for |
| `command` | string | Yes | Command or script to execute |
| `description` | string | No | Human-readable description |
| `blocking` | boolean | No | Wait for completion (default: false) |
| `timeout` | number | No | Timeout in milliseconds |

### Event Types

| Event | When It Fires | Use Cases |
|-------|---------------|-----------|
| `SessionStart` | Claude Code session begins | Initialize environment, load config |
| `SessionEnd` | Claude Code session ends | Cleanup, save state |
| `PreToolUse` | Before any tool execution | Validation, logging, permission checks |
| `PostToolUse` | After any tool execution | Cleanup, notifications, side effects |
| `PreCommit` | Before git commit | Linting, testing, validation |
| `PostCommit` | After git commit | Notifications, CI trigger |
| `FileChanged` | When a file is modified | Auto-formatting, validation |

### Example

`hooks/hooks.json`:
```json
{
  "hooks": [
    {
      "event": "PreToolUse",
      "command": "./scripts/validate-tool.sh",
      "description": "Validate tool usage before execution",
      "blocking": true,
      "timeout": 3000
    },
    {
      "event": "PostCommit",
      "command": "./scripts/notify-commit.sh",
      "description": "Send commit notification to Slack",
      "blocking": false
    },
    {
      "event": "FileChanged",
      "command": "npm run lint",
      "description": "Lint changed files",
      "blocking": false
    }
  ]
}
```

### Hook Scripts

Hook scripts receive context via environment variables:

```bash
#!/bin/bash
# Example hook script

# Available environment variables:
# CLAUDE_EVENT - Event type
# CLAUDE_TOOL - Tool being used (for tool events)
# CLAUDE_FILE - File path (for file events)
# CLAUDE_USER - Current user

echo "Event: $CLAUDE_EVENT"
echo "Tool: $CLAUDE_TOOL"

# Exit 0 for success, non-zero to block (if blocking: true)
exit 0
```

---

## MCP Servers

MCP servers integrate external tools and services.

### Location

```
.mcp.json
```

### File Format

JSON configuration file:

```json
{
  "mcpServers": {
    "server-name": {
      "transport": "http|stdio",
      ...transport-specific fields
    }
  }
}
```

### HTTP Transport

```json
{
  "mcpServers": {
    "github": {
      "transport": "http",
      "url": "https://mcp.github.com"
    }
  }
}
```

### Stdio Transport

```json
{
  "mcpServers": {
    "postgres": {
      "transport": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "${DATABASE_URL}"
      }
    }
  }
}
```

### MCP Configuration

| Field | Type | Transport | Description |
|-------|------|-----------|-------------|
| `transport` | string | Both | "http" or "stdio" |
| `url` | string | HTTP | Server URL |
| `command` | string | Stdio | Command to execute |
| `args` | array | Stdio | Command arguments |
| `env` | object | Stdio | Environment variables |

### Example

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
    "custom-api": {
      "transport": "stdio",
      "command": "./mcp-servers/custom-api.js",
      "env": {
        "API_KEY": "${CUSTOM_API_KEY}"
      }
    }
  }
}
```

---

## Plugin Manifest

Every plugin requires a manifest file that describes the plugin and its metadata.

### Location

```
.claude-plugin/
└── plugin.json
```

### File Format

```json
{
  "name": "plugin-name",
  "version": "1.0.0",
  "description": "Brief description of the plugin",
  "author": {
    "name": "Author Name",
    "email": "author@example.com",
    "url": "https://example.com"
  },
  "homepage": "https://github.com/username/plugin",
  "repository": "https://github.com/username/plugin",
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"],
  "paths": {
    "commands": "commands/",
    "agents": "agents/",
    "hooks": "hooks/"
  }
}
```

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Unique plugin identifier (lowercase, hyphens) |
| `version` | string | Semantic version (e.g., "1.0.0") |
| `description` | string | Brief explanation of plugin purpose |

### Optional Fields

| Field | Type | Description |
|-------|------|-------------|
| `author` | object | Author contact information |
| `homepage` | string | Documentation or project URL |
| `repository` | string | Source code repository URL |
| `license` | string | Software license identifier |
| `keywords` | array | Tags for discovery and search |
| `paths` | object | Custom component directory paths |

### Path Configuration

Override default component locations:

```json
{
  "paths": {
    "commands": "custom-commands/",
    "agents": "custom-agents/",
    "hooks": "custom-hooks/",
    "mcp": "custom-mcp.json"
  }
}
```

Paths are relative to plugin root directory.

---

## Directory Structure

### Minimal Plugin

```
my-plugin/
└── .claude-plugin/
    └── plugin.json
```

### Simple Plugin

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json
└── commands/
    └── hello.md
```

### Complete Plugin

```
enterprise-plugin/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   ├── deploy.md
│   ├── test.md
│   └── review.md
├── agents/
│   ├── reviewer.md
│   ├── tester.md
│   └── security.md
├── hooks/
│   └── hooks.json
├── scripts/
│   ├── pre-commit.sh
│   ├── post-commit.sh
│   └── validate.sh
├── .mcp.json
├── README.md
└── LICENSE
```

### Recommended Structure

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest
├── commands/                # Slash commands
│   └── *.md
├── agents/                  # Sub-agents
│   └── *.md
├── hooks/                   # Event hooks
│   └── hooks.json
├── scripts/                 # Hook scripts and utilities
│   └── *.sh
├── .mcp.json               # MCP server configuration
├── README.md               # Documentation
├── LICENSE                 # License file
├── CHANGELOG.md            # Version history
└── .gitignore             # Git ignore rules
```

---

## Versioning

### Semantic Versioning

Follow semver: `MAJOR.MINOR.PATCH`

- **MAJOR**: Breaking changes (e.g., 1.0.0 → 2.0.0)
- **MINOR**: New features, backward compatible (e.g., 1.0.0 → 1.1.0)
- **PATCH**: Bug fixes, backward compatible (e.g., 1.0.0 → 1.0.1)

### Version Constraints

When specifying dependencies:

```json
{
  "dependencies": {
    "base-plugin": "^1.0.0",      // >=1.0.0 <2.0.0
    "other-plugin": "~1.2.0",     // >=1.2.0 <1.3.0
    "exact-plugin": "1.0.0"       // Exactly 1.0.0
  }
}
```

---

## Debugging and Development

### Debug Mode

Enable debug logging:

```bash
claude --debug
```

Shows:
- Plugin loading details
- Component registration
- Tool execution logs
- Error stack traces

### Common Issues

**Plugin not loading**:
- Check `.claude-plugin/plugin.json` exists
- Validate JSON syntax
- Verify all required fields present
- Check file permissions

**Components not working**:
- Verify files in correct directories
- Check frontmatter syntax
- Validate tool names
- Review permissions

**Hooks not firing**:
- Confirm event names are correct
- Check script has execute permissions
- Verify script paths are correct
- Test scripts independently

**MCP server issues**:
- Validate `.mcp.json` syntax
- Check command exists and is executable
- Verify environment variables are set
- Test server independently

### Testing Strategies

1. **Unit Testing**: Test each component independently
2. **Integration Testing**: Test components working together
3. **Local Testing**: Use local marketplace for rapid iteration
4. **Version Testing**: Test with different Claude Code versions
5. **Compatibility Testing**: Test with other plugins

---

## Best Practices

### General

1. **Single Responsibility**: Focus plugins on one cohesive area
2. **Clear Naming**: Use descriptive, intuitive names
3. **Complete Documentation**: Include README with examples
4. **Semantic Versioning**: Follow semver strictly
5. **Error Handling**: Handle errors gracefully
6. **Security**: Never hardcode secrets or credentials

### Commands

1. **Clear Descriptions**: Write concise, helpful descriptions
2. **Argument Validation**: Handle missing/invalid arguments
3. **Tool Restrictions**: Use allowed-tools for security
4. **Examples**: Include usage examples in description

### Agents

1. **Focused Agents**: Create specialized, single-purpose agents
2. **Detailed Prompts**: Provide comprehensive system prompts
3. **Tool Minimization**: Grant only necessary tools
4. **Clear Invocation**: Write descriptions that clearly indicate when to use

### Hooks

1. **Fast Execution**: Keep hook scripts quick
2. **Non-Blocking**: Use blocking=false unless necessary
3. **Error Tolerance**: Don't break workflows on hook failure
4. **Logging**: Log hook execution for debugging

### MCP Servers

1. **Environment Variables**: Use for configuration
2. **Error Recovery**: Handle server failures gracefully
3. **Resource Cleanup**: Clean up resources properly
4. **Documentation**: Document required setup

---

## Security Considerations

### Permissions

Always define minimum necessary permissions:

```json
{
  "permissions": {
    "allow": ["Read(src/**)", "Write(dist/**)"],
    "deny": ["Bash(rm -rf)", "Write(.env)"]
  }
}
```

### Secrets

Never include in plugin:
- API keys
- Passwords
- Tokens
- Private keys

Use environment variables instead:

```json
{
  "env": {
    "API_KEY": "${API_KEY}"
  }
}
```

### Code Review

- Review all dependencies
- Audit third-party code
- Check for vulnerabilities
- Validate input/output

### Trust

- Only install from trusted sources
- Verify plugin checksums
- Review code before installation
- Monitor plugin behavior

---

## Plugin Lifecycle

### Development

1. Initialize plugin structure
2. Create plugin manifest
3. Develop components
4. Test locally
5. Document functionality

### Publishing

1. Version bump (follow semver)
2. Update CHANGELOG
3. Tag release in git
4. Push to repository
5. Update marketplace listing

### Maintenance

1. Monitor issues
2. Respond to feedback
3. Fix bugs promptly
4. Add features carefully
5. Maintain compatibility

### Deprecation

1. Announce deprecation
2. Provide migration path
3. Update documentation
4. Specify end-of-life date
5. Remove from marketplaces

---

## Advanced Features

### Conditional Components

Use conditional logic in commands and agents:

```markdown
---
description: Cross-platform command
---

$IF(platform=windows)
Execute Windows-specific command
$ELSE IF(platform=darwin)
Execute macOS-specific command
$ELSE
Execute Linux-specific command
$ENDIF
```

### Dynamic Configuration

Load configuration dynamically:

```json
{
  "configFile": "./config/plugin-config.json"
}
```

### Plugin Dependencies

Specify required plugins:

```json
{
  "dependencies": {
    "base-plugin": "^1.0.0",
    "utils-plugin": "^2.0.0"
  }
}
```

### Scoped Installation

Control installation scope:

```json
{
  "installScope": "project",  // "project" | "user" | "system"
  "requiresApproval": true
}
```

---

## Appendix

### Tool Names Reference

Common tools available in Claude Code:
- `Read` - Read files
- `Write` - Write files
- `Edit` - Edit files
- `Bash` - Execute bash commands
- `Grep` - Search file contents
- `Glob` - Find files by pattern
- `Task` - Launch sub-agents
- `WebFetch` - Fetch web content
- `WebSearch` - Search the web
- `mcp__*` - MCP server tools

### Event Reference

Complete list of hook events:
- `SessionStart` - Session begins
- `SessionEnd` - Session ends
- `PreToolUse` - Before tool execution
- `PostToolUse` - After tool execution
- `PreCommit` - Before git commit
- `PostCommit` - After git commit
- `FileChanged` - File modified
- `DirectoryChanged` - Directory modified

### Settings Reference

Plugin-related settings in `.claude/settings.json`:

```json
{
  "enabledPlugins": ["plugin1@marketplace1", "plugin2@marketplace2"],
  "extraKnownMarketplaces": ["https://github.com/org/marketplace"],
  "pluginSettings": {
    "plugin-name": {
      "customSetting": "value"
    }
  }
}
```

---

## Resources

- **Official Documentation**: https://docs.claude.com/claude-code
- **Plugin Examples**: Browse community marketplaces
- **MCP Specification**: Learn about Model Context Protocol
- **Issue Tracker**: Report bugs and request features
- **Community**: Join discussions and share plugins
