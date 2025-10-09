# Claude Code Settings

## Overview

Claude Code settings allow you to configure the behavior, permissions, and environment of your Claude Code CLI. Settings are hierarchical and can be applied at different scopes.

## Settings File Locations

### User Settings (Global)
- **Location**: `~/.claude/settings.json`
- **Scope**: Applies across all projects for your user account

### Project Settings
- **Shared Settings**: `.claude/settings.json`
  - Checked into version control
  - Shared with your team

- **Local Settings**: `.claude/settings.local.json`
  - Not checked into version control
  - Personal project-specific settings

### Enterprise Managed Policy Settings
- Highest precedence
- Managed centrally by administrators

## Settings Precedence

Settings are applied in the following order (highest to lowest priority):
1. Enterprise managed policy settings
2. Project local settings (`.claude/settings.local.json`)
3. Project settings (`.claude/settings.json`)
4. User settings (`~/.claude/settings.json`)

## Key Configuration Options

### 1. Permissions

Control what Claude Code can and cannot do:

```json
{
  "permissions": {
    "allow": ["Bash(npm run lint)", "Bash(git status)"],
    "deny": ["Read(./.env)", "Write(/etc/*)"],
    "ask": ["Bash(rm *)", "Bash(git push)"]
  }
}
```

- **allow**: Permit specific tool uses without confirmation
- **deny**: Block tool uses or file access completely
- **ask**: Request confirmation for certain actions

### 2. Environment Variables

Set environment variables globally or per-project:

```json
{
  "env": {
    "CLAUDE_CODE_ENABLE_TELEMETRY": "1",
    "API_KEY": "your-api-key-here",
    "NODE_ENV": "development"
  }
}
```

Common environment variables:
- API keys for external services
- Telemetry settings
- Model configurations
- Custom application settings

### 3. Plugin Configuration

Control which plugins are active and define additional plugin sources:

```json
{
  "enabledPlugins": ["linter@company-marketplace", "formatter@community"],
  "extraKnownMarketplaces": ["https://github.com/company/plugins"]
}
```

### 4. Advanced Settings

#### Hooks
Custom commands that run before/after tool executions:

```json
{
  "hooks": {
    "pre-tool-use": "echo 'About to run a tool'",
    "post-tool-use": "echo 'Tool execution completed'"
  }
}
```

#### Model Override
Override the default AI model:

```json
{
  "model": "claude-sonnet-4-5-20250929"
}
```

#### Output Style
Adjust system prompt behavior:

```json
{
  "outputStyle": "concise"
}
```

#### Status Line
Configure custom context display:

```json
{
  "statusLine": {
    "enabled": true,
    "format": "{{project}} | {{model}}"
  }
}
```

## Example Configuration

Here's a complete example of a project settings file:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run lint)",
      "Bash(npm test)",
      "Bash(git status)"
    ],
    "deny": [
      "Read(./.env)",
      "Read(./.env.local)",
      "Write(/etc/*)"
    ],
    "ask": [
      "Bash(rm *)",
      "Bash(git push)"
    ]
  },
  "env": {
    "CLAUDE_CODE_ENABLE_TELEMETRY": "1",
    "NODE_ENV": "development"
  },
  "enabledPlugins": [
    "code-reviewer@company-marketplace"
  ],
  "hooks": {
    "pre-tool-use": "./scripts/pre-hook.sh",
    "post-tool-use": "./scripts/post-hook.sh"
  }
}
```

## Notable Features

- **Hierarchical settings**: Clear precedence rules ensure predictable behavior
- **Granular control**: Fine-grained control over tool and file access
- **Enterprise support**: Policy management for organizational compliance
- **Flexible integration**: Support for plugins and custom marketplaces
- **Team collaboration**: Share settings via version control

## Best Practices

1. **Version control shared settings**: Commit `.claude/settings.json` to ensure team consistency
2. **Keep secrets local**: Use `.claude/settings.local.json` for API keys and sensitive data
3. **Use granular permissions**: Be specific with allow/deny rules
4. **Document custom settings**: Add comments or documentation for complex configurations
5. **Test changes**: Validate settings changes don't break existing workflows
