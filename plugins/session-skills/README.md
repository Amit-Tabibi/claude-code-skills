# Claude Code Session Skills

Two complementary [Claude Code](https://claude.com/claude-code) skills for ending a working session cleanly — so you can `/clear`, close your laptop, or come back next week without losing the thread.

They solve the same problem from opposite ends:

| Skill | Path | What it does | Writes to disk? |
|-------|------|--------------|-----------------|
| **session-handoff** | ephemeral | Prints a structured handoff *in chat* so a fresh agent can pick up exactly where you left off after `/clear`. | No — chat only |
| **session-save** | durable | Persists this session's decisions and shipped work *into your project's own files* (changelog, ADR, tasks, docs), routed to where they belong. | Yes — after you approve a plan |

The split is deliberate. A handoff is for continuing **now** in a new context window; you paste it forward and it's gone when the clipboard clears. A save is for stopping **for real**; the record lands in the repo where it survives. Each skill knows about the other and will point you to it if you reach for the wrong one.

## Why these exist

Long Claude Code sessions accumulate state that lives nowhere but the conversation: decisions and their rationale, which files changed and why, background processes still running, what got deferred, the open questions. Hit `/clear` and it's all gone. These skills capture that state on purpose — one into your next context, one into your project.

## session-handoff

Produces a fixed-structure summary aimed at a *future instance of the agent*, not at a human stakeholder. Sections are stable every time (Decisions + what shipped, Key files, Running state, Verification, Deferred + open questions, Pick up here) so the next agent always knows where to look.

Key behaviors:
- **Chat only.** Never writes a file, never touches memory.
- **Synthesizes the session** rather than re-auditing the filesystem — no `git log`, no broad globbing.
- **Surfaces load-bearing runtime state**: background shell IDs (with kill commands), dev server ports, open worktrees.
- **Absolute paths always**, since the next agent may have a different working directory.

Trigger it with phrases like *"session handoff"*, *"hand off"*, *"summarize before I clear"*, *"brief the next agent"*.

## session-save

Persists the session into the project's **existing** structure instead of dropping a generic log file. It surveys the repo first, infers what each location is for, routes each item to its correct home, then **proposes a plan and waits for your approval before writing anything.**

Key behaviors:
- **Structure-aware.** Reads your README / CLAUDE.md / changelog / ADR / `tasks/` etc. and matches each file's existing format.
- **Propose-then-write.** Shows you exactly what goes where, then stops. Nothing is written until you say go.
- **Loses nothing.** Anything without an obvious home is collected into an orphan list and you're asked once, at the end.
- **Never imposes structure.** Won't create new folders or a `SESSION.md` uninvited; if the project has no structure to route into, it asks you how you want to save.

Trigger it with phrases like *"save before I clear"*, *"persist this into the repo"*, *"make sure nothing's lost"*, *"I want to resume cold next week"*.

## Install

### Option 1 — Plugin (recommended, no terminal needed)

Inside Claude Code, type these two commands:

```
/plugin marketplace add Amit-Tabibi/claude-code-skills
/plugin install session-skills@amit-tabibi
```

That's it — no git, no symlinks, no files to move. The skills install instantly and you can enable/disable or update them anytime from `/plugin`.

### Option 2 — Just ask Claude to install them

Open Claude Code in any folder and paste:

> Install the skills from https://github.com/Amit-Tabibi/claude-code-skills into my `~/.claude/skills` folder.

Claude clones the repo and copies the skills for you.

### Option 3 — Manual (for developers who want `git pull` to auto-update)

Skills live in `~/.claude/skills/`. Clone and symlink so a `git pull` keeps them current:

```bash
git clone https://github.com/Amit-Tabibi/claude-code-skills.git
cd claude-code-skills
mkdir -p ~/.claude/skills
ln -s "$PWD/plugins/session-skills/skills/session-handoff" ~/.claude/skills/session-handoff
ln -s "$PWD/plugins/session-skills/skills/session-save"    ~/.claude/skills/session-save
```

Or just copy them in (no symlink, no auto-update):

```bash
mkdir -p ~/.claude/skills
cp -R plugins/session-skills/skills/session-handoff plugins/session-skills/skills/session-save ~/.claude/skills/
```

Either way, verify they loaded by asking for a *"session handoff"* — the `session-handoff` skill should trigger.

## Notes

- **No configuration, no dependencies.** Each skill is a single `SKILL.md`. They invoke no external tools and store no secrets.
- `session-handoff` mentions `~/.claude/plans/` and `~/.claude/projects/<project>/memory/` as places to pull state from. These are standard Claude Code paths; if they don't exist on your machine the skill simply finds nothing there and moves on — harmless.
- These are skills, not slash commands you run manually — Claude Code invokes them automatically when your request matches their trigger phrases.

## License

[MIT](LICENSE)
