# Agent Best Practices

For advanced patterns including state machines, request classification, and orchestration, see [advanced-patterns.md](advanced-patterns.md).

## Prompt Hygiene Over Time

Agent system prompts are especially susceptible to accumulation because:
- Multiple engineers contribute over time
- They're composed from templates + custom additions
- Problems get "fixed" by adding emphasis rather than restructuring

**The accumulation anti-pattern:**
```
Engineer A: "X isn't working well" → adds "IMPORTANT: always do X"
                ↓
This implicitly dilutes Y → Y starts failing
                ↓
Engineer B: adds "CRITICAL: never skip Y"
                ↓
Cycle continues until prompt is bloated and internally competing
```

**The fix:** Periodically review agent prompts holistically, not incrementally.

**Audit checklist:**
- [ ] Remove duplicate instructions (same intent stated multiple ways)
- [ ] Tighten verbose formulations (fewer tokens, same meaning)
- [ ] Check for conflicting emphasis (if everything is "critical", nothing is)
- [ ] Verify intent balance (no single concern dominating at expense of others)
- [ ] Ask: "What would I remove?" before asking "What should I add?"

**Insight from production:** Lovable improved instruction-following and saved $20M/year by removing duplicates, tightening language, and maintaining intent balance—not by adding more emphasis.

## Design Principles

### 1. Single Responsibility

Each agent should do ONE thing well.

**Good:**
- `code-reviewer` - Reviews code quality
- `security-auditor` - Checks security issues
- `test-runner` - Runs and fixes tests

**Bad:**
- `code-helper` - Reviews, writes, tests, documents (too broad)

### 2. Clear Triggers

Descriptions should tell Claude exactly WHEN to use the agent.

**Good descriptions:**
```yaml
description: Use proactively after any code changes to review quality and security.
description: Invoke when user mentions performance issues or slow queries.
description: MUST BE USED when creating or modifying API endpoints.
```

**Bad descriptions:**
```yaml
description: Helps with code.
description: General purpose assistant.
description: Does various things.
```

### 3. Minimal Tool Access

Grant only necessary tools.

**Questions to ask:**
- Does this agent need to modify files? (If no, omit Edit/Write)
- Does this agent need to run commands? (If no, omit Bash)
- Does this agent need web access? (If no, omit WebFetch/WebSearch)

### 4. Specific System Prompts

Vague prompts produce vague results.

**Bad prompt:**
```
You are a helpful code assistant. Help the user with their code.
```

**Good prompt:**
```
You are a senior code reviewer specializing in security.

When invoked:
1. Run git diff to see recent changes
2. Read each modified file
3. Check against security checklist

Security checklist:
- No hardcoded secrets
- Input validation present
- SQL injection prevention
- XSS prevention
- Proper authentication checks

Output format:
## Security Review

### Critical Issues
[List any security vulnerabilities]

### Warnings
[List potential concerns]

### Passed Checks
[List verified security measures]
```

## System Prompt Structure

### Recommended Template

```markdown
You are a [specific role] specializing in [domain].

When invoked:
1. [First action - be specific]
2. [Second action]
3. [Third action]

[Domain knowledge section]

[Checklist or criteria]

[Output format specification]

[Constraints or limitations]
```

### Key Elements

#### Role Definition
Be specific about expertise:
```
You are a senior backend engineer specializing in database optimization.
```

Not:
```
You are a helpful assistant.
```

#### First Action
Tell the agent what to do IMMEDIATELY:
```
When invoked:
1. Run git diff to see what changed
2. Identify files in the diff
3. Begin review
```

Not:
```
When invoked:
1. Think about what to do
2. Consider options
3. Maybe do something
```

#### Domain Knowledge
Include expertise the agent needs:
```
Performance optimization priorities:
- Index usage before table scans
- Query plan analysis
- Connection pooling
- Caching strategies
```

#### Output Format
Specify how results should look:
```
Output format:
## [Section Name]
- Finding 1
- Finding 2

## Recommendations
1. [Specific action]
2. [Specific action]
```

## Model Selection

### When to Use Each Model

| Model | Best For | Avoid For |
|-------|----------|-----------|
| `haiku` | Fast searches, simple lookups, high-volume tasks | Complex reasoning, architecture decisions |
| `sonnet` | Most tasks, balanced quality/speed | Simple lookups (wasteful) |
| `opus` | Complex analysis, architecture, nuanced decisions | Simple tasks (slow and expensive) |
| `inherit` | Consistency with main conversation | When agent needs specific capability |

### Common Patterns

```yaml
# Fast explorer
model: haiku

# Standard worker
model: sonnet

# Architect/planner
model: opus

# Match user's choice
model: inherit
```

## Testing Agents

### Before Deployment

1. **Test explicit invocation:**
   ```
   > Use the [agent-name] agent to [specific task]
   ```

2. **Verify tool access:**
   - Can it read files it needs?
   - Can it run necessary commands?
   - Is it blocked from things it shouldn't do?

3. **Check output format:**
   - Does output match specification?
   - Is it actionable?

4. **Test edge cases:**
   - Empty results
   - Large files
   - Error conditions

### Iterating on Prompts

1. Start with minimal prompt
2. Test with real tasks
3. Note where it fails or is unclear
4. Add specific guidance for those cases
5. Repeat

## Common Mistakes

### 1. Over-Engineering

**Problem:** Agent tries to do too much
**Solution:** Split into focused agents

### 2. Vague Descriptions

**Problem:** Claude doesn't know when to use agent
**Solution:** Add specific trigger conditions

### 3. Missing First Action

**Problem:** Agent doesn't know where to start
**Solution:** Specify immediate first step

### 4. No Output Format

**Problem:** Inconsistent, hard-to-use results
**Solution:** Define exact output structure

### 5. Over-Permissioned

**Problem:** Agent has tools it doesn't need
**Solution:** Specify exact tool list

### 6. Wrong Model

**Problem:** Slow for simple tasks or poor quality for complex
**Solution:** Match model to task complexity

## Version Control

### Project Agents

Store in `.claude/agents/` and commit:
```bash
git add .claude/agents/
git commit -m "Add code-reviewer agent"
```

Benefits:
- Team can use same agents
- Changes tracked
- Can review agent changes in PRs

### Documentation

Consider adding to your project README:
```markdown
## Claude Code Agents

This project includes custom agents:

- `code-reviewer` - Reviews PRs for quality
- `test-runner` - Runs tests and fixes failures
- `deploy-helper` - Assists with deployments
```
