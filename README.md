# Claude Code Marketplace

Plugin marketplace for Claude Code featuring production-ready AI agents.

## Installation

```bash
# Add marketplace
/plugin marketplace add https://github.com/DustyWalker/claude-code-marketplace

# Install plugin
/plugin install production-agents-suite@claude-code-marketplace
```

## Production Agents Suite

15 specialized AI agents for software development:

### Code Quality (5 agents)
- **code-reviewer** - Code review and best practices validation
- **security-auditor** - OWASP security compliance auditing
- **test-suite-generator** - Unit, integration, and E2E test generation
- **performance-optimizer** - Performance analysis and optimization
- **architecture-checker** - SOLID principles and design pattern validation

### Development (4 agents)
- **backend-typescript-architect** - Node.js/TypeScript backend development
- **frontend-developer** - React/Next.js/Vue component development
- **api-designer** - RESTful/GraphQL API specification
- **database-architect** - Database schema design and migrations

### Testing (2 agents)
- **test-automator** - E2E test automation with Playwright/Cypress
- **qa-engineer** - QA testing and regression analysis

### DevOps (3 agents)
- **deployment-engineer** - Multi-cloud deployment automation
- **cicd-automation** - CI/CD pipeline configuration
- **docker-specialist** - Container optimization
- **refactoring-specialist** - Code refactoring and cleanup

### Slash Commands

- `/review` - Code review
- `/test` - Generate tests
- `/security-scan` - Security audit
- `/deploy` - Deploy to staging/production

## Usage

Agents activate automatically based on context:

```
"Review the authentication code" → security-auditor
"Create a user API" → backend-typescript-architect
"Optimize slow queries" → performance-optimizer
```

Or invoke explicitly:

```bash
/review src/auth/
/test src/services/user.service.ts
/security-scan
```

## Repository Structure

```
claude-code-marketplace/
├── .claude-plugin/
│   ├── marketplace.json       # Marketplace manifest
│   └── plugin.json            # Plugin manifest
├── agents/                    # 15 agent files
├── commands/                  # 4 slash commands
├── hooks/                     # Quality gate hooks
├── settings.json              # Security permissions
└── mcp.json                   # MCP server config
```

## Contributing

To add a plugin:

1. Create `.claude-plugin/plugin.json` manifest
2. Add agents/commands with `./` prefix paths
3. Add entry to `.claude-plugin/marketplace.json`
4. Test locally
5. Submit PR

## License

MIT License - See [LICENSE](LICENSE)

## Links

- [Issues](https://github.com/DustyWalker/claude-code-marketplace/issues)
- [Discussions](https://github.com/DustyWalker/claude-code-marketplace/discussions)
