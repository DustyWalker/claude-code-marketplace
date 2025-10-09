# Claude Code Sub-Agents

## Overview

Sub-agents (or subagents) are specialized AI assistants within Claude Code designed for specific tasks. Each subagent operates in its own context window with dedicated expertise, custom system prompts, and configurable tool access.

## What are Sub-Agents?

Sub-agents are specialized instances of Claude that:
- **Operate independently**: Each has a separate context window
- **Have focused expertise**: Optimized for specific domains or tasks
- **Can be customized**: Configure with custom prompts and tool access
- **Are reusable**: Can be used across different projects and workflows

## Key Benefits

### 1. Context Preservation
Each subagent maintains its own isolated context, preventing:
- Context window overflow
- Cross-contamination of unrelated tasks
- Loss of important information

### 2. Specialized Expertise
Fine-tuned system prompts enable:
- Domain-specific knowledge
- Task-specific workflows
- Consistent behavior patterns

### 3. Reusability
Once created, subagents can be:
- Shared across projects
- Used by entire teams
- Version controlled
- Updated centrally

### 4. Flexible Permissions
Configure tool access per-agent:
- Restrict dangerous operations
- Grant access to specific tools only
- Enforce security policies

## Creating Sub-Agents

### Method 1: Interactive Interface (Recommended)

```bash
/agents
```

This opens an interactive interface where you can:
- Create new agents
- Edit existing agents
- Configure agent settings
- Test agent behavior

### Method 2: Markdown Files

Create markdown files in:
- **Project-level**: `.claude/agents/` (shared with team)
- **User-level**: `~/.claude/agents/` (personal)

### Method 3: CLI Configuration

Use the `--agents` flag when starting Claude Code:
```bash
claude --agents code-reviewer,debugger
```

## Sub-Agent Configuration

A typical subagent configuration includes:

```markdown
---
name: agent-name
description: When to invoke this agent and what it does
tools: [optional list of allowed tools]
model: [optional model override]
---

System prompt goes here.

This is the specialized context and instructions for the agent.
```

### Configuration Fields

- **name**: Unique identifier for the agent
- **description**: Explains the agent's purpose and when to invoke it
- **tools**: Optional array of tool names the agent can use
- **model**: Optional model override (defaults to main model)

## Example Sub-Agents

### Code Reviewer

File: `.claude/agents/code-reviewer.md`
```markdown
---
name: code-reviewer
description: Reviews code for quality, security, and best practices. Invoke after significant code changes.
tools: [Read, Grep, Bash]
---

You are a senior code reviewer with expertise in:
- Code quality and maintainability
- Security vulnerabilities
- Performance optimization
- Best practices across multiple languages

When reviewing code:
1. Read and understand the changes
2. Check for common security issues
3. Verify error handling
4. Assess code complexity
5. Suggest specific improvements

Be constructive and provide actionable feedback.
```

### Debugger

File: `.claude/agents/debugger.md`
```markdown
---
name: debugger
description: Analyzes errors and implements fixes. Invoke when encountering bugs or test failures.
tools: [Read, Write, Edit, Bash, Grep]
---

You are an expert debugger specializing in:
- Root cause analysis
- Error trace interpretation
- Systematic debugging approaches
- Fix implementation

Debugging workflow:
1. Understand the error message completely
2. Locate the problematic code
3. Identify the root cause
4. Propose and implement a fix
5. Verify the fix resolves the issue

Always explain your reasoning.
```

### Data Analyst

File: `.claude/agents/data-analyst.md`
```markdown
---
name: data-analyst
description: Performs SQL queries and data analysis. Invoke for database operations and data insights.
tools: [mcp__postgres, Bash]
---

You are a data analyst expert in:
- SQL query optimization
- Data analysis and visualization
- Statistical analysis
- Database schema understanding

When analyzing data:
1. Understand the question or requirement
2. Write efficient SQL queries
3. Interpret results correctly
4. Provide clear insights
5. Suggest data-driven recommendations

Present findings in a clear, actionable format.
```

### Documentation Writer

File: `.claude/agents/doc-writer.md`
```markdown
---
name: doc-writer
description: Creates and updates documentation. Invoke when documentation needs to be written or improved.
tools: [Read, Write, Edit, Glob]
---

You are a technical writer specializing in:
- Clear, concise documentation
- User-focused content
- API documentation
- Code examples
- README files

Documentation principles:
1. Start with the "why" before the "how"
2. Use clear examples
3. Keep language simple
4. Include troubleshooting sections
5. Maintain consistent style

Focus on helping users understand and use the code effectively.
```

### Test Engineer

File: `.claude/agents/test-engineer.md`
```markdown
---
name: test-engineer
description: Writes and runs tests. Invoke when test coverage is needed or tests are failing.
tools: [Read, Write, Edit, Bash]
---

You are a test engineer expert in:
- Unit testing
- Integration testing
- Test-driven development
- Test coverage analysis

Testing approach:
1. Understand the code being tested
2. Identify edge cases
3. Write comprehensive test suites
4. Follow testing best practices
5. Ensure tests are maintainable

Write tests that are clear, focused, and valuable.
```

### Security Auditor

File: `.claude/agents/security-auditor.md`
```markdown
---
name: security-auditor
description: Audits code for security vulnerabilities. Invoke before releases or when security is a concern.
tools: [Read, Grep, Bash]
---

You are a security expert specializing in:
- OWASP Top 10 vulnerabilities
- Secure coding practices
- Authentication and authorization
- Input validation
- Cryptography

Security audit checklist:
1. Authentication and session management
2. Input validation and sanitization
3. SQL injection prevention
4. XSS protection
5. CSRF protection
6. Sensitive data exposure
7. Access control
8. Security misconfigurations

Provide severity ratings and remediation guidance.
```

## Using Sub-Agents

### Automatic Delegation

Claude will automatically delegate tasks to appropriate subagents based on their descriptions:

```
User: Can you review the authentication code for security issues?
Claude: I'll delegate this to the security-auditor agent.
[security-auditor performs the audit]
Claude: Here's what the security auditor found...
```

### Explicit Invocation

Directly request a specific subagent:

```
Please have the code-reviewer agent review src/utils/helpers.js
```

or

```
Use the debugger agent to fix the failing tests in test/integration/
```

## Best Practices

### 1. Create Focused Agents
- **Do**: Create agents for specific, well-defined tasks
- **Don't**: Create overly general agents that try to do everything

### 2. Write Detailed System Prompts
- Include clear objectives
- Specify workflows and methodologies
- Provide examples when helpful
- Define scope and boundaries

### 3. Limit Tool Access
- Only grant necessary tools
- Use most restrictive permissions that work
- Consider security implications
- Test with minimal permissions first

### 4. Version Control Project Agents
- Check `.claude/agents/` into git
- Document agent purposes in README
- Review agent changes in PRs
- Keep agents updated with project evolution

### 5. Test Agent Behavior
- Verify agents work as expected
- Test with edge cases
- Ensure agents follow instructions
- Validate tool restrictions work

### 6. Name Clearly
- Use descriptive, intuitive names
- Follow consistent naming conventions
- Avoid abbreviations unless obvious
- Consider searchability

### 7. Document When to Use
- Write clear descriptions
- Include examples of appropriate use cases
- Specify when NOT to use the agent
- Update as agent evolves

## Advanced Patterns

### Chained Agents

Use multiple agents in sequence:

```markdown
---
name: deploy-coordinator
description: Orchestrates deployment workflow using multiple agents
---

Coordinate deployment by:
1. Use test-engineer to run full test suite
2. Use code-reviewer to verify code quality
3. Use security-auditor to check for vulnerabilities
4. If all pass, execute deployment
5. Use monitoring agent to verify deployment health
```

### Conditional Agent Selection

Agents can decide which other agents to involve:

```markdown
---
name: triage-agent
description: Analyzes issues and delegates to appropriate specialist
---

When a bug report comes in:
1. Analyze the error message and context
2. Determine the type of issue (security, performance, logic, etc.)
3. Delegate to the appropriate specialist agent
4. Compile findings and coordinate response
```

## Invocation Methods

### 1. Automatic Delegation
Claude proactively assigns tasks based on agent descriptions:
- Most seamless experience
- Requires clear agent descriptions
- Claude decides which agent is most appropriate

### 2. Explicit Request
User directly requests a specific agent:
- Full control over which agent runs
- Useful when you know which specialist you need
- Bypasses Claude's decision-making

### 3. Programmatic
Slash commands or hooks trigger agents:
- Enables workflow automation
- Can be event-driven
- Integrates with CI/CD pipelines

## Troubleshooting

### Agent Not Being Invoked

Check:
- Description clearly indicates when to use the agent
- Agent file is in correct location
- Agent name doesn't conflict with built-ins
- Restart Claude Code if recently added

### Tool Access Issues

Verify:
- Tools listed in agent config are correct names
- Project permissions allow those tools
- No conflicts with settings.json restrictions

### Agent Behavior Incorrect

Review:
- System prompt is clear and specific
- No conflicting instructions
- Agent has access to needed tools
- Model selection is appropriate

## Integration with Other Features

### With Slash Commands

Create commands that invoke specific agents:
```markdown
---
description: Run security audit
---
Have the security-auditor agent review all authentication and authorization code.
```

### With Hooks

Trigger agents automatically:
```json
{
  "hooks": {
    "pre-commit": "invoke code-reviewer agent"
  }
}
```

### With MCP Servers

Agents can use MCP tools:
```markdown
---
name: github-helper
tools: [mcp__github]
---
You help manage GitHub issues and pull requests using the GitHub MCP server.
```

## Sharing Sub-Agents

### Within a Team
1. Create agents in `.claude/agents/`
2. Commit to version control
3. Document in project README
4. Team members automatically get updates

### Across Projects
1. Create agents in `~/.claude/agents/`
2. Export to a shared repository
3. Others copy to their `~/.claude/agents/`
4. Consider creating a plugin

### Via Plugins
Package agents as plugins for easy distribution:
1. Create plugin with agents/ directory
2. Publish to marketplace
3. Others install via `/plugin install`

## Next Steps

- Create your first specialized sub-agent for a common task
- Experiment with different tool configurations
- Share useful agents with your team
- Explore combining agents with hooks and commands for automation
- Build a library of domain-specific agents for your organization
