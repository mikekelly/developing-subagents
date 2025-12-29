<required_reading>
Before updating, read based on platform:
- Claude Code → references/claude-code-config.md
- OpenCode → references/opencode-config.md
- Both → references/available-tools.md
</required_reading>

<objective>
Modify an existing agent's configuration, tools, or system prompt for Claude Code or OpenCode.
</objective>

<process>
## Step 1: Identify Platform

Determine platform from file path:
- `.claude/agents/` or `~/.claude/agents/` → Claude Code
- `.opencode/agent/` or `~/.config/opencode/agent/` → OpenCode
- `opencode.json` → OpenCode (JSON format)

## Step 2: Read Current Agent

Get the agent file path and read its contents to understand current configuration.

## Step 3: Identify Change Type

Ask: "What would you like to change?"

Common updates:
- Add/remove tools
- Change model
- Improve system prompt
- Update description
- Change permissions (OpenCode)
- Add skills (Claude Code)
- Change mode (OpenCode)

## Step 4: Make Changes

### Updating Tools

#### Claude Code:
```yaml
# Before
tools: Read, Grep, Glob

# After (add Edit, Write)
tools: Read, Grep, Glob, Edit, Write
```

#### OpenCode:
```yaml
# Before
tools:
  write: false
  bash: false

# After
tools:
  write: true
  edit: true
  bash: true
```

### Updating Model

#### Claude Code:
```yaml
model: sonnet  # Options: sonnet, opus, haiku, inherit
```

#### OpenCode:
```yaml
model: anthropic/claude-opus-4-20250514
```

### Updating Description

Both platforms - ensure description includes:
1. What the agent does
2. When to use it
3. Trigger words if needed ("proactively", "automatically")

### Updating System Prompt

When improving prompts:
- Add specific first action if missing
- Add step-by-step procedure if vague
- Add output format if not specified
- Add domain expertise for specialized agents

### Updating Permissions (OpenCode Only)

```yaml
permission:
  "git *": allow
  "npm test": allow
  "*": ask
```

### Updating Mode (OpenCode Only)

```yaml
mode: primary    # Main conversation agent
mode: subagent   # Specialist
mode: all        # Both
```

### Adding Skills (Claude Code Only)

```yaml
skills: skill1, skill2
```

Note: Subagents don't inherit skills from parent.

## Step 5: Validate Changes

### Claude Code:
- [ ] YAML syntax is valid
- [ ] `name` hasn't changed (creates new agent otherwise)
- [ ] Tools are comma-separated
- [ ] Model is valid value

### OpenCode:
- [ ] YAML syntax is valid
- [ ] Filename matches intended name
- [ ] Tools are object with booleans
- [ ] Model is full identifier
- [ ] Permissions use valid values (allow/ask/deny)

## Step 6: Save and Test

Save the updated file.

### Claude Code:
```
> Use the [agent-name] agent to [task matching new capabilities]
```

### OpenCode:
```
@agent-name [task matching new capabilities]
```

Verify:
- New tools are accessible
- Model change is reflected
- System prompt changes affect behavior
</process>

<success_criteria>
Update is complete when:
- [ ] Platform correctly identified
- [ ] Changes made to correct fields for that platform
- [ ] Configuration syntax validated
- [ ] File saved to same location
- [ ] Agent tested with new configuration
- [ ] Behavior matches expected changes
</success_criteria>
