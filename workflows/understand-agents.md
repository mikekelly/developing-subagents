<required_reading>
For deeper understanding, optionally read:
- references/agent-configuration.md (comparison)
- references/claude-code-config.md
- references/opencode-config.md
</required_reading>

<objective>
Explain how agents work in Claude Code and OpenCode, including delegation, context management, and configuration patterns.
</objective>

<process>
## Core Concepts (Both Platforms)

### What Are Agents?

Agents are specialized AI assistants with:
- **Separate context**: Own conversation history, doesn't pollute main thread
- **Custom configuration**: Specific tools, model, and system prompt
- **Focused expertise**: Optimized for particular task types

### How Delegation Works

1. User makes request in main conversation
2. Platform evaluates if an agent matches the task
3. Agent is invoked via platform mechanism
4. Agent works independently in its own context
5. Agent returns results to main conversation
6. Results presented to user

## Claude Code Specifics

### The Task Tool

Claude Code uses the Task tool to launch subagents:

```json
{
  "description": "Short 3-5 word summary",
  "prompt": "Detailed instructions for the subagent",
  "subagent_type": "agent-name",
  "model": "sonnet",
  "resume": "agent-id",
  "run_in_background": true
}
```

### Invocation Methods

**Automatic**: Claude reads agent descriptions and invokes when tasks match.

Triggers for automatic use:
- Description includes "use proactively"
- Task matches agent's stated purpose
- Agent has tools needed for the task

**Explicit**:
```
> Use the code-reviewer agent to check my changes
```

### Resumable Agents

Continue previous agent conversations:
```json
{
  "resume": "previous-agent-id",
  "prompt": "Continue analyzing..."
}
```

### Built-in Subagents

| Agent | Model | Mode | Purpose |
|-------|-------|------|---------|
| general-purpose | Sonnet | Read/Write | Complex multi-step tasks |
| Explore | Haiku | Read-only | Fast codebase search |
| Plan | Sonnet | Read-only | Research during planning |

## OpenCode Specifics

### Agent Types

**Primary Agents:**
- Main conversation agents accessed via Tab key
- Built-in: Build (default), Plan (restricted)

**Subagents:**
- Specialists invoked by primary or via `@mention`
- Built-in: General, Explore

### Invocation Methods

**Primary agents**: Tab key to switch

**Subagents via @mention**:
```
@code-reviewer check this function
```

**Automatic delegation**: Primary agents delegate based on task matching

### Configuration Methods

1. **Markdown files**: `.opencode/agent/agent-name.md`
2. **JSON config**: `opencode.json` with `"agent": {}` section
3. **CLI**: `opencode agent create`

### Unique Features

- **Temperature control**: Adjust randomness (0.0-1.0)
- **Permission wildcards**: Fine-grained command control
- **maxSteps**: Limit agentic iterations

## Platform Comparison

| Feature | Claude Code | OpenCode |
|---------|-------------|----------|
| Agent types | Subagents only | Primary + Subagents |
| Invocation | Task tool | @mention |
| Tool format | Comma-separated | Boolean object |
| Model format | Aliases | Full identifiers |
| Resumable | Yes | No |
| Permissions | permissionMode | permission object |

## Context Management

Both platforms:
- Each agent has fresh context window
- No access to main conversation history
- Own tool permissions
- Results returned as single message

Benefits:
- Main conversation stays focused
- Long explorations don't consume main context
- Specialized prompts improve quality

## When to Create Custom Agents

Create custom agents for:
- Domain-specific expertise (security, performance, accessibility)
- Project-specific workflows (your testing framework, deploy process)
- Team standards (code style, documentation format)

## Best Practices Summary

1. **Single purpose**: One agent, one job
2. **Clear triggers**: Description says when to use
3. **Minimal tools**: Only what's needed
4. **Specific prompts**: Step-by-step instructions
5. **Test explicitly**: Verify before relying on auto-delegation
</process>

<common_questions>
## FAQ

**Q: When should I create a custom agent vs use built-in?**
A: Create custom when you need domain expertise, project-specific workflows, or team standards that built-ins don't provide.

**Q: Project vs user level agents?**
A: Project for team sharing and version control. User for personal productivity across projects.

**Q: Why isn't my agent being used automatically?**
A: Check description clarity. Add "use proactively" or make trigger conditions clearer.

**Q: Can agents call other agents?**
A: Claude Code: No, subagents cannot spawn other subagents. OpenCode: Primary agents can delegate to subagents.

**Q: How do I migrate agents between platforms?**
A: See references/agent-configuration.md for migration guide.

**Q: Claude Code or OpenCode - which should I use?**
A: Both work well. Claude Code has resumable agents and MCP tools. OpenCode has primary agents and granular permissions.
</common_questions>

<success_criteria>
Understanding is complete when user can answer:
- [ ] What is the difference between main conversation and agent context?
- [ ] How do Claude Code and OpenCode differ in agent invocation?
- [ ] When should I create a custom agent vs use built-in?
- [ ] What triggers automatic vs explicit invocation?
- [ ] How do I choose between project and user level agents?
</success_criteria>
