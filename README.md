# Self-Evolving Memory

**A local-first skill for helping AI agent memory evolve from user corrections.**

Self-Evolving Memory gives long-running agents a disciplined way to improve memory when they get something wrong. Instead of simply replying "got it", the agent diagnoses the issue, applies a deliberate update, and keeps track of why the change was made.

> Stop teaching your agent the same thing twice.

## Quick Start

Send this repository to an agent that supports skill installation:

> Install this skill: https://github.com/zhangzhejian/self-evolving-memory

The agent should fetch the repository, install it into its skills directory, and reload or restart so the skill can be discovered.

## Why This Exists

Agent memory is not one-size-fits-all. A coding agent, personal assistant, monitoring bot, and customer support agent should not remember things in the same way. Even within the same category, each user may need different memory habits, boundaries, and update rules.

Memory also fails in subtle ways. An agent may remember an old fact, apply a preference too broadly, ignore a relevant memory, or keep accumulating notes without knowing which ones are still useful.

Self-Evolving Memory is built for those moments. It helps agents treat corrections as opportunities to improve future behavior, not just patch the current response.

## How It Works

The skill teaches a simple memory-improvement loop:

```text
notice feedback -> diagnose the memory issue -> propose a change -> apply carefully -> record why
```

Most corrections should update memory. Some should adjust retrieval behavior or storage shape. Occasionally, if the feedback shows that the workflow itself is wrong, the agent may need a cautious skill update.

This keeps memory evolution:

- **intentional**: the agent identifies what kind of memory problem occurred,
- **explainable**: each change has a reason,
- **auditable**: updates are recorded instead of hidden,
- **reversible**: bad memory changes can be inspected and corrected,
- **consistent**: changes in raw events, long-term memory, retrieval rules, and workflow instructions can be reconciled,
- **practical**: the workflow fits normal agent conversations.

## Example

A user says:

> "No, that is not a candidate room. Always send those updates to this exact room."

Without a memory workflow, the agent may only fix the current answer.

With Self-Evolving Memory, the agent should preserve the actual improvement:

- the previous memory was unclear,
- the user confirmed the final rule,
- future behavior should follow that rule,
- the reason for the update is recorded.

The result is an agent that improves from correction instead of repeatedly making the same mistake.

## What Can Evolve

Feedback can point to different levels of the memory system:

- a specific memory item was missing, wrong, or stale,
- the agent retrieved the wrong memory or ignored the right one,
- different memory layers disagree after a correction or schema change,
- the memory structure does not fit the scenario,
- the skill workflow is causing bad memory behavior.

Self-Evolving Memory does not assume a universal best practice. It helps the memory system adapt to the agent's role, the user's preferences, and the situation.

## Cross-Layer Consistency

Real agent memory is rarely one file or one database table. A durable behavior may be shaped by raw event logs, working memory, long-term preferences, project memory, retrieval policy, schedules, cursors, and skill instructions.

This skill treats cross-layer inconsistency as a first-class failure mode. If a new correction updates one layer but an older layer can still drive the wrong behavior, the agent should create a reconciliation patch instead of adding another note.

Examples:

- a raw feedback event says a workflow is disabled, but an old trigger still runs it,
- long-term memory is patched, but project memory still contains the old rule,
- a schema evolves, but old memories are not migrated or deprecated,
- retrieval policy keeps ranking a superseded memory above the corrected one.

The key question is: did the new information only add knowledge, or should it change future behavior? If behavior changes, the affected layers need to be reconciled.

## Evaluation

Early tests use MemoryArena-derived cases to check whether the workflow improves memory failure diagnosis. This is not an official MemoryArena leaderboard result.

In a 10-case smoke test sampled from five MemoryArena subsets (`bundled_shopping`, `formal_reasoning_math`, `formal_reasoning_phys`, `group_travel_planner`, and `progressive_search`), agent outputs were scored with a simple MemoryOps rubric:

- known failure taxonomy,
- scope-specific memory,
- schema or retrieval action,
- workflow boundary action,
- avoidance of naive single-fact updates.

| Setup | Score |
| --- | ---: |
| Generic agent diagnosis, no skill | 38 / 50 |
| With Self-Evolving Memory workflow | 49 / 50 |

The skill did not make the model know more. Strong models already identify many scope problems. The improvement was diagnostic consistency: the agent more reliably mapped feedback to memory failure categories and separated memory-item updates from schema, retrieval, and workflow changes.

## What Is Included

```text
SKILL.md                  # The agent-facing workflow
scripts/memory_ops.sh     # Small local helper for memory events and updates
references/
  diagnostics.md          # Memory failure categories
  schema.md               # Reference data shapes
  evaluation.md           # Guidance for validating memory changes
agents/openai.yaml        # Skill metadata
```

The skill is the main product. The helper script is intentionally small, transparent, and local-first.

## Use Cases

Self-Evolving Memory is useful for agents that operate over time:

- personal assistants,
- coding agents,
- monitoring bots,
- customer support agents,
- project copilots,
- team coordination agents,
- agent frameworks with persistent memory.

It is especially useful when the same user or team works with the same agent across many sessions.

## Design Principles

### Corrections should compound

Each meaningful correction should improve the agent's future behavior.

### Memory should stay explainable

Users and developers should be able to understand why a memory changed.

### Not every note belongs in long-term memory

The agent should distinguish durable preferences from temporary instructions, stale facts, and weak signals.

### Memory practices should fit the scenario

Different agents need different memory shapes. A coding agent may need project and repository context; a monitoring bot may need alert rules and delivery targets; a personal assistant may need preferences and boundaries.

### Memory layers should agree

Raw logs, curated memory, retrieval policy, and skill workflows should not silently contradict each other. When one layer changes future behavior, the related layers should be reconciled.

### Local-first by default

The current implementation uses local files. There is no server, daemon, telemetry, or external API.

### The memory system can evolve too

Sometimes the issue is not one memory item. It may be retrieval behavior, storage structure, or the skill workflow itself.

## Privacy

Self-Evolving Memory does not send data to external services.

Memory can still contain private information. Treat memory files as private unless you have reviewed them.

## Roadmap

- Memory review reports
- Conflict detection
- Stale memory cleanup
- Memory evaluation examples
- Skill-level self-improvement patterns
- Cross-layer reconciliation examples and benchmark cases
- Integrations with common agent memory stores

## Status

Early and practical. The current version focuses on the core workflow: helping agents improve memory from real feedback instead of silently accumulating messy notes.

## License

MIT
