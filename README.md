# Self-Evolving Memory

**A memory skill that helps AI agents learn from corrections.**

Most AI agents can save notes. The harder problem is what happens when the agent gets memory wrong.

It may forget something you already said. It may remember an old fact. It may treat a one-time instruction as a permanent rule. It may even have the right memory but fail to use it.

Self-Evolving Memory is designed for that moment.

## The Idea

When you correct an agent, it should not only fix the current answer.

It should also ask:

- What did I get wrong?
- Was my memory missing, stale, too broad, or ignored?
- What should change so I do better next time?
- Why is this change justified?

Then it updates memory in a way that can be inspected later.

In short:

> Every correction should make the agent better.

## Why It Matters

Good agent memory is not just about storage. It is about trust.

Users want agents that:

- stop making the same mistake,
- remember stable preferences,
- forget or downgrade stale facts,
- explain why memory changed,
- avoid turning every small correction into a permanent rule.

Self-Evolving Memory gives the agent a simple discipline for doing that.

## What It Helps With

This skill is useful when an agent says or implies:

> "I forgot."  
> "I remembered the wrong thing."  
> "I used an outdated fact."  
> "I over-applied your preference."  
> "I had the context, but did not use it."

Instead of treating those as one-off mistakes, the agent treats them as memory problems to diagnose and improve.

## A Simple Example

You tell an agent:

> "No, that is not a candidate room. Always send those updates to this exact room."

A normal agent may say:

> "Got it."

But it may still forget later.

With Self-Evolving Memory, the agent should record the correction as a memory improvement:

- the old memory was unclear,
- the user confirmed the final rule,
- future behavior should use the confirmed rule,
- the reason for the change is preserved.

That is the difference between remembering words and improving behavior.

## How It Works

The skill teaches the agent a small loop:

```text
notice feedback -> diagnose the memory issue -> propose a memory change -> apply it carefully -> remember why
```

The goal is not to build a giant memory database.

The goal is to make memory changes deliberate, explainable, and reversible.

## What Makes It Different

Many memory systems focus on saving more information.

Self-Evolving Memory focuses on improving memory quality.

It helps the agent decide:

- what should be remembered,
- what should stay temporary,
- what should be corrected,
- what should be marked stale,
- what skill or workflow caused the memory mistake.

## Who It Is For

Self-Evolving Memory is useful for long-running agents such as:

- personal assistants,
- coding agents,
- monitoring bots,
- customer support agents,
- project copilots,
- team coordination agents.

It is especially useful when the same user works with the same agent over time.

## The Promise

**Stop teaching your agent the same thing twice.**

Every correction should become part of the agent's learning process.

## Privacy

This project is local-first. It does not need a server or external API.

Memory can still contain private information, so treat memory files as private unless you review them.

## Status

Early and practical.

The current version is a skill plus a small local helper script. The main value is the workflow: helping agents improve memory from real feedback instead of silently accumulating messy notes.

## License

MIT
