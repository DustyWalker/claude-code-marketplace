# Claude Code Slash Commands

## Overview

Slash commands provide quick access to Claude Code features and enable workflow automation. They come in two varieties: built-in commands and custom commands.

## Built-in Slash Commands

### Project Management
- `/add-dir` - Add additional working directories to the current session
- `/init` - Initialize project with CLAUDE.md guide file
- `/memory` - Edit CLAUDE.md memory files for persistent context

### Session Management
- `/clear` - Clear conversation history
- `/compact` - Compact conversation with optional focus on specific topics
- `/rewind` - Rewind conversation and/or code to a previous state

### Configuration
- `/config` - Open Settings interface
- `/model` - Select or change AI model
- `/permissions` - View and update permissions
- `/status` - Show version, model, and account details

### Code & Development
- `/review` - Request code review from Claude
- `/pr_comments` - View pull request comments
- `/vim` - Enter vim mode for editing

### Integrations
- `/agents` - Manage custom AI subagents
- `/mcp` - Manage MCP (Model Context Protocol) server connections

### System & Account
- `/login` - Switch Anthropic accounts
- `/logout` - Sign out of Anthropic account
- `/help` - Get usage help
- `/bug` - Report bugs to Anthropic
- `/doctor` - Check Claude Code installation health
- `/terminal-setup` - Install terminal key binding

### Usage & Monitoring
- `/cost` - Show token usage statistics
- `/usage` - Show plan usage limits

## Custom Slash Commands

### Storage Locations

Custom commands are stored as markdown files:

**Project-level** (shared with team):
```
.claude/commands/
```

**Personal** (user-specific):
```
~/.claude/commands/
```

### Command Structure

A custom command file consists of optional frontmatter and a prompt template:

```markdown
---
description: Brief description of what this command does
allowed-tools: Bash(git add:*), Bash(git status:*)
---
Command prompt goes here with $ARGUMENTS or $1, $2, etc.
```

### Frontmatter Options

- `description` - Brief explanation of the command (shown in autocomplete)
- `allowed-tools` - Restrict which tools Claude can use
- `model` - Override the default model for this command
- `permissions` - Custom permission rules

### Argument Handling

Commands support multiple argument patterns:

**All arguments as one string**:
```markdown
---
description: Create a git commit
---
Create a git commit with message: $ARGUMENTS
```

**Individual positional arguments**:
```markdown
---
description: Rename a file
---
Rename file $1 to $2
```

**Numbered arguments**:
```markdown
---
description: Search and replace
---
Search for "$1" and replace with "$2" in file $3
```

### Example Custom Commands

#### Git Commit Command
File: `.claude/commands/commit.md`
```markdown
---
description: Create a git commit
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git commit:*)
---
Create a git commit with the following message: $ARGUMENTS

Before committing:
1. Show me what files will be committed
2. Create the commit
3. Show the commit log
```

#### Code Review Command
File: `.claude/commands/code-review.md`
```markdown
---
description: Review code for quality and security
allowed-tools: Read(*), Grep(*)
---
Please review the code in $1 for:
- Code quality issues
- Security vulnerabilities
- Best practice violations
- Performance concerns

Provide specific recommendations for improvements.
```

#### Run Tests Command
File: `.claude/commands/test.md`
```markdown
---
description: Run tests for a specific file
allowed-tools: Bash(npm test:*), Bash(pytest:*)
---
Run all tests related to $ARGUMENTS and report the results.
If any tests fail, analyze the failures and suggest fixes.
```

#### Bash Execution Command
File: `.claude/commands/exec.md`
```markdown
---
description: Execute a bash command
allowed-tools: Bash(*)
---
Execute the following bash command: $ARGUMENTS

Show me the output and explain what happened.
```

#### Documentation Generator
File: `.claude/commands/document.md`
```markdown
---
description: Generate documentation for a file
allowed-tools: Read(*), Write(*)
---
Read the file $1 and generate comprehensive documentation including:
- Overview of purpose
- Function/class descriptions
- Parameter documentation
- Usage examples

Save the documentation to $1.md
```

## Command Usage

### Invoking Commands

Type a forward slash followed by the command name:
```
/commit Fixed bug in authentication module
/code-review src/auth.js
/test user-service
```

### Autocomplete

As you type `/`, Claude Code will show available commands with their descriptions. Use arrow keys to navigate and Enter to select.

### Listing Commands

To see all available commands:
```
/help
```

## Best Practices

1. **Clear descriptions**: Write concise, descriptive command summaries
2. **Restrict tools**: Use `allowed-tools` to limit scope and improve security
3. **Document arguments**: Include examples in command descriptions
4. **Version control**: Check project commands into git for team sharing
5. **Test thoroughly**: Verify commands work as expected before sharing
6. **Use meaningful names**: Choose intuitive command names that reflect their purpose
7. **Keep it simple**: Focus each command on a single, well-defined task
8. **Include context**: Provide enough detail in the prompt for Claude to understand the task

## Advanced Features

### Conditional Logic

You can include conditional instructions in commands:
```markdown
---
description: Smart commit with optional push
---
Create a git commit with message: $ARGUMENTS

After committing, ask me if I want to push to remote.
```

### Multi-step Workflows

Commands can orchestrate complex workflows:
```markdown
---
description: Full deployment workflow
---
Execute the following deployment workflow:
1. Run all tests
2. If tests pass, build the project
3. If build succeeds, ask for confirmation to deploy
4. Deploy to staging
5. Show deployment status
```

### File References

Commands can reference files in the project:
```markdown
---
description: Update dependencies
---
Read package.json, update all dependencies to latest versions,
and save the changes. Then run npm install.
```

## Troubleshooting

**Command not found**:
- Check the file is in `.claude/commands/` or `~/.claude/commands/`
- Verify the filename matches the command (e.g., `/foo` needs `foo.md`)
- Restart Claude Code if you just added the command

**Arguments not working**:
- Ensure you're using `$ARGUMENTS`, `$1`, `$2`, etc. correctly
- Check that arguments are being passed when invoking the command

**Permission denied**:
- Review the `allowed-tools` frontmatter
- Check project permissions in settings
- Verify the tools you're using are allowed

## Next Steps

- Create your first custom command for a common workflow
- Share useful commands with your team via `.claude/commands/`
- Explore the community for shared command examples
- Combine commands with hooks for advanced automation
