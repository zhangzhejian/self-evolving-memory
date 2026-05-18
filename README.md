# Self-Evolving Memory

**A local-first MemoryOps skill for AI agents.**

Self-Evolving Memory turns every correction into a safer, more useful agent memory update.
Instead of letting an agent silently overwrite context, it makes memory changes explicit:

```text
observe -> diagnose -> propose patch -> validate -> apply -> record outcome
```

The goal is simple: **stop teaching your agent the same thing twice**.

## Why

Long-running agents do not just need more memory. They need better memory hygiene.

Common failure modes:

- "I already told you that."
- "You remembered the wrong room/project/person."
- "That was true last week, but it is stale now."
- "You treated one example as a permanent preference."
- "The right memory existed, but you did not use it."
- "Your skill workflow keeps writing noisy memories."

Self-Evolving Memory gives agents a disciplined way to handle those failures:

- classify what went wrong,
- create an evidence-backed patch,
- validate when needed,
- apply the change,
- keep an append-only event and patch trail.

## What It Is

This repository is a portable agent skill plus a shell helper:

```text
SKILL.md                  # Skill entry point
scripts/memory_ops.sh     # File-backed MemoryOps helper
references/
  schema.md               # Memory item, event, patch, retrieval policy schemas
  diagnostics.md          # Memory failure taxonomy and decision rules
  evaluation.md           # Regression eval patterns for memory changes
agents/openai.yaml        # UI metadata for skill lists
```

It is intentionally small:

- local files only,
- no daemon,
- no telemetry,
- no external API,
- transparent shell + `jq`,
- Git-friendly JSONL logs.

## Core Idea

Treat memory like code.

Do not directly mutate long-term memory when a user corrects the agent. Create a patch:

```json
{
  "kind": "update_memory",
  "failure_mode": "stale_memory",
  "target": "mem_forward_room",
  "reason": "User confirmed the notification target is final, not a candidate.",
  "before": "Forward target candidate: rm_oc_...",
  "after": "Forward all notifications to rm_oc_...",
  "status": "proposed"
}
```

Then verify and apply it.

## Storage Layout

Default store:

```text
.memory/
  events.jsonl
  working.md
  long_term/
    profile.md
    facts.jsonl
    projects/
  meta/
    memory-system.md
    patches.jsonl
    retrieval-policy.md
  evals/
    cases.jsonl
    runs.jsonl
```

## Install

### Codex / OpenAI-style skills

Copy the skill into your skills directory:

```bash
cp -R self-evolving-memory ~/.codex/skills/self-evolving-memory
```

Restart Codex so the skill registry can pick it up.

### From GitHub

```bash
git clone https://github.com/zhangzhejian/self-evolving-memory.git
cp -R self-evolving-memory ~/.codex/skills/self-evolving-memory
```

### Manual Use Without a Skill Runtime

You can use the shell helper directly in any project:

```bash
sh scripts/memory_ops.sh init --store .memory
```

Requires:

- POSIX shell
- `jq`

## Quick Start

Initialize a memory store:

```bash
sh scripts/memory_ops.sh init --store .memory
```

Record user feedback:

```bash
sh scripts/memory_ops.sh event \
  --store .memory \
  --type feedback \
  --source user \
  --text "User confirmed notifications must go to rm_oc_551556d402d68263." \
  --tag memory-failure
```

Create a memory patch:

```bash
patch_id=$(
  sh scripts/memory_ops.sh patch \
    --store .memory \
    --kind add_memory \
    --failure-mode missing_memory \
    --reason "Need confirmed notification target." \
    --after "Forward all monitor notifications to rm_oc_551556d402d68263." \
    --risk low |
  jq -r .id
)
```

Apply the patch:

```bash
sh scripts/memory_ops.sh apply \
  --store .memory \
  --patch "$patch_id" \
  --memory-type policy \
  --scope global \
  --confidence 1 \
  --validated \
  --tag notification-target
```

Inspect memory:

```bash
sh scripts/memory_ops.sh list facts --store .memory
sh scripts/memory_ops.sh list patches --store .memory
sh scripts/memory_ops.sh list events --store .memory
```

## Failure Modes

The skill asks the agent to classify feedback before changing memory:

| Failure mode | Meaning |
| --- | --- |
| `missing_memory` | The agent should have remembered something but did not. |
| `wrong_memory` | Stored memory is false. |
| `stale_memory` | Memory was true before but is outdated now. |
| `overgeneralized_memory` | A narrow signal became an overly broad rule. |
| `underused_memory` | Relevant memory existed but was not used. |
| `conflicting_memory` | Active memories disagree. |
| `bad_schema` | The memory structure cannot represent what is needed. |
| `bad_retrieval` | Retrieval/filtering/ranking failed. |
| `bad_skill` | A skill workflow caused wrong capture or wrong use. |

## When to Use

Use Self-Evolving Memory when building agents that need continuity:

- personal assistants,
- coding agents,
- customer support bots,
- monitoring agents,
- project copilots,
- team coordination agents,
- any agent that should improve from user corrections.

Good prompts:

```text
Review your memory and find stale facts.
I corrected you twice; diagnose the memory failure.
Turn this feedback into a memory patch.
Update the skill so this memory bug does not happen again.
Run a memory eval before applying this change.
```

## Design Principles

- **Evidence over vibes**: durable memory needs source evidence.
- **Patch, do not overwrite**: memory changes should be reviewable.
- **Prefer deprecation over deletion**: keep history unless there is a reason to remove it.
- **Eval meaningful changes**: schema, retrieval, and skill patches need verification.
- **Local-first**: memory should be inspectable with normal tools.
- **Reversible by default**: bad memory updates should be easy to roll back.

## Security and Privacy

This project does not call external services. The helper script writes local files only.

Still, memory can contain sensitive data. Treat `.memory/` as private unless you have explicitly reviewed it.

Suggested `.gitignore`:

```gitignore
.memory/
```

## Status

Early, practical, and intentionally small.

The current version focuses on file-backed memory operations and skill instructions. Future work may include richer eval runners, conflict reports, memory compaction, and retrieval-policy testing.

## License

MIT
