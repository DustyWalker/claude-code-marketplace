# Claude Code Plugin Marketplaces

## Overview

Plugin marketplaces are centralized directories that list available Claude Code plugins. They enable plugin discovery, version management, and distribution across teams and communities. A marketplace is essentially a JSON file that catalogs plugins from various sources.

## What is a Plugin Marketplace?

A marketplace provides:
- **Centralized discovery**: Browse available plugins in one place
- **Version management**: Track and update plugin versions
- **Easy installation**: Install plugins with simple commands
- **Team distribution**: Share curated plugins with your organization
- **Multiple sources**: Support plugins from Git repos, GitHub, local paths, and package managers

## Marketplace Sources

Marketplaces can reference plugins from:

### 1. GitHub Repositories
```json
{
  "source": "github",
  "owner": "username",
  "repo": "plugin-repo"
}
```

### 2. Git Repositories
```json
{
  "source": "git",
  "url": "https://gitlab.com/company/plugin.git"
}
```

### 3. Local Paths
```json
{
  "source": "local",
  "path": "./local-plugins/my-plugin"
}
```

### 4. Package Managers
```json
{
  "source": "npm",
  "package": "@company/claude-plugin"
}
```

## Adding Marketplaces

### GitHub Marketplace

```bash
/plugin marketplace add owner/repo
```

Example:
```bash
/plugin marketplace add anthropic/claude-plugins
```

### Git Repository

```bash
/plugin marketplace add https://gitlab.com/company/plugins.git
```

### Local Marketplace (for development)

```bash
/plugin marketplace add ./my-marketplace
```

### With Custom Name

```bash
/plugin marketplace add owner/repo --name company-plugins
```

## Managing Marketplaces

### List All Marketplaces

```bash
/plugin marketplace list
```

### Update a Marketplace

```bash
/plugin marketplace update marketplace-name
```

### Remove a Marketplace

```bash
/plugin marketplace remove marketplace-name
```

### View Marketplace Details

```bash
/plugin marketplace info marketplace-name
```

## Creating a Marketplace

### Step 1: Create Marketplace Structure

```
my-marketplace/
├── .claude-plugin/
│   └── marketplace.json    # Marketplace manifest (required)
└── plugins/                # Optional: host plugins here
    ├── plugin-1/
    └── plugin-2/
```

### Step 2: Create Marketplace Manifest

`.claude-plugin/marketplace.json`:

```json
{
  "name": "company-plugins",
  "version": "1.0.0",
  "owner": {
    "name": "Company Name",
    "email": "plugins@company.com",
    "url": "https://company.com"
  },
  "description": "Curated plugins for company development workflows",
  "plugins": [
    {
      "name": "code-reviewer",
      "version": "1.2.0",
      "description": "Automated code review agent",
      "source": "github",
      "owner": "company",
      "repo": "code-reviewer-plugin"
    },
    {
      "name": "deploy-helper",
      "version": "2.0.1",
      "description": "Deployment automation tools",
      "source": "git",
      "url": "https://gitlab.company.com/tools/deploy-plugin.git"
    },
    {
      "name": "test-runner",
      "version": "1.5.0",
      "description": "Enhanced test execution and reporting",
      "source": "local",
      "path": "./plugins/test-runner"
    }
  ]
}
```

### Step 3: Host the Marketplace

#### GitHub (Recommended)

1. Create a GitHub repository
2. Add `.claude-plugin/marketplace.json`
3. Commit and push
4. Tag releases for versioning

```bash
git init
git add .claude-plugin/marketplace.json
git commit -m "Initial marketplace"
git tag v1.0.0
git push origin main --tags
```

#### GitLab or Other Git Hosting

Same process, just use your Git hosting platform.

#### Internal/Private Server

Host the repository on internal infrastructure and share the URL.

## Marketplace Manifest Schema

### Required Fields

```json
{
  "name": "marketplace-name",
  "owner": {
    "name": "Owner Name"
  },
  "plugins": []
}
```

### Complete Example

```json
{
  "name": "enterprise-plugins",
  "version": "1.0.0",
  "owner": {
    "name": "Acme Corporation",
    "email": "devtools@acme.com",
    "url": "https://acme.com/devtools"
  },
  "description": "Enterprise-approved Claude Code plugins",
  "homepage": "https://github.com/acme/claude-plugins",
  "repository": "https://github.com/acme/claude-plugins",
  "license": "MIT",
  "plugins": [
    {
      "name": "security-scanner",
      "version": "2.1.0",
      "description": "Automated security vulnerability scanning",
      "author": "Security Team",
      "keywords": ["security", "scanning"],
      "source": "github",
      "owner": "acme",
      "repo": "security-scanner-plugin",
      "branch": "main"
    },
    {
      "name": "compliance-checker",
      "version": "1.0.5",
      "description": "Ensure code meets compliance requirements",
      "source": "git",
      "url": "https://git.acme.com/tools/compliance-plugin.git",
      "ref": "v1.0.5"
    }
  ],
  "categories": [
    {
      "name": "Security",
      "plugins": ["security-scanner", "compliance-checker"]
    },
    {
      "name": "Development",
      "plugins": ["test-runner", "code-formatter"]
    }
  ]
}
```

## Plugin Entry Schema

Each plugin in the marketplace can have:

### Required Fields

```json
{
  "name": "plugin-name",
  "version": "1.0.0",
  "description": "What this plugin does",
  "source": "github",
  "owner": "username",
  "repo": "plugin-repo"
}
```

### Source-Specific Fields

**GitHub Source**:
```json
{
  "source": "github",
  "owner": "username",
  "repo": "repo-name",
  "branch": "main",          // optional
  "tag": "v1.0.0"            // optional
}
```

**Git Source**:
```json
{
  "source": "git",
  "url": "https://git.example.com/plugin.git",
  "ref": "main"              // optional: branch, tag, or commit
}
```

**Local Source**:
```json
{
  "source": "local",
  "path": "./plugins/plugin-name"
}
```

**NPM Source**:
```json
{
  "source": "npm",
  "package": "@scope/package-name",
  "version": "^1.0.0"        // semver range
}
```

### Optional Metadata

```json
{
  "author": "Plugin Author",
  "homepage": "https://example.com/plugin",
  "repository": "https://github.com/user/plugin",
  "license": "MIT",
  "keywords": ["tag1", "tag2"],
  "icon": "https://example.com/icon.png"
}
```

## Using Plugins from Marketplaces

### Install a Plugin

```bash
/plugin install plugin-name@marketplace-name
```

Example:
```bash
/plugin install code-reviewer@company-plugins
```

### Install Specific Version

```bash
/plugin install plugin-name@marketplace-name#version
```

Example:
```bash
/plugin install code-reviewer@company-plugins#1.2.0
```

### Interactive Browse and Install

```bash
/plugin
```

This opens an interactive interface showing:
- Available marketplaces
- Plugins in each marketplace
- Installation status
- Quick install/uninstall actions

## Best Practices

### For Marketplace Maintainers

#### 1. Clear Governance
- Define approval process for new plugins
- Establish quality standards
- Document submission guidelines
- Regular security reviews

#### 2. Versioning
- Use semantic versioning
- Tag marketplace releases
- Keep changelog updated
- Document breaking changes

#### 3. Organization
- Group plugins by category
- Use clear, consistent naming
- Provide comprehensive descriptions
- Include keywords for discoverability

#### 4. Quality Control
- Test all listed plugins
- Verify plugin sources
- Check for security issues
- Ensure documentation is complete

#### 5. Documentation
- Create clear README
- Document all plugins
- Provide usage examples
- Include troubleshooting guide

### For Plugin Authors

#### 1. Follow Standards
- Meet marketplace requirements
- Use semantic versioning
- Include complete manifest
- Provide thorough documentation

#### 2. Maintainability
- Respond to issues promptly
- Keep plugins updated
- Deprecate old versions properly
- Communicate changes clearly

#### 3. Testing
- Test across different environments
- Verify all components work
- Check for conflicts
- Validate metadata

## Example Marketplace Structures

### Community Marketplace

```json
{
  "name": "community-plugins",
  "owner": {
    "name": "Claude Code Community"
  },
  "description": "Community-contributed plugins for Claude Code",
  "plugins": [
    {
      "name": "git-flow",
      "version": "1.0.0",
      "description": "Git flow workflow automation",
      "source": "github",
      "owner": "community",
      "repo": "git-flow-plugin"
    },
    {
      "name": "docker-helper",
      "version": "2.1.0",
      "description": "Docker container management tools",
      "source": "github",
      "owner": "community",
      "repo": "docker-helper"
    }
  ],
  "categories": [
    {
      "name": "Version Control",
      "plugins": ["git-flow"]
    },
    {
      "name": "DevOps",
      "plugins": ["docker-helper"]
    }
  ]
}
```

### Enterprise Marketplace

```json
{
  "name": "enterprise-plugins",
  "owner": {
    "name": "Enterprise DevTools Team",
    "email": "devtools@enterprise.com"
  },
  "description": "Approved plugins for enterprise development",
  "plugins": [
    {
      "name": "security-baseline",
      "version": "3.0.0",
      "description": "Enterprise security requirements checker",
      "source": "git",
      "url": "https://git.enterprise.com/tools/security-baseline.git",
      "required": true
    },
    {
      "name": "compliance-audit",
      "version": "2.5.0",
      "description": "Automated compliance auditing",
      "source": "git",
      "url": "https://git.enterprise.com/tools/compliance.git",
      "required": true
    },
    {
      "name": "performance-profiler",
      "version": "1.2.0",
      "description": "Performance analysis tools",
      "source": "git",
      "url": "https://git.enterprise.com/tools/profiler.git"
    }
  ]
}
```

### Team Marketplace

```json
{
  "name": "team-plugins",
  "owner": {
    "name": "Development Team"
  },
  "description": "Team-specific workflow plugins",
  "plugins": [
    {
      "name": "jira-integration",
      "version": "1.5.0",
      "description": "Jira issue management",
      "source": "github",
      "owner": "team",
      "repo": "jira-plugin"
    },
    {
      "name": "custom-linter",
      "version": "2.0.0",
      "description": "Team-specific code style enforcement",
      "source": "local",
      "path": "./plugins/custom-linter"
    }
  ]
}
```

## Enterprise Configuration

### Centralized Marketplace Management

Administrators can:
- Pre-configure approved marketplaces
- Distribute via enterprise settings
- Control which marketplaces are accessible
- Enforce required plugins

Example enterprise settings:
```json
{
  "marketplaces": {
    "allowed": ["enterprise-plugins"],
    "blocked": ["public-community"],
    "required": ["enterprise-plugins"]
  },
  "plugins": {
    "required": [
      "security-baseline@enterprise-plugins",
      "compliance-audit@enterprise-plugins"
    ]
  }
}
```

### Access Control

Control marketplace access:
- Public marketplaces
- Private/internal marketplaces
- Authenticated marketplaces
- Read-only marketplaces

## Advanced Features

### Plugin Categories

Organize plugins by functionality:
```json
{
  "categories": [
    {
      "name": "Security",
      "description": "Security and compliance tools",
      "plugins": ["security-scanner", "vulnerability-checker"]
    },
    {
      "name": "Testing",
      "description": "Testing and quality assurance",
      "plugins": ["test-runner", "coverage-reporter"]
    }
  ]
}
```

### Required Plugins

Mark plugins as required:
```json
{
  "plugins": [
    {
      "name": "security-baseline",
      "required": true,
      ...
    }
  ]
}
```

### Deprecation Notices

Mark plugins as deprecated:
```json
{
  "plugins": [
    {
      "name": "old-plugin",
      "deprecated": true,
      "deprecationMessage": "Use new-plugin instead",
      "replacement": "new-plugin"
    }
  ]
}
```

## Troubleshooting

### Marketplace Not Loading

Check:
- URL/path is correct
- `.claude-plugin/marketplace.json` exists
- JSON is valid (no syntax errors)
- Network connectivity (for remote marketplaces)

### Plugins Not Appearing

Verify:
- Marketplace is added and updated
- Plugin entries have all required fields
- Source URLs/paths are accessible
- Marketplace JSON follows schema

### Installation Failures

Ensure:
- Plugin source is accessible
- Network connectivity for remote sources
- Local paths exist for local sources
- Required dependencies are available

## Next Steps

- Create a marketplace for your team or organization
- Add existing marketplaces to discover plugins
- Submit your plugins to community marketplaces
- Set up governance policies for enterprise marketplace
- Automate marketplace updates in CI/CD
