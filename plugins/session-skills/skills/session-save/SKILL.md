---
name: session-save
description: Use when the user wants to safely close or clear a session and have the work PERSISTED INTO THE PROJECT'S OWN FILES so nothing is lost - phrases like "save before I clear", "save our decisions and changes", "make sure nothing's lost", "persist this into the repo", "I want to resume cold next week", "don't lose anything before I close". Routes decisions and shipped changes into the project's existing structure (changelog, ADR/decisions doc, tasks, docs - not a generic log file), is structure-aware, and asks before it writes. This is the DURABLE path - it writes to real files on disk. Do NOT use it when the user only wants a chat-only summary to paste into a fresh agent to keep working now, with nothing written to disk.
---

# Session Save

Persist what happened this session into the project itself, so the user can close or `/clear` and lose nothing - then resume days later, or start a fresh session, with the record already in the repo.

This is the **durable** counterpart to a chat-only handoff. A handoff is ephemeral: a summary you paste into a fresh agent to keep working *now*, gone the moment you lose the clipboard. Session-save is the opposite: it writes the important decisions and changes **into the project's real files**, where they survive. If the user only wants that chat-only summary and not a write to disk, this skill does not apply — don't write to their files.

**Goal: close safely, lose nothing.**

## When to invoke

The user says: "safely close this session", "I want to clear but save everything first", "save our decisions and changes", "make sure nothing's lost before I close", "wrap up so I can come back later", "finish session", or any near-equivalent. Also invoke proactively if the user says they're about to `/clear` or delete a session without having saved.

## Core principle - your judgment is the engine

The hard part of this skill is judgment: knowing **what mattered** this session and **where it belongs** in this specific project. That judgment is yours - the steps below are guardrails around it, not a replacement for it. Do not mechanically apply a fixed taxonomy; read the project and route intelligently, the way a careful teammate would.

## How to do it

1. **Survey the project structure FIRST.** Before deciding where anything goes, read the layout: top-level folders and any orienting docs (`README`, `CLAUDE.md`, an index, a changelog, an ADR/decisions doc, `tasks/`, `proposals/`, `docs/`, `snapshots/`, etc.). Infer what each place is *for* and the conventions it follows (file format, naming, how entries are structured). This survey is what lets the skill work in a project you don't already know - it is not optional.

2. **Synthesize THIS session.** From the conversation, pull: decisions made (and why), changes/work shipped (with paths), and open threads / next steps. Synthesize from session context - do NOT re-audit the filesystem or run `git log` to reconstruct what happened. If you didn't do it this session, it doesn't belong here.

3. **Route each item to its correct EXISTING home.** Map intelligently onto what the survey found: decisions → the decisions log / ADR if one exists; shipped changes → the changelog or snapshots; new work or follow-ups → tasks / proposals; durable notes → docs. Match each target file's existing format and style. Append; don't reformat or rewrite what's already there.

4. **Propose the save plan, then STOP for approval.** Present a concise plan: each target file → exactly what will be written to it. Then wait. Do NOT modify any file before the user says go - these are their real project files.

5. **Write on approval.** Append or update in place, following each file's existing conventions.

6. **Collect orphans, ask once at the end.** Anything that has no clear home in the project: gather all of them and ask the user **once**, at the end, where each should go (or whether to skip it). Never interrupt per item, and never silently drop something important.

7. **Empty or unstructured project.** If there's no structure to route into, do NOT invent one silently. Ask the user how they want to save (a single file? a new doc? where?) and follow their choice.

## Hard rules

1. **Fit the project; never impose.** Do not create new files or folders unless the user opts in. Route into what already exists.
2. **Never write before the plan is approved.** Show the plan, get the go, then write. Propose-then-write is the default; only skip the approval step if the user explicitly tells you to for this session.
3. **Match existing formats.** Append in the style of the target file. Don't overwrite, reorder, or reformat existing content.
4. **Nothing gets lost.** If an important decision or change has no obvious home, surface it in the orphan list - never quietly drop it. "Close safely, lose nothing" is the whole point.
5. **Synthesize, don't audit.** Reconstruct the session from context, not from a filesystem or git sweep.

## Anti-patterns - do not do these

- Dumping a generic `SESSION.md` / log file into a project that already has a proper place for decisions and changes.
- Creating `.claude/sessions/`, a `logs/` folder, or any new structure uninvited.
- Writing to files before the user has approved the plan.
- Reformatting, reordering, or overwriting existing file content instead of appending in its style.
- Interrupting once per unmapped item instead of collecting them and asking a single time at the end.
- Silently dropping an item because it didn't map cleanly.
- Rebuilding the session summary from `git log` / `Glob` instead of from what actually happened this session.
