---
name: developing-subagents
description: MUST be loaded before working with any subagent or agent. Covers creating, building, reviewing, auditing, updating, modifying, and understanding agents for Claude Code and OpenCode. Use when setting up agent configurations, debugging agent behavior, or learning how agents work. Supports both platforms.
---

<essential_principles>
Agents are pre-configured AI assistants that can be delegated specific tasks. Both **Claude Code** and **OpenCode** support custom agents with similar concepts but different implementations.

**Key Benefits (Both Platforms):**
- **Context preservation**: Separate context prevents pollution of main conversation
- **Specialized expertise**: Fine-tuned instructions for specific domains
- **Reusability**: Use across projects, share with teams
- **Flexible permissions**: Different tool access per agent

**Platform Comparison:**

| Aspect | Claude Code | OpenCode |
|--------|-------------|----------|
| Agent types | Subagents only | Primary + Subagents |
| Project location | `.claude/agents/` | `.opencode/agent/` |
| User location | `~/.claude/agents/` | `~/.config/opencode/agent/` |
| CLI config | `--agents` JSON flag | `opencode.json` |
| Tool format | Comma-separated names | Object with booleans |
| Model format | Aliases (sonnet, opus, haiku) | Full IDs (anthropic/claude-...) |

**Built-in Agents:**

| Agent | Claude Code | OpenCode | Purpose |
|-------|-------------|----------|---------|
| Build | - | ✓ (primary) | Default agent, full tool access |
| Plan | ✓ | ✓ (primary) | Analysis without modifications |
| General | ✓ | ✓ (subagent) | Multi-step research and tasks |
| Explore | ✓ | ✓ (subagent) | Fast codebase exploration |

**Advanced Patterns:**

For complex agents on either platform, treat prompts as state machines with:
- Phase-based execution (classify → assess → execute)
- Request classification (different types → different strategies)
- Mandatory output structures (not suggestions, requirements)
- Explicit anti-patterns (NEVER DO sections)
- Escalation triggers (when to stop and ask for help)

See `references/advanced-patterns.md` for detailed guidance.
</essential_principles>

<intake>
**Which platform are you working with?**

- **Claude Code** (`.claude/agents/`, Task tool)
- **OpenCode** (`.opencode/agent/`, `opencode.json`)

**What would you like to do?**

1. **Create** a new agent
2. **Review** an existing agent
3. **Update** an existing agent
4. **Understand** how agents work

**Wait for response before proceeding.**
</intake>

<routing>
**Platform Detection:**
- If user mentions "Claude Code", `.claude/`, or Task tool → Claude Code platform
- If user mentions "OpenCode", `.opencode/`, or `opencode.json` → OpenCode platform
- If unclear → Ask which platform

**Required Reading by Platform:**
- Claude Code → `references/claude-code-config.md`
- OpenCode → `references/opencode-config.md`
- Both → `references/agent-configuration.md` (shared concepts)

| Response | Next Action | Workflow |
|----------|-------------|----------|
| 1, "create", "new", "build" | Ask: "Project-level or user-level?" | workflows/create-agent.md |
| 2, "review", "audit", "check" | Ask: "Path to agent file?" | workflows/review-agent.md |
| 3, "update", "modify", "edit" | Ask: "Path to agent file?" | workflows/update-agent.md |
| 4, "understand", "explain", "how" | Explain concepts | workflows/understand-agents.md |

**Intent-based routing:**
- "create code reviewer", "build debugger agent" → workflows/create-agent.md
- "review my agent", "check agent config" → workflows/review-agent.md
- "add tools to agent", "change agent model" → workflows/update-agent.md
- "how do agents work", "what is Task tool" → workflows/understand-agents.md
- "OpenCode primary agent", "opencode subagent" → detect OpenCode platform

**After determining platform, read the appropriate reference, then follow the workflow exactly.**
</routing>

<quick_reference>
**Quick Commands:**

| Action | Claude Code | OpenCode |
|--------|-------------|----------|
| Create interactively | `/agents` | `opencode agent create` |
| Invoke explicitly | `Use the code-reviewer agent...` | `@agent-name prompt` |
| List agents | `/agents` | `opencode agent list` |

**Claude Code - Task Tool:**
```json
{
  "description": "Review auth code",
  "prompt": "Check for security issues...",
  "subagent_type": "code-reviewer",
  "model": "sonnet",
  "resume": "agent-id",
  "run_in_background": true
}
```

**OpenCode - JSON Config:**
```json
{
  "agent": {
    "code-reviewer": {
      "mode": "subagent",
      "description": "Reviews code for quality",
      "model": "anthropic/claude-sonnet-4-20250514",
      "tools": {"write": false, "bash": false}
    }
  }
}
```
</quick_reference>

<reference_index>
**Domain Knowledge:**

All in `references/`:

| Reference | Content |
|-----------|---------|
| claude-code-config.md | Claude Code specific: YAML frontmatter, tools, Task tool |
| opencode-config.md | OpenCode specific: JSON config, modes, permissions |
| agent-configuration.md | Shared concepts, comparison, migration guide |
| available-tools.md | All tools agents can access (both platforms) |
| best-practices.md | Design principles, prompt writing |
| advanced-patterns.md | State machines, request classification, delegation |
| example-agents.md | Examples for both platforms |
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
