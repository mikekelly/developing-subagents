# OpenCode Agent Configuration

## Agent Types

OpenCode has two agent categories:

### Primary Agents
- Main assistants accessed via Tab key or custom keybinds
- Handle primary conversations
- Access all configured tools
- Built-in: **Build** (default), **Plan** (restricted)

### Subagents
- Specialized assistants invoked by primary agents or manually via `@mention`
- Built-in: **General** (multi-step research), **Explore** (fast codebase search)

## Configuration Methods

### Method 1: Markdown Files

Place `.md` files in agent directories. The filename (without extension) becomes the agent name.

**Locations:**

| Type | Location | Scope |
|------|----------|-------|
| Project | `.opencode/agent/` | Current project |
| User | `~/.config/opencode/agent/` | All projects |

**Format:**
```markdown
---
description: When and why to use this agent
mode: subagent
model: anthropic/claude-sonnet-4-20250514
temperature: 0.7
tools:
  write: false
  bash: false
permission:
  "git *": allow
  "*": ask
maxSteps: 10
---

Your system prompt content here...
```

### Method 2: JSON Configuration

Define agents in `opencode.json`:

```json
{
  "agent": {
    "code-reviewer": {
      "mode": "subagent",
      "description": "Reviews code for quality and security",
      "model": "anthropic/claude-sonnet-4-20250514",
      "tools": {
        "write": false,
        "bash": false
      },
      "prompt": "{file:./prompts/code-reviewer.md}"
    }
  }
}
```

## Configuration Fields

### description (Required)

Brief explanation of the agent's purpose. Used for agent discovery and delegation.

### mode

Agent category:

| Value | Description |
|-------|-------------|
| `primary` | Main conversation agent (Tab key access) |
| `subagent` | Specialist invoked by primary or via `@` |
| `all` | Available as both |

### model

Full model identifier:
```yaml
model: anthropic/claude-sonnet-4-20250514
model: anthropic/claude-haiku-4-20250514
model: anthropic/claude-opus-4-20250514
```

### temperature

Control randomness (0.0-1.0):
- Lower values = more focused, deterministic
- Higher values = more creative, varied

### tools

Object specifying tool access:

```yaml
tools:
  write: true    # File writing
  edit: true     # File editing
  bash: true     # Shell commands
  webfetch: true # Web requests
```

Or disable all:
```yaml
tools:
  write: false
  bash: false
```

### permission

Fine-grained command permissions:

| Value | Description |
|-------|-------------|
| `ask` | Approval required before execution |
| `allow` | Unrestricted access |
| `deny` | Tool disabled |

**Wildcards supported:**
```yaml
permission:
  "git *": allow      # Allow all git commands
  "npm install": ask  # Ask for npm install
  "*": deny           # Deny everything else
```

### prompt

System prompt content. Can be inline or reference a file:

```yaml
prompt: "You are a code reviewer..."
# Or reference external file:
prompt: "{file:./prompts/code-reviewer.md}"
```

### maxSteps

Limit agentic iterations before text-only response:
```yaml
maxSteps: 10
```

### disable

Disable the agent without deleting:
```yaml
disable: true
```

## Invoking Agents

### Manual Invocation
Use `@` mention in chat:
```
@code-reviewer check this function for bugs
```

### Tab Key (Primary Agents)
Press Tab to cycle through primary agents.

### Automatic Delegation
Primary agents delegate to subagents based on task matching.

## Built-in Agents

### Primary Agents

| Agent | Description |
|-------|-------------|
| **Build** | Default agent with complete tool access |
| **Plan** | Restricted mode - file edits and bash default to "ask" |

### Subagents

| Agent | Description |
|-------|-------------|
| **General** | Multi-step research and code searching |
| **Explore** | Fast-specialized for pattern matching |

## Creating Agents Interactively

Run the interactive setup:
```bash
opencode agent create
```

This guides you through:
1. Location selection (global or project)
2. Agent description
3. System prompt generation
4. Tool access selection

## Example Configurations

### Read-Only Code Reviewer (Markdown)

File: `.opencode/agent/code-reviewer.md`
```markdown
---
description: Reviews code for quality, security, and best practices
mode: subagent
model: anthropic/claude-sonnet-4-20250514
tools:
  write: false
  edit: false
  bash: false
---

You are a senior code reviewer. Analyze code for:
- Code quality and readability
- Security vulnerabilities
- Performance issues
- Best practices violations

Output format:
## Critical Issues
## Warnings
## Suggestions
```

### Security Auditor (JSON)

In `opencode.json`:
```json
{
  "agent": {
    "security-auditor": {
      "mode": "subagent",
      "description": "Performs security audits and identifies vulnerabilities",
      "model": "anthropic/claude-opus-4-20250514",
      "tools": {
        "write": false,
        "bash": false
      },
      "permission": {
        "*": "deny"
      }
    }
  }
}
```
