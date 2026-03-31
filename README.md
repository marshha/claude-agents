# claude-agents

A set of Claude Code agents for a complete software development workflow — design, implement, test, document, and review — runnable as a single pipeline or as individual agents.

## Quick start

Run the full workflow with a single command:

```
claude --agent feature-workflow
```

Describe the feature you want to build. The orchestrator handles the rest — delegating to specialized agents for each phase, confirming with you between steps, and producing a final summary when done.

## How it works

The `feature-workflow` agent runs five phases in sequence, delegating each to a specialized agent:

```
Design  →  Implement  →  Test  →  Document  →  Code Review
```

1. **Design** — explores the codebase, proposes a structured implementation plan, iterates until you approve, and writes the plan to `docs/plans/<feature-name>-plan.md`
2. **Implement** — reads the plan and executes it step by step, running tests and linting between steps, committing after each one
3. **Test** — writes tests for the implemented code covering happy path, edge cases, and error cases
4. **Document** — writes or updates documentation grounded in the code that was just written
5. **Code Review** — reviews the full change, classifies issues by severity, and distinguishes new problems from pre-existing ones

Between each phase, the orchestrator:
- Summarizes what the previous agent accomplished
- Asks you to confirm before proceeding to the next phase
- Passes relevant context forward (plan file path, changed files, feature scope)

### Skipping phases

You can skip any phase. The orchestrator will confirm each skipped phase individually and explain the consequence before proceeding. For example:

> "You're choosing to skip the Test phase. This means no automated test coverage will be written for this feature. Are you sure?"

You can also start mid-pipeline by providing an existing plan file — the orchestrator will skip the Design phase and begin with Implementation.

### Failure handling

If any agent reports a problem (test failures, lint errors, unresolved ambiguities), the orchestrator stops and offers three options: retry the phase, skip it, or stop the workflow.

## Using individual agents

Each agent can also be used standalone via @-mention for targeted tasks:

```
@"feature-design (agent)" add a rate-limiting middleware
@"feature-implement (agent)" execute the plan at docs/plans/rate-limiting-plan.md
@"test-writer (agent)" write tests for src/middleware/rateLimit.ts
@"doc-writer (agent)" update the README
@"code-review (agent)" review the changes on this branch
```

You can also refer to an agent by name in natural language; Claude will decide whether to delegate:

```
Use the feature-design agent to plan adding OAuth support
```

See the [Claude Code subagents documentation](https://code.claude.com/docs/en/sub-agents) for full invocation options including the `--agent` CLI flag.

## Agents

| Name | Model | Description |
|---|---|---|
| `feature-workflow` | opus | Orchestrates the full feature lifecycle — design, implement, test, document, and review — by delegating to specialized agents in sequence |
| `feature-design` | opus | Guides through designing a feature — explores the codebase, proposes a structured implementation plan, writes it to `docs/plans/<feature-name>-plan.md`, and iterates until ready to execute |
| `feature-implement` | sonnet | Reads a markdown implementation plan and executes it step by step — making code changes, running tests and linting, and committing after each step |
| `test-writer` | opus | Writes tests covering happy path and edge cases, reasoning from intended behavior rather than current output |
| `doc-writer` | sonnet | Writes documentation grounded exclusively in code that has been read or online sources that are explicitly cited |
| `code-review` | opus | Reviews code changes, classifies issues by severity, distinguishes new from pre-existing problems |

## Tool access

| Agent | Tools |
|---|---|
| `feature-workflow` | Agent(feature-design, feature-implement, test-writer, doc-writer, code-review), Read, Bash, AskUserQuestion |
| `feature-design` | Read, Glob, Grep, Bash, Write, Edit, AskUserQuestion, WebFetch, WebSearch |
| `feature-implement` | Read, Glob, Grep, Bash, Write, Edit, AskUserQuestion |
| `test-writer` | Read, Glob, Grep, Bash, Write, Edit, AskUserQuestion |
| `doc-writer` | Read, Glob, Grep, Bash, Write, Edit, AskUserQuestion, WebFetch, WebSearch |
| `code-review` | Read, Glob, Grep, Bash, AskUserQuestion |

## Files

```
.claude/agents/
├── code-review.md
├── doc-writer.md
├── feature-design.md
├── feature-implement.md
├── feature-workflow.md
└── test-writer.md
```

These are project-scoped agents. Per the [Claude Code documentation](https://code.claude.com/docs/en/sub-agents#choose-the-subagent-scope), files in `.claude/agents/` are available to anyone who works in this project. To use these agents in another project, copy the files to that project's `.claude/agents/` directory, or to `~/.claude/agents/` to make them available across all your projects.

## Requirements

[Claude Code](https://claude.ai/download).
