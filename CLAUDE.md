# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is the **official Claude Code plugin marketplace repository**. It serves multiple purposes:

1. **Plugin Marketplace** - Distribution hub for Claude Code plugins
2. **Documentation Repository** - Comprehensive guides for agent development
3. **Research Archive** - Market intelligence and adoption patterns
4. **Reference Implementation** - Production-ready agents suite as an example

## Repository Structure

### Marketplace Components

#### `.claude-plugin/marketplace.json`
**The marketplace manifest** - defines all plugins available in this marketplace.

- Lists available plugins with metadata (name, version, description, source)
- Categorizes plugins by functionality (Code Quality, Security, Testing, Performance, DevOps)
- Used by Claude Code to discover and install plugins
- **CRITICAL**: This file must be valid JSON and follow the marketplace schema

Users add this marketplace with:
```bash
/plugin marketplace add claude-code-marketplace/claude-code-marketplace
```

#### `.claude-plugin/plugin.json`
**Plugin manifest for the Production Agents Suite** - the flagship plugin in this marketplace.

- Defines the 15-agent production suite as an installable plugin
- Lists all agents and slash commands included
- Metadata for marketplace discovery

### Production Agents Suite (`.claude/`)

The reference implementation plugin included in this marketplace:

- **`.claude/agents/`** - 15 specialized agents (15 markdown files)
  - Phase 1: code-reviewer, security-auditor, test-suite-generator, performance-optimizer, backend-typescript-architect
  - Phase 2: test-automator, qa-engineer, architecture-checker
  - Phase 3: frontend-developer, api-designer, database-architect, refactoring-specialist
  - Phase 4: deployment-engineer, cicd-automation, docker-specialist

- **`.claude/commands/`** - 4 slash commands
  - review.md, test.md, security-scan.md, deploy.md

- **`.claude/hooks/hooks.json`** - Quality gate hooks
  - PreToolUse, PostToolUse, PreCommit validation

- **`.claude/settings.json`** - Security and permissions configuration

### Documentation (`/docs/`)

Official Claude Code reference documentation (7 files):

- `settings.md` - Configuration, permissions, environment variables, hooks
- `slash-commands.md` - Built-in and custom command creation
- `mcp.md` - MCP server integration with practical examples
- `sub-agents.md` - Creating and using specialized AI assistants
- `plugins.md` - Plugin structure, installation, development
- `plugin-marketplaces.md` - Creating and managing marketplaces
- `plugins-reference.md` - Technical schemas and specifications

**Source**: Extracted from official Claude Code docs at docs.claude.com

### Market Research (`/top-agents-research/`)

Data-driven analysis of the Claude Code ecosystem:

- `comprehensive-agents-research.md` - **PRIMARY RESEARCH DOCUMENT**
  - Analysis of 115K+ developers, 1.09M VS Code installs
  - 11 agent categories ranked by usage
  - Deployment strategies and ROI metrics
  - Success factors and adoption patterns

**Use when**: Planning agent priorities, calculating ROI, understanding market trends

### System Prompt Engineering (`/writing-agent-system-prompt/`)

Technical guide for creating effective agents:

- `comprehensive-system-prompt-guide.md` - **DEFINITIVE IMPLEMENTATION GUIDE**
  - 16 sections covering architecture, structure, tools, safety
  - 4 production-ready agent templates
  - Testing and iteration methodologies
  - Advanced orchestration patterns

**Use when**: Writing agent prompts, debugging agents, implementing best practices

### Other Files

- `DEPLOYMENT-GUIDE.md` - Phased deployment strategy for the production agents suite
- `README.md` - Public-facing marketplace documentation
- `.gitignore` - Git ignore rules
- `LICENSE` - MIT license

## Working with This Repository

### This is a Documentation and Plugin Repository

**No build process, tests, or runtime**:
- All documentation is static Markdown
- No package.json, dependencies, or build commands
- No test suite (validation is manual)
- Changes are direct file edits

### Common Tasks

#### Adding a Plugin to the Marketplace

1. **Ensure plugin quality**:
   - Has `.claude-plugin/plugin.json` manifest
   - Follows [plugin structure](docs/plugins.md)
   - Meets quality standards from [system prompt guide](writing-agent-system-prompt/comprehensive-system-prompt-guide.md)

2. **Add to marketplace.json**:
   ```json
   {
     "name": "plugin-name",
     "version": "1.0.0",
     "description": "What the plugin does",
     "source": "github",
     "owner": "username",
     "repo": "repo-name",
     "agents": ["agent1", "agent2"],
     "commands": ["command1"]
   }
   ```

3. **Update categories** if needed

4. **Test the addition**:
   ```bash
   /plugin marketplace add ./
   /plugin install plugin-name@local
   ```

#### Updating Documentation

1. **Identify documentation layer**:
   - Official reference → Update `docs/` files
   - Market research → Update `top-agents-research/`
   - Implementation guides → Update `writing-agent-system-prompt/`

2. **Maintain consistency**:
   - Keep examples realistic and production-ready
   - Cross-reference between layers appropriately
   - Preserve existing formatting patterns

3. **Update references**:
   - If adding new docs, update this CLAUDE.md
   - Update README.md if user-facing changes
   - Cite sources for research data

#### Modifying the Production Agents Suite

The `.claude/` directory is the reference implementation. Changes here:

1. **Update agent files** in `.claude/agents/`
   - Follow 9-section structure from system prompt guide
   - Update version in `.claude-plugin/plugin.json`

2. **Update slash commands** in `.claude/commands/`
   - Maintain YAML frontmatter format
   - Update allowed-tools if needed

3. **Test changes locally**:
   ```bash
   cd /path/to/test-project
   cp -r /path/to/this/repo/.claude ./
   # Test agents and commands
   ```

4. **Update documentation**:
   - Update DEPLOYMENT-GUIDE.md if workflow changes
   - Update README.md if user-facing changes
   - Bump version in both plugin.json files

### Quality Standards

**For Official Documentation** (`docs/`):
- Schema-first, comprehensive coverage
- Use official terminology
- Include complete examples
- Validate against official Claude Code docs

**For Market Research** (`top-agents-research/`):
- Data-driven with sources cited
- Include statistics and metrics
- Provide actionable insights
- Update quarterly or when significant changes occur

**For Implementation Guides** (`writing-agent-system-prompt/`):
- Template-based with tested patterns
- Include anti-patterns and pitfalls
- Provide production-ready examples
- Keep code examples realistic (not toy examples)

**For Marketplace Plugins**:
- Complete documentation (README, usage examples)
- Semantic versioning (major.minor.patch)
- Security review (no hardcoded secrets)
- Tested in real projects

## Architecture Principles

### Three-Layer Information Architecture

```
Layer 1: Official Reference (docs/)
   ↓ provides foundation for
Layer 2: Market Intelligence (top-agents-research/)
   ↓ informs decisions about
Layer 3: Implementation Guide (writing-agent-system-prompt/)
```

**Layer 1** (docs/): "What is possible" - Complete feature documentation
**Layer 2** (research/): "What works" - Real-world patterns and proven strategies
**Layer 3** (guides/): "How to build" - Technical execution and best practices

### Marketplace as Living Catalog

The marketplace manifest (`.claude-plugin/marketplace.json`) is the **single source of truth** for plugin availability. All plugins must:

1. Be listed in marketplace.json with complete metadata
2. Have accessible source (GitHub, Git, npm, or local)
3. Include valid plugin.json manifest
4. Meet quality and security standards

## Typical Workflows

### User Installing Plugins

1. User adds marketplace: `/plugin marketplace add claude-code-marketplace/claude-code-marketplace`
2. Claude Code reads `.claude-plugin/marketplace.json`
3. User browses: `/plugin` (interactive) or `/plugin install production-agents-suite@claude-code-marketplace`
4. Claude Code fetches from source (GitHub) and installs to user's project

### Contributor Adding Plugin

1. Fork this repository
2. Create plugin following [docs/plugins.md](docs/plugins.md)
3. Add entry to `.claude-plugin/marketplace.json`
4. Test: `/plugin marketplace add ./` then `/plugin install plugin-name@local`
5. Submit pull request with documentation
6. After merge, users can install: `/plugin install plugin-name@claude-code-marketplace`

### Maintainer Updating Suite

1. Modify agents in `.claude/agents/`
2. Update commands in `.claude/commands/` if needed
3. Bump version in `.claude-plugin/plugin.json`
4. Update version in `.claude-plugin/marketplace.json`
5. Update DEPLOYMENT-GUIDE.md and README.md
6. Test in real project
7. Create git tag: `git tag v1.1.0`
8. Push with tags: `git push origin main --tags`

### Researcher Updating Market Data

1. Read new research sources (GitHub repos, marketplace data, surveys)
2. Update `top-agents-research/comprehensive-agents-research.md`
3. Cite sources and include compilation date
4. Cross-reference with implementation guide if patterns change
5. Update this CLAUDE.md if significant findings impact architecture

## Key Files Reference

| File | Purpose | Update Frequency | Critical? |
|------|---------|------------------|-----------|
| `.claude-plugin/marketplace.json` | Plugin catalog | When adding/updating plugins | YES |
| `.claude-plugin/plugin.json` | Suite manifest | When suite version changes | YES |
| `README.md` | Public documentation | When user-facing changes occur | YES |
| `DEPLOYMENT-GUIDE.md` | Suite installation guide | When deployment strategy changes | YES |
| `.claude/agents/*.md` | Agent implementations | When improving agents | YES |
| `docs/*.md` | Official reference | When Claude Code features change | Medium |
| `top-agents-research/*.md` | Market intelligence | Quarterly or major ecosystem changes | Medium |
| `writing-agent-system-prompt/*.md` | Implementation guide | When best practices evolve | Medium |
| `CLAUDE.md` | This file | When repository structure changes | YES |

## Git Workflow

### Branching Strategy

- `main` - Stable, production-ready plugins and documentation
- Feature branches for new plugins or major updates
- Tag releases with semantic versions: `v1.0.0`, `v1.1.0`, etc.

### Commit Conventions

- `feat: Add [plugin-name] to marketplace` - New plugin
- `fix: Correct [agent-name] tool permissions` - Bug fix
- `docs: Update [doc-name] with [feature]` - Documentation
- `refactor: Improve [agent-name] prompt structure` - Code improvement
- `chore: Bump production-agents-suite to v1.1.0` - Maintenance

### Release Process

1. Update version in relevant plugin.json files
2. Update CHANGELOG.md (create if needed)
3. Update README.md with new features
4. Commit: `chore: Release v1.x.0`
5. Tag: `git tag v1.x.0`
6. Push: `git push origin main --tags`

## For AI Assistants

### Context for Future Instances

**Repository nature**: Claude Code plugin marketplace with documentation and reference implementation

**Primary tasks**:
- Adding/updating plugins in marketplace.json
- Improving agents in the production suite
- Updating documentation for accuracy
- Maintaining quality standards
- Assisting contributors with plugin development

**Key principles**:
- marketplace.json is the source of truth for plugins
- All changes must maintain JSON validity
- Plugins must meet quality standards before listing
- Documentation must be accurate and cite sources
- Examples must be production-ready, not toy code

### When Asked To...

**"Add a plugin to the marketplace"**:
1. Verify plugin has valid .claude-plugin/plugin.json
2. Add entry to .claude-plugin/marketplace.json with all required fields
3. Update categories if needed
4. Validate JSON syntax
5. Update README.md if notable plugin

**"Improve an agent"**:
1. Read current agent from .claude/agents/
2. Follow structure from writing-agent-system-prompt/comprehensive-system-prompt-guide.md
3. Update with improvements
4. Bump version in .claude-plugin/plugin.json
5. Test prompt structure and tool access

**"Update documentation"**:
1. Identify correct layer (reference/research/implementation)
2. Make changes preserving format and structure
3. Cross-reference related docs if needed
4. Update this CLAUDE.md if major structural changes

**"Create a new marketplace"**:
1. Explain marketplace structure from docs/plugin-marketplaces.md
2. Show how to create .claude-plugin/marketplace.json
3. Provide examples from this repository
4. Explain hosting and distribution

## Security & Permissions

### Repository Security

- No secrets or credentials should ever be committed
- Agent prompts should not include hardcoded API keys
- Environment variables used for sensitive configuration
- Hooks validate but don't execute arbitrary code

### Plugin Security Review

Before adding plugins to marketplace:
- Review agent prompts for security issues
- Check tool permissions (least privilege principle)
- Verify no hardcoded secrets
- Ensure hooks have appropriate timeouts
- Validate external MCP server configurations

## Support & Troubleshooting

### Common Issues

**Marketplace not loading**:
- Verify `.claude-plugin/marketplace.json` is valid JSON
- Check GitHub repository is public and accessible
- Ensure file is in correct location

**Plugin installation fails**:
- Verify source repository exists and is accessible
- Check plugin.json manifest is valid
- Ensure all agent/command files exist
- Review tool permissions

**Agent not working**:
- Check agent file has valid YAML frontmatter
- Verify tool list includes required tools
- Review agent description for auto-activation clarity
- Test prompt structure against system prompt guide

### Getting Help

- Read official docs in `/docs` folder
- Review implementation guide for agent development
- Check market research for adoption patterns
- Submit GitHub issues for bugs or feature requests
- Join discussions for community support

---

This CLAUDE.md provides complete context for working with the Claude Code plugin marketplace repository. All updates should maintain the three-layer architecture and ensure the marketplace.json remains the authoritative plugin catalog.
