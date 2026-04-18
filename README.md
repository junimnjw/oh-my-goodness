# oh-my-godness

Claude Code skills by [@junimnjw](https://github.com/junimnjw).

🇰🇷 [한국어 README](README.ko.md)

## Skills

### [`pre-commit-review`](skills/pre-commit-review)

Reviews your staged changes (`git diff --staged`) for **bugs and logic errors** before you commit. Focused on Python and TypeScript. Emphasizes catching real bugs over style nits, and outputs findings grouped by severity (Critical / Warning / Suggestion).

## Requirements

- [Claude Code](https://docs.claude.com/en/docs/claude-code) (CLI, desktop, web, or IDE extension)

## Install

Claude Code discovers skills in two places, in priority order:

- **Personal** (`~/.claude/skills/`) — available across all your projects
- **Project** (`<project>/.claude/skills/`) — scoped to one project, overrides personal with the same name

Both work the same way: drop a directory containing a `SKILL.md` and Claude Code picks it up. No registration step.

### Personal install (recommended for most people)

```bash
# 1. Create the directory if it doesn't exist yet
mkdir -p ~/.claude/skills

# 2. Clone this repo anywhere (e.g. a workspace)
git clone https://github.com/junimnjw/oh-my-godness.git /tmp/oh-my-godness

# 3. Copy just the skill you want
cp -r /tmp/oh-my-godness/skills/pre-commit-review ~/.claude/skills/
```

> **Heads-up on first install:** if `~/.claude/skills/` didn't exist when your Claude Code session started, you'll need to **restart Claude Code** once before it starts watching the directory. After that, adding/editing/removing skills takes effect live in the current session.

### Project install (scoped to one repo)

If you want the skill only inside a specific project:

```bash
cd /path/to/your-project
mkdir -p .claude/skills

git clone https://github.com/junimnjw/oh-my-godness.git /tmp/oh-my-godness
cp -r /tmp/oh-my-godness/skills/pre-commit-review .claude/skills/

# Optional: commit it so your teammates get it too
git add .claude/skills/pre-commit-review
```

### Symlink option (auto-updates with `git pull`)

If you want updates whenever you `git pull`, symlink instead of copying:

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/junimnjw/oh-my-godness.git ~/code/oh-my-godness

ln -s ~/code/oh-my-godness/skills/pre-commit-review ~/.claude/skills/pre-commit-review
```

### Verify the install

```bash
ls ~/.claude/skills/pre-commit-review/SKILL.md
```

If the file exists, try a trigger phrase in Claude Code like "review my staged changes". If Claude doesn't pick it up, restart Claude Code once (see the heads-up above).

## Usage

Stage some changes, then ask Claude Code to review:

```bash
git add .
# then in Claude Code:
# > review my staged changes
# > 커밋 전에 리뷰해줘
# > diff 리뷰해줘
```

Claude will run `git diff --staged`, look at the relevant files, and output findings grouped as **Critical / Warning / Suggestion**, ending with a verdict on whether it's safe to commit.

## Uninstall

```bash
rm -rf ~/.claude/skills/pre-commit-review
# or for project-level:
rm -rf /path/to/your-project/.claude/skills/pre-commit-review
```

## Contributing

Issues and PRs welcome. If you're adding a new skill, put it under `skills/<skill-name>/` with a `SKILL.md` at the root. See [CLAUDE.md](CLAUDE.md) for the repo's conventions.

## License

[MIT](LICENSE)
