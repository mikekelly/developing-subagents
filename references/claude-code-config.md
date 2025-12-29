# Claude Code Agent Configuration

## File Format

Claude Code agents use Markdown files with YAML frontmatter:

```markdown
---
name: agent-name
description: When and why to use this agent
tools: tool1, tool2, tool3
model: sonnet
permissionMode: default
skills: skill1, skill2
---

System prompt content here...
```

## File Locations

| Type | Location | Scope | Priority |
|------|----------|-------|----------|
| CLI-defined | `--agents` flag | Session only | Highest |
| Project | `.claude/agents/` | Current project | High |
| User | `~/.claude/agents/` | All projects | Lower |
| Plugin | Plugin's `agents/` dir | Plugin scope | Lowest |

When agent names conflict, higher priority wins.

## Required Fields

### name

Unique identifier for the agent.

**Requirements:**
- Lowercase letters only
- Hyphens for word separation
- No spaces or special characters
- Must be unique within scope

**Examples:**
```yaml
name: code-reviewer      # Good
name: test-runner        # Good
name: Code_Reviewer      # Bad - uppercase and underscore
```

### description

Natural language description telling Claude WHEN to invoke the agent.

**Trigger phrases for proactive use:**
- "Use proactively"
- "Use immediately after"
- "MUST BE USED when"

**Examples:**
```yaml
# Good - clear trigger
description: Expert code reviewer. Use proactively after any code modifications.

# Bad - no trigger indication
description: Helps with code.
```

## Optional Fields

### tools

Comma-separated list of tools the agent can access.

**Behavior:**
- If omitted: Agent inherits ALL tools from main thread (including MCP)
- If specified: Agent can ONLY use listed tools

**Available tools:**
- File: `Read`, `Write`, `Edit`, `Glob`, `Grep`
- System: `Bash`, `Task`
- Info: `WebFetch`, `WebSearch`, `LSP`
- Interactive: `AskUserQuestion`, `TodoWrite`
- Special: `NotebookEdit`

**Format:**
```yaml
tools: Read, Grep, Glob, Bash
```

### model

Which AI model the agent uses.

| Value | Description |
|-------|-------------|
| `sonnet` | Balanced performance (default) |
| `opus` | Most capable, complex reasoning |
| `haiku` | Fastest, simple tasks |
| `inherit` | Use same model as main conversation |

### permissionMode

How the agent handles permission requests.

| Value | Description |
|-------|-------------|
| `default` | Normal permission handling |
| `acceptEdits` | Auto-accept file edits |
| `bypassPermissions` | Skip all permission prompts |
| `plan` | Planning mode (read-only research) |
| `ignore` | Ignore permission system |

### skills

Skills to auto-load when agent starts.

**Important:** Subagents do NOT inherit skills from parent. Must list explicitly.

```yaml
skills: commit, review-pr
```

## Using the Task Tool

The Task tool launches subagents from the main conversation.

**Required parameters:**
```json
{
  "description": "Short 3-5 word summary",
  "prompt": "Detailed instructions for the subagent",
  "subagent_type": "agent-name"
}
```

**Optional parameters:**
- `model`: Override agent's default model
- `resume`: Agent ID to continue previous conversation
- `run_in_background`: Run asynchronously

**Example:**
```json
{
  "description": "Review authentication code",
  "prompt": "Review the auth module for security issues. Check for SQL injection, XSS, and hardcoded credentials.",
  "subagent_type": "code-reviewer",
  "model": "opus"
}
```

## CLI-Based Configuration

Define agents via command line for testing or automation:

```bash
claude --agents '{
  "code-reviewer": {
    "description": "Expert code reviewer. Use proactively after code changes.",
    "prompt": "You are a senior code reviewer...",
    "tools": ["Read", "Grep", "Glob", "Bash"],
    "model": "sonnet"
  }
}'
```

## Built-in Subagents

| Agent | Model | Mode | Purpose |
|-------|-------|------|---------|
| general-purpose | Sonnet | Read/Write | Complex multi-step tasks |
| Explore | Haiku | Read-only | Fast codebase exploration |
| Plan | Sonnet | Read-only | Research during plan mode |

## Resumable Agents

Agents can be resumed to continue previous work:

```json
{
  "resume": "agent-id-from-previous",
  "prompt": "Continue analyzing the authentication module...",
  "subagent_type": "code-analyzer"
}
```

Agent transcripts are stored as `agent-{agentId}.jsonl` files.
