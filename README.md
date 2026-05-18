# Self-Evolving Memory

**A local-first memory improvement workflow for long-running AI agents.**

Self-Evolving Memory helps agents turn user corrections into durable memory improvements. Instead of simply saying "got it" and moving on, an agent can diagnose what went wrong, update memory deliberately, and preserve the reason for the change.

The goal is simple:

> Stop teaching your agent the same thing twice.

## Overview

Most agent memory systems focus on storing more information. Self-Evolving Memory focuses on improving memory quality.

It is designed for situations where an agent:

- forgets something the user already explained,
- remembers an outdated fact,
- applies a preference too broadly,
- ignores relevant memory that already exists,
- updates memory without a clear reason,
- accumulates messy notes over time.

This project provides a portable skill and a small local helper script that give agents a repeatable memory-update discipline.

## Core Workflow

When feedback arrives, the agent should not immediately overwrite memory.

It should follow a small loop:

```text
observe feedback -> diagnose memory issue -> propose change -> apply carefully -> record why
```

This makes memory changes:

- **deliberate**: the agent identifies the type of memory problem first,
- **explainable**: the reason for the update is preserved,
- **auditable**: changes are recorded instead of hidden,
- **reversible**: bad memory updates can be inspected and corrected,
- **safer**: one-off feedback is less likely to become an overbroad rule.

## Example

A user says:

> "No, that is not a candidate room. Always send those updates to this exact room."

A normal agent may only fix the current response.

Self-Evolving Memory encourages the agent to treat the correction as a memory improvement:

- the old memory was unclear,
- the user confirmed the final rule,
- future behavior should follow the confirmed rule,
- the reason for the change should be recorded.

That is the difference between saving notes and improving behavior.

## What This Project Contains

```text
SKILL.md                  # Agent-facing workflow
scripts/memory_ops.sh     # Local helper for memory events and changes
references/
  diagnostics.md          # Memory failure taxonomy
  schema.md               # Reference data shapes
  evaluation.md           # Memory evaluation guidance
agents/openai.yaml        # Skill metadata
```

The skill is the main product. The script is intentionally small and local-first.

## Use Cases

Self-Evolving Memory is useful for long-running agents such as:

- personal assistants,
- coding agents,
- monitoring bots,
- customer support agents,
- project copilots,
- team coordination agents,
- agent frameworks with persistent memory.

It is especially useful when the same user or team works with the same agent over time.

## Design Principles

### Memory should improve from correction

Every meaningful correction is a chance to improve future behavior, not just fix the current answer.

### Memory should be explainable

Users and developers should be able to understand why a memory changed.

### Memory should not grow blindly

The agent should distinguish stable preferences from temporary instructions, stale facts, and weak signals.

### Memory should be local and inspectable

The current implementation uses local files. There is no server, daemon, telemetry, or external API.

### Memory workflows can evolve too

Sometimes the problem is not a single memory item. It may be the retrieval policy, the storage shape, or the skill workflow itself.

## Installation

To install the skill, send this GitHub repository to an agent that supports skill installation and ask it to install the skill:

> Install this skill: https://github.com/zhangzhejian/self-evolving-memory

The agent should fetch the repository, place it in its skills directory, and reload or restart so the skill can be discovered.

For runtimes that use the bundled helper script directly, the local requirements are:

- POSIX shell
- `jq`

## Privacy

Self-Evolving Memory is local-first and does not send data to external services.

Memory can still contain private information. Treat memory files as private unless you have reviewed them.

## Roadmap

Planned directions:

- memory review reports,
- conflict detection,
- stale memory cleanup,
- richer memory evaluation examples,
- skill-level self-improvement patterns,
- integrations with common agent memory stores.

## Status

Early and practical.

The current version focuses on the workflow: helping agents improve memory from real feedback instead of silently accumulating messy notes.

## License

MIT
