# Available Tools Reference

## Claude Code Tools

### File Operations

| Tool | Description | Use Case |
|------|-------------|----------|
| `Read` | Read file contents | Viewing code, configs, docs |
| `Write` | Create/overwrite files | Creating new files |
| `Edit` | Modify existing files | Code changes, fixes |
| `Glob` | Find files by pattern | Locate files by name/path |
| `Grep` | Search file contents | Find code patterns |

### System Operations

| Tool | Description | Use Case |
|------|-------------|----------|
| `Bash` | Execute shell commands | Run tests, builds, git |
| `Task` | Launch subagents | Delegate to specialists |

### Information Tools

| Tool | Description | Use Case |
|------|-------------|----------|
| `WebFetch` | Fetch web content | Documentation, APIs |
| `WebSearch` | Search the web | Research, current info |
| `LSP` | Language server operations | Go to definition, references |

### Interaction Tools

| Tool | Description | Use Case |
|------|-------------|----------|
| `AskUserQuestion` | Ask user for input | Clarification, choices |
| `TodoWrite` | Manage task lists | Progress tracking |

### Notebook Tools

| Tool | Description | Use Case |
|------|-------------|----------|
| `NotebookEdit` | Edit Jupyter notebooks | Data science workflows |

### Claude Code Tool Sets

```yaml
# Read-only analysis
tools: Read, Grep, Glob

# Read-only with shell
tools: Read, Grep, Glob, Bash

# Code modification
tools: Read, Edit, Write, Grep, Glob, Bash

# Orchestrator (can delegate)
tools: Read, Grep, Glob, Bash, Task

# Full access (or omit entirely)
tools: Read, Edit, Write, Bash, Grep, Glob, WebFetch, WebSearch, Task
```

## OpenCode Tools

### Tool Categories

OpenCode uses boolean flags for tool categories:

| Tool | Description | Equivalent Claude Code |
|------|-------------|------------------------|
| `write` | Create/overwrite files | Write |
| `edit` | Modify existing files | Edit |
| `bash` | Execute shell commands | Bash |
| `webfetch` | Fetch web content | WebFetch |

### OpenCode Tool Configuration

```yaml
# Read-only (no modifications)
tools:
  write: false
  edit: false
  bash: false

# Can modify files but no shell
tools:
  write: true
  edit: true
  bash: false

# Full access
tools:
  write: true
  edit: true
  bash: true
  webfetch: true
```

### OpenCode Permissions

Fine-grained command control:

```yaml
permission:
  "git *": allow       # Allow all git commands
  "npm install": ask   # Prompt for npm install
  "rm -rf": deny       # Block dangerous commands
  "*": ask             # Ask for everything else
```

Permission values:
- `allow`: Unrestricted access
- `ask`: Approval required
- `deny`: Blocked

## Tool Selection by Purpose

### Read-Only Analysis

For agents that should never modify files:

**Claude Code:**
```yaml
tools: Read, Grep, Glob
```

**OpenCode:**
```yaml
tools:
  write: false
  edit: false
  bash: false
```

### Code Modification

For agents that need to change code:

**Claude Code:**
```yaml
tools: Read, Edit, Write, Grep, Glob, Bash
```

**OpenCode:**
```yaml
tools:
  write: true
  edit: true
  bash: true
```

### Restricted Shell Access

Allow specific commands only:

**Claude Code:** Use `permissionMode` or prompt instructions

**OpenCode:**
```yaml
permission:
  "git *": allow
  "npm test": allow
  "npm run *": allow
  "*": deny
```

## Security Guidelines

### Principle of Least Privilege

| Agent Type | Recommended Access |
|------------|-------------------|
| Code reviewer | Read-only |
| Test runner | Read + Bash |
| Bug fixer | Read + Edit + Bash |
| Documentation | Read + Write |
| Explorer | Read only |

### High-Risk Tools

Both platforms:
- Shell access can execute arbitrary commands
- Write/Edit can modify any file
- Consider restricting for security-focused agents

### MCP Tools (Claude Code Only)

Agents can access MCP tools from configured servers:

```yaml
# Inherit all MCP tools (omit tools field)
# Or specify explicitly:
tools: Read, Grep, mcp-server-name:tool-name
```
