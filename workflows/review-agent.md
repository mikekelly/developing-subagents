<required_reading>
Before reviewing, read based on platform:
- Claude Code → references/claude-code-config.md
- OpenCode → references/opencode-config.md
- Both → references/best-practices.md
</required_reading>

<objective>
Audit an existing agent configuration against best practices for Claude Code or OpenCode.
</objective>

<process>
## Step 1: Identify Platform

Determine platform from file path:
- `.claude/agents/` or `~/.claude/agents/` → Claude Code
- `.opencode/agent/` or `~/.config/opencode/agent/` → OpenCode
- `opencode.json` → OpenCode (JSON format)

## Step 2: Read the Agent File

Get the agent file path and read its contents.

## Step 3: Validate Configuration

### Claude Code Validation

Check required fields:
- [ ] `name`: Present, lowercase, hyphens only
- [ ] `description`: Present, describes when to use

Check optional fields if present:
- [ ] `tools`: Valid comma-separated list (Read, Write, Edit, Glob, Grep, Bash, Task, etc.)
- [ ] `model`: Valid value (sonnet, opus, haiku, inherit)
- [ ] `permissionMode`: Valid if present (default, acceptEdits, bypassPermissions, plan, ignore)
- [ ] `skills`: Valid comma-separated list if present

### OpenCode Validation

Check required fields:
- [ ] `description`: Present, describes when to use

Check optional fields:
- [ ] `mode`: Valid value (primary, subagent, all)
- [ ] `model`: Full model identifier (anthropic/claude-...)
- [ ] `temperature`: Number between 0.0 and 1.0
- [ ] `tools`: Object with boolean values (write, edit, bash, webfetch)
- [ ] `permission`: Object with allow/ask/deny values
- [ ] `maxSteps`: Positive integer
- [ ] `disable`: Boolean

## Step 4: Evaluate Description Quality

Good descriptions:
- State what the agent does
- State when to use it
- Use action words ("Use proactively", "Invoke when")

Flag if description:
- Is vague ("helps with code")
- Doesn't indicate when to use
- Is too long (should be 1-2 sentences)

## Step 5: Assess Tool Selection

### Claude Code
Check if tools match purpose:
- **Over-permissioned**: Has Write/Edit but only analyzes
- **Under-permissioned**: Missing tools needed for purpose
- **Optimal**: Has exactly what's needed

### OpenCode
Check tool booleans:
- Read-only agent with `write: true` → over-permissioned
- Code modifier with `write: false` → under-permissioned
- Check `permission` wildcards for security

## Step 6: Review System Prompt

Quality checklist:
- [ ] Clear role definition
- [ ] Specific first action (what to do immediately)
- [ ] Step-by-step procedure
- [ ] Domain-specific guidance
- [ ] Output format specification
- [ ] Success criteria or checklist

Common issues:
- No clear starting action
- Too vague ("be helpful")
- Missing output format
- No domain expertise included

## Step 7: Check Model Selection

### Claude Code
- `haiku` for simple/fast tasks
- `sonnet` for balanced tasks
- `opus` for complex reasoning
- `inherit` for consistency

### OpenCode
- Haiku for exploration
- Sonnet for most tasks
- Opus for complex analysis

Flag if:
- Complex task using haiku
- Simple task using opus (wasteful)

## Step 8: Generate Report

```markdown
## Agent Review: [name]

### Platform
[Claude Code / OpenCode]

### Summary
[Overall assessment]

### Issues Found
1. [Critical issues - must fix]
2. [Warnings - should fix]
3. [Suggestions - nice to have]

### Recommended Changes
[Specific changes with examples]

### Strengths
[What's working well]
```
</process>

<success_criteria>
Review is complete when:
- [ ] Platform identified correctly
- [ ] All configuration fields validated for that platform
- [ ] Description quality assessed
- [ ] Tool selection evaluated
- [ ] System prompt reviewed for completeness
- [ ] Model selection checked
- [ ] Report generated with actionable recommendations
</success_criteria>
