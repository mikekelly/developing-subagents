---
name: developing-subagents
description: MUST be loaded before working with any subagent. Covers creating, building, reviewing, auditing, updating, modifying, and understanding Claude Code subagents and the Task tool. Use when setting up agent configurations, debugging agent behavior, or learning how agents work.
---

<essential_principles>
Subagents are pre-configured AI assistants that Claude Code delegates tasks to. Each subagent:
- Has a specific purpose and expertise area
- Uses its own context window separate from the main conversation
- Can be configured with specific tools it's allowed to use
- Includes a custom system prompt guiding its behavior

**Key Benefits:**
- **Context preservation**: Prevents pollution of main conversation
- **Specialized expertise**: Fine-tuned instructions for specific domains
- **Reusability**: Use across projects, share with teams
- **Flexible permissions**: Different tool access per subagent

**File Locations:**

| Type | Location | Scope | Priority |
|------|----------|-------|----------|
| Project subagents | `.claude/agents/` | Current project | Highest |
| User subagents | `~/.claude/agents/` | All projects | Lower |

Project-level subagents take precedence when names conflict.

**File Format (Required):**

```markdown
---
name: your-agent-name
description: Description of when this agent should be invoked
tools: tool1, tool2, tool3  # Optional - inherits all if omitted
model: sonnet  # Optional - sonnet, opus, haiku, or inherit
permissionMode: default  # Optional
skills: skill1, skill2  # Optional - skills to auto-load
---

Your agent's system prompt goes here. Define the agent's role,
capabilities, and approach to solving problems.

Include specific instructions, best practices, and constraints.
```

**Configuration Fields:**

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Unique identifier (lowercase, hyphens only) |
| `description` | Yes | Natural language description of purpose |
| `tools` | No | Comma-separated tool list. Omit to inherit all |
| `model` | No | `sonnet`, `opus`, `haiku`, or `inherit` |
| `permissionMode` | No | `default`, `acceptEdits`, `bypassPermissions`, `plan`, `ignore` |
| `skills` | No | Comma-separated skills to auto-load |

**Built-in Subagents:**

Claude Code includes these built-in subagents:

- **general-purpose**: Complex multi-step tasks, can read/write files (Sonnet)
- **Explore**: Fast read-only codebase exploration (Haiku)
- **Plan**: Research during plan mode (Sonnet)

**Advanced Patterns:**

For complex agents, treat prompts as state machines with:
- Phase-based execution (classify → assess → execute)
- Request classification (different types → different strategies)
- Mandatory output structures (not suggestions, requirements)
- Explicit anti-patterns (NEVER DO sections)
- Escalation triggers (when to stop and ask for help)

See `references/advanced-patterns.md` for detailed guidance.

**Using the Task Tool:**

The Task tool launches subagents. Required parameters:
- `description`: Short (3-5 word) summary
- `prompt`: Detailed task instructions
- `subagent_type`: Which agent type to use

Optional parameters:
- `model`: Override model (`sonnet`, `opus`, `haiku`)
- `resume`: Agent ID to continue previous conversation
- `run_in_background`: Run asynchronously

Example Task tool invocation:
```json
{
  "description": "Review authentication code",
  "prompt": "Review the auth module for security issues...",
  "subagent_type": "code-reviewer"
}
```
</essential_principles>

<intake>
What would you like to do?

1. **Create** a new subagent
2. **Review** an existing subagent
3. **Update** an existing subagent
4. **Understand** how subagents work

**Wait for response before proceeding.**
</intake>

<routing>
| Response | Next Action | Workflow |
|----------|-------------|----------|
| 1, "create", "new", "build" | Ask: "Project-level or user-level agent?" | workflows/create-agent.md |
| 2, "review", "audit", "check" | Ask: "Path to agent file?" | workflows/review-agent.md |
| 3, "update", "modify", "edit" | Ask: "Path to agent file?" | workflows/update-agent.md |
| 4, "understand", "explain", "how" | Explain concepts | workflows/understand-agents.md |

**Intent-based routing:**
- "create code reviewer", "build debugger agent" → workflows/create-agent.md
- "review my agent", "check agent config" → workflows/review-agent.md
- "add tools to agent", "change agent model" → workflows/update-agent.md
- "how do agents work", "what is Task tool" → workflows/understand-agents.md

**After reading the workflow, follow it exactly.**
</routing>

<quick_reference>
**Quick Commands:**

**Create agent interactively:**
```
/agents
```

**Invoke agent explicitly:**
```
> Use the code-reviewer agent to check my changes
```

**Resume previous agent:**
```json
{
  "resume": "previous-agent-id",
  "prompt": "Continue the analysis..."
}
```

**Run agent in background:**
```json
{
  "run_in_background": true,
  "prompt": "Long-running task..."
}
```
</quick_reference>

<reference_index>
**Domain Knowledge:**

All in `references/`:

| Reference | Content |
|-----------|---------|
| agent-configuration.md | Complete field reference, model selection |
| available-tools.md | All tools agents can access |
| best-practices.md | Design principles, prompt writing |
| advanced-patterns.md | State machines, request classification, delegation, escalation |
| example-agents.md | Code reviewer, debugger, data scientist |
</reference_index>

<workflows_index>
**Workflows:**

All in `workflows/`:

| Workflow | Purpose |
|----------|---------|
| create-agent.md | Build a new subagent from scratch |
| review-agent.md | Audit agent against best practices |
| update-agent.md | Modify existing agent configuration |
| understand-agents.md | Learn how subagents work |
</workflows_index>
