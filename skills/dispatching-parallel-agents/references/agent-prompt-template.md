# Agent Prompt Template

Use this template when dispatching parallel agents for independent problem domains.

## Template Structure

```markdown
Fix the {{NUMBER}} failing tests in {{FILE_PATH}}:

{{NUMBERED_LIST_OF_FAILURES}}

These are {{PROBLEM_TYPE}} issues. Your task:

1. Read the test file and understand what each test verifies
2. Identify root cause - {{DIAGNOSTIC_QUESTIONS}}
3. Fix by:
   - {{FIX_APPROACH_1}}
   - {{FIX_APPROACH_2}}
   - {{FIX_APPROACH_3}}

Do NOT {{CONSTRAINTS}}.

Return: {{EXPECTED_OUTPUT_FORMAT}}
```

## Placeholder Definitions

| Placeholder | Description | Example |
|------------|-------------|---------|
| `{{NUMBER}}` | Count of failures in scope | `3` |
| `{{FILE_PATH}}` | Relative path to test file | `src/agents/agent-tool-abort.test.ts` |
| `{{NUMBERED_LIST_OF_FAILURES}}` | Test names with error context | See below |
| `{{PROBLEM_TYPE}}` | Category of issue | `timing/race condition`, `null reference`, `async` |
| `{{DIAGNOSTIC_QUESTIONS}}` | What agent should investigate | `timing issues or actual bugs?` |
| `{{FIX_APPROACH_N}}` | Allowed fix strategies | `Replace arbitrary timeouts with event-based waiting` |
| `{{CONSTRAINTS}}` | What NOT to do | `just increase timeouts - find the real issue` |
| `{{EXPECTED_OUTPUT_FORMAT}}` | What agent returns | `Summary of what you found and what you fixed` |

## Example: Filled Template

```markdown
Fix the 3 failing tests in src/agents/agent-tool-abort.test.ts:

1. "should abort tool with partial output capture" - expects 'interrupted at' in message
2. "should handle mixed completed and aborted tools" - fast tool aborted instead of completed
3. "should properly track pendingToolCount" - expects 3 results but gets 0

These are timing/race condition issues. Your task:

1. Read the test file and understand what each test verifies
2. Identify root cause - timing issues or actual bugs?
3. Fix by:
   - Replacing arbitrary timeouts with event-based waiting
   - Fixing bugs in abort implementation if found
   - Adjusting test expectations if testing changed behavior

Do NOT just increase timeouts - find the real issue.

Return: Summary of what you found and what you fixed.
```

## Prompt Quality Checklist

Before dispatching, verify the prompt is:

- [ ] **Focused** - Single clear problem domain
- [ ] **Self-contained** - All context needed to understand the problem
- [ ] **Specific about output** - Clear expectation for what agent returns
- [ ] **Constrained** - Boundaries on what NOT to change
- [ ] **Actionable** - Concrete steps agent can follow

## Common Anti-Patterns

| Anti-Pattern | Problem | Fix |
|-------------|---------|-----|
| "Fix all the tests" | Too broad, agent gets lost | Scope to one file/subsystem |
| "Fix the race condition" | No context about location | Paste error messages and test names |
| No constraints | Agent might refactor everything | Add "Do NOT change production code" |
| "Fix it" | Vague output expectation | "Return summary of root cause and changes" |
