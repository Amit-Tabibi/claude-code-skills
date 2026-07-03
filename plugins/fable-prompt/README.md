# fable-prompt

A [Claude Code](https://claude.com/claude-code) skill that turns a rough ask into a **finished, lean prompt optimized for Claude Fable 5 / Mythos 5** — then tells you which `effort` level to run it at.

## The idea

Fable 5 is capable and obedient enough that **over-specified prompts hurt it.** The old instinct — pile on rules, enumerate every edge case, spell out each "don't do X" — degrades the output. This skill enforces the opposite: give direction and intent, then trust the model. Every prompt it produces is deliberately, almost uncomfortably, short.

It's distilled from Anthropic's official guide, [*Prompting Claude Fable 5*](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5) — the reusable instruction snippets are quoted or adapted straight from it.

## What it does

You give a rough ask ("I need a prompt that does X"). The skill:

1. Pulls out the **task**, the **intent** (why / who it's for — the highest-leverage thing), and the **shape** (one-shot vs. long autonomous run; code, content, or analysis).
2. Assembles a lean prompt: an intent line, the direct request, and *only* the canonical snippets the task actually needs.
3. Hands it back as one copy-paste block, plus a one-line `effort` recommendation.

It leans on seven principles — lead with the reason, short beats enumerated, state the boundaries, don't over-plan or over-build, ground progress claims on long runs, aim high, and never ask the model to echo its reasoning (that risks a refusal).

## Trigger it

Ask for it in natural language:

> *"I need a Fable 5 prompt that reviews this migration plan."*
> *"Turn this into a prompt for Fable 5."*
> *"Write me a Fable 5 prompt for a long autonomous refactor."*

The skill fires on those phrasings. It's for prompting **Fable 5 / Mythos 5 specifically** — not other Claude models, and not general content writing.

## Install

See the [repo root](https://github.com/Amit-Tabibi/claude-code-skills) for install options. The quickest, inside Claude Code:

```
/plugin marketplace add Amit-Tabibi/claude-code-skills
/plugin install fable-prompt@amit-tabibi
```

## License

[MIT](../../LICENSE)
