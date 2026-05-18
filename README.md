# Self-Evolving Memory

**A local-first skill for improving AI agent memory from user corrections.**

Self-Evolving Memory gives long-running agents a disciplined way to update memory when they get something wrong. Instead of simply replying "got it", the agent diagnoses the memory issue, applies a deliberate update, and keeps track of why the change was made.

> Stop teaching your agent the same thing twice.

## Quick Start

Send this repository to an agent that supports skill installation:

> Install this skill: https://github.com/zhangzhejian/self-evolving-memory

The agent should fetch the repository, install it into its skills directory, and reload or restart so the skill can be discovered.

## Why This Exists

Agent memory often fails in subtle ways. An agent may remember an old fact, apply a preference too broadly, ignore a relevant memory, or keep accumulating notes without knowing which ones are still useful.

Self-Evolving Memory is built for those moments. It helps agents treat corrections as opportunities to improve future behavior, not just patch the current response.

## How It Works

The skill teaches a simple memory-improvement loop:

```text
notice feedback -> diagnose the memory issue -> propose a change -> apply carefully -> record why
```

This keeps memory updates:

- **intentional**: the agent identifies what kind of memory problem occurred,
- **explainable**: each change has a reason,
- **auditable**: updates are recorded instead of hidden,
- **reversible**: bad memory changes can be inspected and corrected,
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
- Integrations with common agent memory stores

## Status

Early and practical. The current version focuses on the core workflow: helping agents improve memory from real feedback instead of silently accumulating messy notes.

## License

MIT
