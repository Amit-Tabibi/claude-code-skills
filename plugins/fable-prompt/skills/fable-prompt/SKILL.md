---
name: fable-prompt
description: Use when the user needs a prompt written FOR Claude Fable 5 (or Mythos 5) — triggers on "I need a Fable 5 prompt", "prompt for Fable 5", "write me a Fable 5 prompt", "turn this into a Fable 5 prompt", "make a prompt for Fable". Takes a rough ask and returns a finished, Fable-5-optimized prompt to paste. NOT for prompting other Claude models or for general content writing.
---

## What this skill does

The user gives a rough ask ("I need a prompt that does X"). This skill turns it into a **finished, Fable-5-optimized prompt** they can paste straight into a Fable 5 / Mythos 5 session, plus a one-line note on which `effort` level to run it at.

The whole point: **Fable 5 is smart and obedient enough that over-specified prompts HURT it.** This skill enforces the opposite of the old instinct — give direction and intent, then trust the model. Every prompt it produces is deliberately lean.

Reply in the user's language; the produced prompt itself is in whatever language its target needs (the user's language for content, usually English for code/agents).

## Source of truth

Everything here is distilled from Anthropic's official guide: *Prompting Claude Fable 5* (platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5). The reusable instruction snippets below are quoted/adapted from it — they're battle-tested, prefer them over inventing new phrasing.

## The 7 principles (what makes a prompt "Fable 5 good")

1. **Give the reason, not just the request.** Intent lets Fable 5 connect the task to the right context instead of guessing. This is the highest-leverage move. Pattern: *"I'm working on [larger task] for [who]. They need [what the output enables]. With that in mind: [request]."*
2. **Short instruction beats an enumerated list.** One brevity/boundary sentence steers as well as ten "don't do X" rules. Longer ≠ better; it degrades output.
3. **State the boundaries.** Say explicitly what to do and what NOT to do, and when to stop — Fable 5 can otherwise take unrequested actions (fix a thing you only asked about, make backups).
4. **Don't over-plan / don't over-build.** On ambiguous or routine tasks at high effort it can survey options and tidy things it wasn't asked to. Add a "when you have enough info, act" or "don't refactor beyond the task" line when relevant.
5. **Ground progress claims (long runs only).** For multi-step/autonomous work, tell it to audit each claim against a real tool result — kills fabricated status reports.
6. **Aim high.** Fable 5 shines on hard, long, ambiguous work. Let it scope, ask clarifying questions, and execute end-to-end rather than hand-holding.
7. **Never ask it to reproduce its reasoning in the response.** "Explain your thinking / show your work" as response text can trigger a refusal. If reasoning visibility is wanted, it comes from thinking blocks, not the prompt.

## Canonical snippets (drop in verbatim when the situation calls for it)

Only include the ones the task actually needs — don't bolt on all of them.

- **Don't over-plan (ambiguous task):**
  > When you have enough information to act, act. Don't re-derive facts already established, re-litigate a settled decision, or narrate options you won't pursue. If weighing a choice, give a recommendation, not a survey.

- **Lead with the outcome (any deliverable):**
  > Lead with the outcome. Your first sentence answers "what happened" or "what did you find." Supporting detail comes after. Readability matters more than compression — don't shrink into fragments, abbreviations, or arrow chains.

- **Don't over-build (code/build tasks at high effort):**
  > Don't add features, refactor, or introduce abstractions beyond what the task requires. Do the simplest thing that works. Don't add error handling for scenarios that can't happen; only validate at real system boundaries.

- **Boundaries / stop-and-report (analysis or "I'm thinking out loud"):**
  > When I'm describing a problem or asking a question rather than requesting a change, the deliverable is your assessment. Report your findings and stop. Don't apply a fix until I ask.

- **Ground progress (long/autonomous runs):**
  > Before reporting progress, audit each claim against a tool result from this session. Only report work you can point to evidence for; if something isn't verified, say so. If tests fail, say so with the output.

- **Checkpoints (long workflows):**
  > Pause for me only when the work genuinely requires it: a destructive/irreversible action, a real scope change, or input only I can provide. Otherwise proceed.

- **Self-verification (long build):**
  > Establish a method to check your own work as you build. Every [interval], verify against the spec — prefer a fresh-context subagent over self-critique.

- **Autonomous / not-watching:**
  > You're operating autonomously; I'm not watching in real time. For reversible actions that follow from the request, proceed without asking. Don't end a turn on a promise ("I'll…") — do the work now.

## Execution

### Step 1 — Read the ask, decide if you have enough

Extract three things from the user's request:
- **Task** — what the prompt should make Fable 5 do.
- **Intent** — why / who it's for / what the output enables. (If missing, this is the #1 thing to get — principle 1.)
- **Shape** — one-shot vs. long autonomous run? code, content, analysis, agent/subagents? output language + format?

If Task + Intent are clear enough to write a strong prompt, **write it — don't interrogate.** Only ask when a genuine gap would change the prompt. Cap at 2–3 crisp questions, and offer a sensible default with each so the user can just nod.

### Step 2 — Build the prompt

Assemble in this order:
1. **Context + intent line** (principle 1) — one or two sentences: the larger goal, who it's for, what it enables.
2. **The actual request** — concrete and direct.
3. **Only the snippets this task needs** — pick from the canonical list by shape:
   - Quick/one-shot content → intent line + request + maybe "lead with outcome." That's it. Resist adding more.
   - Analysis / "look at this" → add the boundaries/stop-and-report snippet.
   - Code/build → add don't-over-build.
   - Long or autonomous → add ground-progress + checkpoints (+ self-verification / autonomous as fit).
4. **Constraints/format** — output language, length, structure, must/must-nots.

Keep it lean. If you're tempted to add a fourth or fifth instruction, re-check whether the shorter version already covers it (principle 2). **The prompt should look almost too short.**

Never include: "explain your reasoning," "show your thinking," "walk me through your logic" in the response body (principle 7).

### Step 3 — Deliver

Return, in chat:
1. The finished prompt in a single copy-paste code block.
2. **One line: recommended `effort`** — `high` default; `xhigh` for the hardest/most capability-sensitive; `medium`/`low` for routine. (Fable 5's low often beats prior models' xhigh.)
3. *(optional, one line)* what you deliberately left OUT and why — reinforces the lean philosophy so the user internalizes it.

Then stop. Don't over-explain the prompt you just wrote.

## Anti-patterns (don't produce these)

- Ten bullet "rules" the model must follow → collapse to one sentence.
- Enumerating every edge case → state the principle, trust the model.
- "First, explain your approach, then…" reasoning-echo scaffolding → refusal risk.
- Defensive "if X fails, then Y, and also handle Z" for things that can't happen.
- Padding a simple content ask with autonomous-agent machinery it'll never use.
