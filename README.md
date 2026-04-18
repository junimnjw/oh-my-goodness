# oh-my-godness

Claude Code skills by [@junimnjw](https://github.com/junimnjw).

🇰🇷 [한국어 README](README.ko.md)

## Skills

### [`pre-commit-review`](skills/pre-commit-review)

Reviews your staged changes (`git diff --staged`) for **bugs and logic errors** before you commit. Focused on Python and TypeScript. Emphasizes catching real bugs over style nits, and outputs findings grouped by severity (Critical / Warning / Suggestion).

## Requirements

- [Claude Code](https://docs.claude.com/en/docs/claude-code) (CLI, desktop, web, or IDE extension)

## Install

Claude Code loads skills from `~/.claude/skills/`. Any directory under there that contains a `SKILL.md` file becomes a skill Claude can trigger.

### Option A: Install a single skill (recommended)

Pick one skill and drop it into your skills directory:

```bash
# Make sure the skills directory exists
mkdir -p ~/.claude/skills

# Clone this repo anywhere you like (e.g. a workspace)
git clone https://github.com/junimnjw/oh-my-godness.git /tmp/oh-my-godness

# Copy just the skill you want
cp -r /tmp/oh-my-godness/skills/pre-commit-review ~/.claude/skills/
```

### Option B: Symlink (auto-updates with `git pull`)

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

If the file exists, Claude Code will pick up the skill on its next session. Start a new Claude Code session and try a trigger phrase like "review my staged changes".

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
```

## Contributing

Issues and PRs welcome. If you're adding a new skill, put it under `skills/<skill-name>/` with a `SKILL.md` at the root.

## License

[MIT](LICENSE)
