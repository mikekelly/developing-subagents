<required_reading>
Before starting, read based on platform:
- Claude Code → references/claude-code-config.md
- OpenCode → references/opencode-config.md
- Both → references/best-practices.md, templates/agent-template.md
</required_reading>

<objective>
Create a new agent with proper configuration for Claude Code or OpenCode.
</objective>

<process>
## Step 1: Determine Platform

If not already known, ask: "Claude Code or OpenCode?"

**Platform indicators:**
- Claude Code: `.claude/agents/`, Task tool, `--agents` flag
- OpenCode: `.opencode/agent/`, `opencode.json`, `@mention`

## Step 2: Determine Agent Location

Ask: "Project-level or user-level?"

| Platform | Project | User |
|----------|---------|------|
| Claude Code | `.claude/agents/` | `~/.claude/agents/` |
| OpenCode | `.opencode/agent/` | `~/.config/opencode/agent/` |

- **Project-level**: Available only in current project, version controlled
- **User-level**: Available across all projects

## Step 3: Define Agent Purpose

Ask: "What specific task should this agent handle?"

Good agent purposes:
- Code review with security focus
- Test running and failure analysis
- Documentation generation
- Database query optimization

Bad (too broad):
- "General coding help"
- "Do everything"

## Step 4: Choose Agent Name

Requirements:
- Lowercase letters and hyphens only
- Descriptive of purpose
- Examples: `code-reviewer`, `test-runner`, `api-debugger`

**Note:** For Claude Code, name goes in YAML. For OpenCode, name is the filename.

## Step 5: Determine Agent Mode (OpenCode Only)

For OpenCode, ask: "Primary agent or subagent?"

- **Primary**: Main conversation agent (Tab key access)
- **Subagent**: Specialist invoked by primary or via `@mention`

## Step 6: Write Description

The description tells the platform WHEN to use this agent.

**Trigger phrases:**
- "Use proactively"
- "Use immediately after"
- "MUST BE USED when"

**Example:**
```
Expert code reviewer. Use proactively after any code modifications to check for quality and security issues.
```

## Step 7: Select Tools

### For Claude Code:
Common tool sets:
```yaml
# Read-only
tools: Read, Grep, Glob, Bash

# Code modification
tools: Read, Edit, Write, Bash, Grep, Glob
```

### For OpenCode:
```yaml
# Read-only
tools:
  write: false
  edit: false
  bash: false

# Code modification
tools:
  write: true
  edit: true
  bash: true
```

## Step 8: Select Model

### Claude Code:
| Model | Use Case |
|-------|----------|
| `haiku` | Fast, simple tasks |
| `sonnet` | Balanced (default) |
| `opus` | Complex reasoning |
| `inherit` | Match main conversation |

### OpenCode:
```yaml
model: anthropic/claude-sonnet-4-20250514
model: anthropic/claude-opus-4-20250514
model: anthropic/claude-haiku-4-20250514
```

## Step 9: Write System Prompt

Structure (works for both platforms):

```markdown
You are a [role] specializing in [domain].

When invoked:
1. [First action]
2. [Second action]
3. [Third action]

[Domain-specific guidance]

[Checklist or criteria]

## Output Format

### [Section 1]
[Content]

### Recommendations
[Actionable items]
```

## Step 10: Create the File

### Claude Code:
```bash
mkdir -p .claude/agents  # or ~/.claude/agents
```

```markdown
---
name: agent-name
description: Description here
tools: Read, Grep, Glob, Bash
model: sonnet
---

System prompt here...
```

### OpenCode:
```bash
mkdir -p .opencode/agent  # or ~/.config/opencode/agent
```

```markdown
---
description: Description here
mode: subagent
model: anthropic/claude-sonnet-4-20250514
tools:
  write: false
  bash: true
---

System prompt here...
```

## Step 11: Test the Agent

### Claude Code:
```
> Use the [agent-name] agent to [specific task]
```

### OpenCode:
```
@agent-name [specific task]
```

Verify:
- Agent activates correctly
- Has access to expected tools
- Follows system prompt instructions
- Produces expected output format
</process>

<success_criteria>
Agent is complete when:
- [ ] File is in correct location for platform
- [ ] Name/filename uses lowercase with hyphens
- [ ] Description clearly states when to use
- [ ] Tools are appropriate for purpose
- [ ] Model matches task complexity
- [ ] System prompt has clear step-by-step instructions
- [ ] Agent has been tested with explicit invocation
</success_criteria>
