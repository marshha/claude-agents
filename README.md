# claude-agents

A set of four Claude Code subagent definitions for a software development workflow: design a feature, implement it, test it, and document it.

## Suggested workflow

```
feature-design  →  feature-implement  →  test-writer  →  doc-writer
```

1. **`feature-design`** explores the codebase and writes a structured implementation plan to `docs/plans/<feature-name>-plan.md`
2. **`feature-implement`** reads that plan and executes it step by step, committing after each step
3. **`test-writer`** writes tests for the implemented code
4. **`doc-writer`** writes or updates documentation

`feature-implement` requires a plan file as input and will not proceed without one.

## Agents

| Name | Model | Description |
|---|---|---|
| `feature-design` | opus | Guides through designing a feature — explores the codebase, proposes a structured implementation plan, writes it to `docs/plans/<feature-name>-plan.md`, and iterates until ready to execute |
| `feature-implement` | sonnet | Reads a markdown plan produced by `feature-design` and executes it step by step — making code changes, running tests and linting, and committing after each step |
| `test-writer` | opus | Writes tests covering happy path and edge cases, reasoning from intended behavior rather than current output |
| `doc-writer` | sonnet | Writes documentation grounded exclusively in code that has been read or online sources that are explicitly cited |

## Invocation

Use an @-mention to guarantee a specific agent runs for a task:

```
@"feature-design (agent)" add a rate-limiting middleware
@"feature-implement (agent)" execute the plan at docs/plans/rate-limiting-plan.md
@"test-writer (agent)" write tests for src/middleware/rateLimit.ts
@"doc-writer (agent)" update the README
```

You can also refer to an agent by name in natural language; Claude will decide whether to delegate:

```
Use the feature-design agent to plan adding OAuth support
```

See the [Claude Code subagents documentation](https://code.claude.com/docs/en/sub-agents) for full invocation options including the `--agent` CLI flag.

## Tool access

Each agent has access to a specific set of tools, as declared in the `tools` field of its frontmatter.

| Agent | Tools |
|---|---|
| `feature-design` | Read, Glob, Grep, Bash, Write, Edit, AskUserQuestion, WebFetch, WebSearch |
| `feature-implement` | Read, Glob, Grep, Bash, Write, Edit, AskUserQuestion |
| `test-writer` | Read, Glob, Grep, Bash, Write, Edit, AskUserQuestion |
| `doc-writer` | Read, Glob, Grep, Bash, Write, Edit, AskUserQuestion, WebFetch, WebSearch |

## Files

```
.claude/agents/
├── doc-writer.md
├── feature-design.md
├── feature-implement.md
└── test-writer.md
```

These are project-scoped agents. Per the [Claude Code documentation](https://code.claude.com/docs/en/sub-agents#choose-the-subagent-scope), files in `.claude/agents/` are available to anyone who works in this project. To use these agents in another project, copy the files to that project's `.claude/agents/` directory, or to `~/.claude/agents/` to make them available across all your projects.

## Requirements

[Claude Code](https://claude.ai/download).
