# Agent Configuration Reference (Shared)

This reference covers concepts shared between Claude Code and OpenCode agents, plus migration guidance.

## Core Concepts (Both Platforms)

### What Are Agents?

Agents are pre-configured AI assistants with:
- **Separate context**: Own conversation history
- **Custom configuration**: Specific tools, model, system prompt
- **Focused expertise**: Optimized for particular tasks

### Agent Lifecycle

1. User makes request in main conversation
2. Platform evaluates if an agent matches the task
3. Agent is invoked with the task
4. Agent works independently in its own context
5. Agent returns results to main conversation
6. Results presented to user

### System Prompt Structure

Both platforms use similar prompt structure:

```markdown
You are a [role] specializing in [domain].

When invoked:
1. [First action]
2. [Second action]
3. [Continue pattern]

[Domain-specific guidance]

[Checklist or criteria]

[Output format requirements]
```

## Platform Comparison

### File Locations

| Type | Claude Code | OpenCode |
|------|-------------|----------|
| Project | `.claude/agents/` | `.opencode/agent/` |
| User | `~/.claude/agents/` | `~/.config/opencode/agent/` |
| CLI/Config | `--agents` JSON | `opencode.json` |

### Configuration Format

**Claude Code (YAML frontmatter):**
```yaml
---
name: code-reviewer
description: Reviews code quality
tools: Read, Grep, Glob, Bash
model: sonnet
permissionMode: default
skills: commit
---
```

**OpenCode (YAML frontmatter or JSON):**
```yaml
---
description: Reviews code quality
mode: subagent
model: anthropic/claude-sonnet-4-20250514
temperature: 0.7
tools:
  write: false
  bash: false
permission:
  "git *": allow
---
```

### Field Mapping

| Concept | Claude Code | OpenCode |
|---------|-------------|----------|
| Agent name | `name:` field | Filename |
| Description | `description:` | `description:` |
| Agent type | Always subagent | `mode: primary\|subagent\|all` |
| Model | `model: sonnet` | `model: anthropic/claude-...` |
| Tools | Comma-separated names | Object with booleans |
| Permissions | `permissionMode:` | `permission:` with wildcards |
| Skills/Prompts | `skills:` field | `prompt: {file:...}` |

### Tool Access Comparison

**Claude Code tools:**
- `Read`, `Write`, `Edit`, `Glob`, `Grep`
- `Bash`, `Task`
- `WebFetch`, `WebSearch`, `LSP`
- `AskUserQuestion`, `TodoWrite`

**OpenCode tools:**
- `write` (includes Write + Edit)
- `bash` (shell commands)
- `webfetch` (web requests)

### Model Aliases

| Claude Code | OpenCode |
|-------------|----------|
| `sonnet` | `anthropic/claude-sonnet-4-20250514` |
| `opus` | `anthropic/claude-opus-4-20250514` |
| `haiku` | `anthropic/claude-haiku-4-20250514` |
| `inherit` | (use parent model) |

## Migration Guide

### Claude Code → OpenCode

1. **Create agent directory:**
   ```bash
   mkdir -p .opencode/agent
   ```

2. **Rename and restructure file:**
   - Remove `name:` field (use filename instead)
   - Add `mode: subagent`
   - Convert model alias to full identifier
   - Convert tools to object format
   - Convert permissionMode to permission object

3. **Example conversion:**

   **Before (Claude Code):**
   ```yaml
   ---
   name: code-reviewer
   description: Reviews code for quality
   tools: Read, Grep, Glob, Bash
   model: sonnet
   permissionMode: default
   ---
   ```

   **After (OpenCode) - `.opencode/agent/code-reviewer.md`:**
   ```yaml
   ---
   description: Reviews code for quality
   mode: subagent
   model: anthropic/claude-sonnet-4-20250514
   tools:
     write: false
     bash: true
   ---
   ```

### OpenCode → Claude Code

1. **Create agent directory:**
   ```bash
   mkdir -p .claude/agents
   ```

2. **Restructure file:**
   - Add `name:` field matching filename
   - Remove `mode:` (all are subagents)
   - Convert model to alias
   - Convert tools object to comma-separated list

3. **Example conversion:**

   **Before (OpenCode):**
   ```yaml
   ---
   description: Reviews code for quality
   mode: subagent
   model: anthropic/claude-sonnet-4-20250514
   tools:
     write: false
     bash: true
   ---
   ```

   **After (Claude Code) - `.claude/agents/code-reviewer.md`:**
   ```yaml
   ---
   name: code-reviewer
   description: Reviews code for quality
   tools: Read, Grep, Glob, Bash
   model: sonnet
   ---
   ```

## Shared Best Practices

These principles apply to both platforms:

1. **Single responsibility**: One agent, one job
2. **Clear triggers**: Description says when to use
3. **Minimal tools**: Only grant what's needed
4. **Specific prompts**: Step-by-step instructions
5. **Defined output**: Specify expected format
6. **Test explicitly**: Verify before relying on auto-delegation
