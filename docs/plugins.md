# Claude Code Plugins

## Overview

Plugins extend Claude Code with custom functionality including commands, agents, hooks, and MCP servers. They provide a powerful way to package and share reusable components across projects and teams.

## What are Plugins?

Plugins are bundles of Claude Code extensions that:
- Add custom slash commands
- Provide specialized sub-agents
- Implement event hooks
- Include MCP server integrations
- Can be shared across projects and teams
- Are installable from marketplaces

## Plugin Components

A plugin can include any combination of:

### 1. Commands
Custom slash commands that extend Claude Code's CLI functionality.

### 2. Agents
Specialized sub-agents for specific tasks or domains.

### 3. Hooks
Event handlers that respond to Claude Code actions.

### 4. MCP Servers
Model Context Protocol servers for external tool integration.

## Plugin Structure

### Basic Directory Layout

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest (required)
├── commands/                # Custom slash commands
│   ├── deploy.md
│   └── test.md
├── agents/                  # Sub-agent definitions
│   ├── reviewer.md
│   └── debugger.md
├── hooks/                   # Event hooks
│   └── hooks.json
└── .mcp.json               # MCP server configuration
```

### Plugin Manifest

Every plugin requires a manifest file: `.claude-plugin/plugin.json`

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "Brief description of what this plugin does",
  "author": {
    "name": "Your Name",
    "email": "your.email@example.com"
  },
  "homepage": "https://github.com/username/my-plugin",
  "repository": "https://github.com/username/my-plugin",
  "license": "MIT",
  "keywords": ["productivity", "testing", "deployment"]
}
```

#### Manifest Fields

**Required**:
- `name` - Unique plugin identifier
- `version` - Semantic version (e.g., "1.0.0")
- `description` - Brief explanation of the plugin

**Optional**:
- `author` - Contact information
- `homepage` - Documentation URL
- `repository` - Source code link
- `license` - Software license
- `keywords` - Discovery and search tags

## Installing Plugins

### From a Marketplace

1. **Add a marketplace** (if not already added):
```bash
/plugin marketplace add your-org/claude-plugins
```

2. **Browse available plugins**:
```bash
/plugin
```

3. **Install a specific plugin**:
```bash
/plugin install plugin-name@marketplace-name
```

Example:
```bash
/plugin install code-reviewer@company-marketplace
```

### From a Local Path

For development and testing:
```bash
/plugin marketplace add ./local-plugins
/plugin install my-plugin@local
```

## Managing Plugins

### List Installed Plugins

```bash
/plugin list
```

### Enable a Plugin

```bash
/plugin enable plugin-name@marketplace
```

### Disable a Plugin

```bash
/plugin disable plugin-name@marketplace
```

### Uninstall a Plugin

```bash
/plugin uninstall plugin-name@marketplace
```

### Update a Plugin

```bash
/plugin update plugin-name@marketplace
```

### View Plugin Info

```bash
/plugin info plugin-name@marketplace
```

## Creating Plugins

### Step 1: Initialize Plugin Structure

Create the basic directory structure:

```bash
mkdir my-plugin
cd my-plugin
mkdir -p .claude-plugin commands agents hooks
```

### Step 2: Create Plugin Manifest

`.claude-plugin/plugin.json`:
```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "My awesome Claude Code plugin",
  "author": {
    "name": "Your Name"
  },
  "keywords": ["development", "productivity"]
}
```

### Step 3: Add Components

#### Add a Command

`commands/greet.md`:
```markdown
---
description: Greet the user
---
Say hello to $ARGUMENTS in a friendly way!
```

#### Add an Agent

`agents/helper.md`:
```markdown
---
name: helper
description: A helpful assistant for common tasks
---
You are a helpful assistant focused on productivity and efficiency.
Provide clear, actionable guidance for common development tasks.
```

#### Add Hooks

`hooks/hooks.json`:
```json
{
  "hooks": [
    {
      "event": "PreToolUse",
      "command": "./scripts/pre-tool.sh",
      "description": "Validate before tool execution"
    }
  ]
}
```

#### Add MCP Server

`.mcp.json`:
```json
{
  "mcpServers": {
    "custom-tool": {
      "transport": "stdio",
      "command": "npx",
      "args": ["-y", "custom-mcp-server"]
    }
  }
}
```

### Step 4: Test Locally

```bash
cd ..
claude /plugin marketplace add ./my-plugin
claude /plugin install my-plugin@local
```

### Step 5: Version and Publish

1. Update version in `plugin.json`
2. Create git tag
3. Push to repository
4. Add to marketplace

## Example Plugins

### Development Workflow Plugin

```
dev-workflow/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   ├── commit.md          # Smart git commit
│   ├── review.md          # Request code review
│   └── deploy.md          # Deploy to staging/prod
├── agents/
│   ├── reviewer.md        # Code review specialist
│   └── tester.md          # Test engineer
└── hooks/
    └── hooks.json         # Pre-commit validation
```

### Data Analysis Plugin

```
data-tools/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   ├── query.md           # Run SQL queries
│   └── visualize.md       # Create data visualizations
├── agents/
│   └── analyst.md         # Data analysis expert
└── .mcp.json             # PostgreSQL MCP server
```

### Security Audit Plugin

```
security-audit/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   ├── scan.md            # Security scan
│   └── report.md          # Generate security report
├── agents/
│   ├── auditor.md         # Security auditor
│   └── penetration.md     # Penetration tester
└── hooks/
    └── hooks.json         # Pre-commit security checks
```

## Best Practices

### 1. Single Responsibility
- Focus each plugin on one cohesive set of functionality
- Don't create monolithic plugins that do everything
- Users can install multiple focused plugins

### 2. Clear Documentation
- Include README.md with setup instructions
- Document all commands and their arguments
- Explain what each agent specializes in
- Provide usage examples

### 3. Semantic Versioning
- Follow semver (major.minor.patch)
- Major: Breaking changes
- Minor: New features (backward compatible)
- Patch: Bug fixes

### 4. Test Thoroughly
- Test all commands with various arguments
- Verify agents behave correctly
- Ensure hooks don't break workflows
- Test MCP server connections

### 5. Dependencies
- Document any required dependencies
- Include installation instructions
- Consider using MCP stdio servers for external tools
- Minimize external dependencies when possible

### 6. Security
- Don't include hardcoded secrets
- Use environment variables for sensitive data
- Document required permissions
- Review code for security issues

### 7. Organization
- Keep related components together
- Use consistent naming conventions
- Organize by functionality
- Keep file structure clean

## Development Tips

### Local Development

Use local marketplaces for rapid iteration:
```bash
claude /plugin marketplace add ./my-plugins
claude /plugin install test-plugin@local
```

Make changes, then:
```bash
claude /plugin update test-plugin@local
```

### Debugging

Enable debug mode:
```bash
claude --debug
```

This shows:
- Plugin loading details
- Component registration
- Error messages
- Tool execution logs

### Testing Components Individually

Before packaging as a plugin, test components separately:
- Place commands in `.claude/commands/` to test
- Place agents in `.claude/agents/` to test
- Test hooks in project settings
- Test MCP servers via `.mcp.json`

### Version Control

Include in your plugin repository:
- All plugin files and directories
- README.md with documentation
- LICENSE file
- CHANGELOG.md for version history

Don't include:
- `.env` files
- Personal API keys
- User-specific configurations
- Build artifacts

## Sharing Plugins

### Via GitHub

1. Create a GitHub repository
2. Push your plugin code
3. Tag releases with version numbers
4. Add to a marketplace JSON file

### Via Private Marketplace

For internal/private plugins:
1. Host marketplace JSON on internal server
2. Add marketplace with `/plugin marketplace add`
3. Share marketplace URL with team

### Via Claude Plugin Registry

Submit to official plugin registry:
1. Meet quality standards
2. Include comprehensive documentation
3. Pass security review
4. Submit for approval

## Advanced Features

### Custom Path Configuration

Specify custom paths in `plugin.json`:

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "Plugin with custom paths",
  "paths": {
    "commands": "custom-commands/",
    "agents": "custom-agents/",
    "hooks": "custom-hooks/"
  }
}
```

### Conditional Components

Plugins can include conditional logic in components:

```markdown
---
description: Platform-specific command
---
$IF(platform=linux)
Run Linux-specific command
$ELSE
Run generic command
$ENDIF
```

### Plugin Dependencies

Specify required plugins in manifest:

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "dependencies": {
    "base-plugin": "^1.0.0"
  }
}
```

## Troubleshooting

### Plugin Not Loading

Check:
- `.claude-plugin/plugin.json` exists and is valid JSON
- Plugin name doesn't conflict with another plugin
- All required fields are present in manifest
- Plugin is enabled (`/plugin enable`)

### Components Not Working

Verify:
- Component files are in correct directories
- File formats are correct (Markdown for commands/agents)
- No syntax errors in frontmatter
- Permissions allow component execution

### MCP Server Issues

Ensure:
- `.mcp.json` is valid JSON
- MCP server command is correct
- Required dependencies are installed
- Environment variables are set

## Next Steps

- Create your first plugin for a common workflow
- Browse existing plugins for inspiration
- Contribute to community plugins
- Share your plugins with the community
- Set up a team marketplace for internal plugins
