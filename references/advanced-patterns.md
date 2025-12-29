# Advanced Agent Patterns

These patterns elevate agents from simple task executors to autonomous specialists that reduce hallucination and improve reliability.

## Core Insight: Prompts as State Machines

The most effective agents treat prompts as state machines, not text. Each phase has:
- **Entry conditions** - What triggers this phase
- **Actions** - What to do in this phase
- **Exit criteria** - How to know the phase is complete
- **Transitions** - What phase comes next

This structure prevents the agent from skipping steps or getting lost.

## Pattern 1: Phase-Based Execution

Structure agent prompts with explicit phases:

```markdown
## Phase 0: Intent Classification

Before acting, classify the request:
- TYPE A (Quick lookup): Single file, known location → grep/glob directly
- TYPE B (Exploration): Unknown location, multiple angles → use explore agent
- TYPE C (Research): External docs, examples needed → use librarian agent
- TYPE D (Complex): Multi-step implementation → create todo list first

**Exit criteria:** Request type identified, execution strategy selected.

## Phase 1: Assessment

Evaluate the codebase before making changes:
- Check existing patterns in similar files
- Review recent commits for conventions
- Classify codebase: disciplined / transitional / legacy / greenfield

**Exit criteria:** Know what patterns to follow (or intentionally break).

## Phase 2: Execution

Execute based on classification from Phase 0:
- TYPE A: Direct action, no delegation
- TYPE B: Delegate to specialist, await results
- TYPE C: Research first, then implement
- TYPE D: Create todos, execute sequentially, verify each step

**Exit criteria:** Task complete, verified working.
```

## Pattern 2: Request Classification

Classify requests upfront to determine execution strategy:

```markdown
When invoked, first classify the request:

| Type | Pattern | Strategy | Min Parallel Calls |
|------|---------|----------|-------------------|
| Conceptual | "How do I X?" | docs + search | 3+ |
| Implementation | "Show me source" | clone + read | 4+ |
| Context | "Why was this changed?" | git history + issues | 4+ |
| Comprehensive | Complex multi-part | all tools | 6+ |

Different request types require different execution strategies.
Never apply a one-size-fits-all approach.
```

## Pattern 3: Mandatory Output Structure

Don't suggest output format—require it:

```markdown
Every response MUST include these sections:

<intent_analysis>
Distinguish between literal request and underlying need.
What does the user actually want to achieve?
</intent_analysis>

<findings>
**Files:** [list of relevant files with line numbers]
**Answer:** [direct answer to the question]
**Evidence:** [code snippets or links supporting the answer]
</findings>

<next_steps>
What should happen after this? Specific actionable items.
</next_steps>

Responses missing any section are incomplete.
```

## Pattern 4: Tool Cost Awareness

Agents should understand tool economics:

```markdown
Tool allocation strategy:

| Tool | Cost | Use When |
|------|------|----------|
| grep/glob/read | FREE | Clear scope, known locations |
| explore agent | FREE | Multiple search angles, unfamiliar modules |
| librarian agent | CHEAP | External docs, OSS examples |
| oracle agent | EXPENSIVE | Architecture decisions, post-failure debugging |

Default flow:
1. Start with FREE tools
2. Escalate to CHEAP if FREE insufficient
3. Reserve EXPENSIVE for truly complex decisions

Never use expensive tools for simple lookups.
```

## Pattern 5: Delegation Requirements

When delegating to other agents, require structured handoff:

```markdown
When delegating work, prompts MUST include:

1. **TASK**: What specifically needs to be done
2. **EXPECTED OUTCOME**: What success looks like
3. **REQUIRED TOOLS**: What tools the subagent needs
4. **MUST DO**: Non-negotiable requirements
5. **MUST NOT DO**: Explicit prohibitions
6. **CONTEXT**: Background information needed
7. **EXIT CRITERIA**: How to know it's done

Vague delegation prompts are rejected. If you can't fill all 7 sections,
you don't understand the task well enough to delegate it.
```

Example delegation:
```markdown
TASK: Find all API endpoints that handle user authentication

EXPECTED OUTCOME: List of files with line numbers for each auth endpoint

REQUIRED TOOLS: Grep, Glob, Read

MUST DO:
- Search for route definitions containing "auth", "login", "session"
- Include middleware that checks authentication
- Report file:line format

MUST NOT DO:
- Modify any files
- Execute any code
- Make assumptions about auth flow

CONTEXT: Preparing for security audit of authentication system

EXIT CRITERIA: Complete list of auth-related endpoints with evidence
```

## Pattern 6: Anti-Pattern Sections

Explicitly define what the agent must NEVER do:

```markdown
## NEVER DO

- Never suppress type errors with `as any` or `@ts-ignore`
- Never start implementing unless explicitly requested
- Never search for outdated year in queries (use current year)
- Never commit without running tests
- Never delete tests to make suite pass
- Never apply "fix" that just hides the symptom

These are hard constraints, not guidelines.
Violating any is grounds for stopping and reconsidering.
```

## Pattern 7: Escalation Triggers

Define when to stop and ask for help:

```markdown
## Escalation Protocol

Stop and escalate to [senior agent/user] when:
- 3 consecutive attempts have failed
- Uncertainty exceeds 70% on architectural decision
- Change would affect more than 5 files
- Security-sensitive code requires modification
- Test coverage would drop below threshold

Escalation format:
"I've attempted X three times with results Y.
My hypothesis is Z but confidence is low.
Recommend consulting [oracle/user] before proceeding."

Knowing when NOT to proceed is as important as knowing how to proceed.
```

## Pattern 8: Parallel Execution Requirements

Specify minimum parallel tool usage:

```markdown
## Execution Strategy

For thorough analysis, never search sequentially.
Launch multiple tools in parallel to cross-validate:

Minimum parallel calls by task type:
- Simple lookup: 2+ tools
- Code search: 3+ tools
- Research: 4+ tools
- Comprehensive: 6+ tools

Example parallel search for "auth handler":
1. grep for "auth" in route files
2. glob for *auth*.ts patterns
3. LSP find references on AuthMiddleware
4. grep for "login" and "session" patterns

Cross-validate results. If tools disagree, investigate discrepancy.
```

## Putting It Together: Complete Example

```markdown
---
name: codebase-analyst
description: Deep codebase analysis specialist. Use for understanding unfamiliar code, finding implementations, or architectural review.
tools: Read, Grep, Glob, Bash, LSP
model: sonnet
---

You are a codebase analyst specializing in deep code understanding.

## Phase 0: Request Classification

Classify every request:
- TYPE A (Needle): Specific function/class → direct grep/LSP
- TYPE B (Pattern): Similar implementations → glob + sampling
- TYPE C (Flow): How data moves → trace calls, read files
- TYPE D (Architecture): System design → comprehensive analysis

## Phase 1: Parallel Discovery

Minimum tool calls by type:
- TYPE A: 2+ parallel searches
- TYPE B: 3+ parallel with different patterns
- TYPE C: 4+ parallel (entry points, handlers, models, tests)
- TYPE D: 6+ parallel across all layers

Never search sequentially. Always cross-validate.

## Phase 2: Synthesis

<required_output>
<files>
List every relevant file with line numbers.
Format: `path/to/file.ts:42-67`
</files>

<answer>
Direct answer to the question.
</answer>

<evidence>
Code snippets supporting the answer.
</evidence>

<confidence>
HIGH / MEDIUM / LOW with explanation.
</confidence>
</required_output>

## NEVER DO

- Never guess file locations without searching
- Never report findings without file:line evidence
- Never stop at first match—find ALL relevant code
- Never provide answer without confidence assessment

## Escalation

If confidence is LOW after Phase 2, state:
"Confidence is low. Found X but may be incomplete.
Recommend: [specific additional search or user clarification]"
```

## Key Takeaways

1. **Structure creates autonomy** - Rigid phases paradoxically enable more autonomous behavior
2. **Classification reduces errors** - Knowing request type determines correct execution path
3. **Mandatory output prevents drift** - Required sections ensure complete responses
4. **Cost awareness improves efficiency** - Right tool for right job
5. **Explicit anti-patterns prevent mistakes** - NEVER DO is as important as MUST DO
6. **Escalation builds trust** - Knowing limits is a feature, not a bug
