# Amit Tabibi's Claude Code Skills

A growing collection of [Claude Code](https://claude.com/claude-code) skills I've built and use daily. It's a plugin marketplace: **each plugin installs on its own**, so you can take just the one you want.

## What's inside

| Plugin | Skills | What it's for |
|--------|--------|---------------|
| **[session-skills](plugins/session-skills)** | `session-handoff`, `session-save` | Ending a working session cleanly — hand off to a fresh context, or persist the session's decisions and work into your project's own files. |
| **[fable-prompt](plugins/fable-prompt)** | `fable-prompt` | Turn a rough ask into a finished, lean prompt optimized for Claude Fable 5 / Mythos 5. |

Each plugin has its own README with the full story.

## Install

### Option 1 — Plugin (recommended, no terminal needed)

Inside Claude Code, add the marketplace once, then install whichever plugins you want:

```
/plugin marketplace add Amit-Tabibi/claude-code-skills
/plugin install session-skills@amit-tabibi
/plugin install fable-prompt@amit-tabibi
```

No git, no symlinks, no files to move. Enable/disable or update anytime from `/plugin`.

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
# take only what you want:
ln -s "$PWD/plugins/session-skills/skills/session-handoff" ~/.claude/skills/session-handoff
ln -s "$PWD/plugins/session-skills/skills/session-save"    ~/.claude/skills/session-save
ln -s "$PWD/plugins/fable-prompt/skills/fable-prompt"      ~/.claude/skills/fable-prompt
```

Or copy them in (no symlink, no auto-update):

```bash
mkdir -p ~/.claude/skills
cp -R plugins/*/skills/* ~/.claude/skills/
```

## Notes

- **No configuration, no dependencies.** Each skill is a single `SKILL.md`. They invoke no external tools and store no secrets.
- These are skills, not slash commands you run manually — Claude Code invokes them automatically when your request matches their trigger phrases.
- Repo layout: every plugin lives under `plugins/<name>/` with its own `.claude-plugin/plugin.json`, README, and `skills/`. The marketplace manifest at `.claude-plugin/marketplace.json` lists them all.

## License

[MIT](LICENSE)
