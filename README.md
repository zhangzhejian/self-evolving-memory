# Self-Evolving Memory

**A memory skill that helps AI agents learn from corrections.**

Most agent memory systems can store facts. The hard part is what happens after the agent gets something wrong:

- it forgot something you already said,
- it remembered an old fact,
- it used the wrong preference,
- it over-learned from one example,
- it had the right memory but ignored it.

Self-Evolving Memory gives agents a simple habit:

> When corrected, do not just answer better this time. Fix the memory so the same mistake is less likely next time.

## The Promise

**Stop teaching your agent the same thing twice.**

Every correction can become a clean memory update:

```text
user correction -> diagnose the memory problem -> propose a fix -> apply it -> remember why
```

That means the agent's memory becomes:

- easier to trust,
- easier to inspect,
- less likely to become a pile of stale notes,
- better at improving from real feedback.

## A Tiny Example

You tell your agent:

```text
No, do not treat that room as a candidate. Always send Garry's finance updates to rm_oc_551556d402d68263.
```

Without this skill, the agent might only fix the current answer.

With this skill, the agent should record:

```text
Problem: stale or underspecified memory
Fix: confirmed forwarding room is rm_oc_551556d402d68263
Evidence: user explicitly confirmed it
Result: future notifications use the confirmed room
```

## What Is Included

```text
SKILL.md                  # The agent-facing workflow
scripts/memory_ops.sh     # Small shell helper for memory logs and patches
references/               # Optional deeper guides
agents/openai.yaml        # Skill metadata
```

The project is intentionally small:

- local files only,
- no background service,
- no external API,
- no telemetry,
- shell + `jq`,
- human-readable logs.

## Install

Clone the repo:

```bash
git clone https://github.com/zhangzhejian/self-evolving-memory.git
```

Install as a Codex/OpenAI-style skill:

```bash
cp -R self-evolving-memory ~/.codex/skills/self-evolving-memory
```

Restart Codex so it can load the new skill.

## Use It Directly

You can also use the shell helper without a skill runtime.

Initialize a memory folder:

```bash
sh scripts/memory_ops.sh init --store .memory
```

Record feedback:

```bash
sh scripts/memory_ops.sh event \
  --store .memory \
  --type feedback \
  --source user \
  --text "User confirmed the forwarding room."
```

Create a memory fix:

```bash
patch_id=$(
  sh scripts/memory_ops.sh patch \
    --store .memory \
    --kind add_memory \
    --failure-mode missing_memory \
    --reason "Need confirmed forwarding room." \
    --after "Always forward Garry finance updates to rm_oc_551556d402d68263." |
  jq -r .id
)
```

Apply it:

```bash
sh scripts/memory_ops.sh apply \
  --store .memory \
  --patch "$patch_id" \
  --memory-type policy \
  --scope global \
  --confidence 1 \
  --validated
```

Inspect what changed:

```bash
sh scripts/memory_ops.sh list facts --store .memory
sh scripts/memory_ops.sh list patches --store .memory
sh scripts/memory_ops.sh list events --store .memory
```

## When To Use It

Use this skill when you want an agent to improve across sessions:

- personal assistants,
- coding agents,
- monitoring bots,
- customer support agents,
- project copilots,
- team coordination agents.

Good prompts:

```text
Remember this using the self-evolving memory workflow.
I corrected you twice. Diagnose the memory issue.
Review your memory and find stale facts.
Turn this feedback into a memory patch.
Update the skill so this memory bug does not happen again.
```

## How It Thinks About Memory Problems

The skill teaches the agent to classify memory failures before changing memory:

- missing memory,
- wrong memory,
- stale memory,
- overgeneralized memory,
- ignored memory,
- conflicting memory,
- bad storage structure,
- bad retrieval behavior,
- bad skill workflow.

The point is not to create a complicated memory database. The point is to give the agent a better reflex after mistakes.

## Privacy

This project writes local files only.

Still, memory can contain private information. Do not commit `.memory/` unless you have reviewed it.

```gitignore
.memory/
```

## Requirements

- POSIX shell
- `jq`

## License

MIT
