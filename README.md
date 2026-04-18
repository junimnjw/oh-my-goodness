# oh-my-godness

Claude Code skills and agents by [@junimnjw](https://github.com/junimnjw).

🇰🇷 [한국어 README](README.ko.md)

## Contents

### Skills

- [`pre-commit-review`](skills/pre-commit-review) — Reviews staged changes (`git diff --staged`) for bugs and logic errors before you commit. Focused on Python and TypeScript, grouped by severity (Critical / Warning / Suggestion).

### Agents

- [`code-wiki-writer`](.claude/agents/code-wiki-writer.md) — Generates bilingual (Korean + English) onboarding documentation for a source folder. Writes sectioned Markdown into `docs/wiki/ko/` and `docs/wiki/en/`, picking sections based on project shape. Re-running reconciles the wiki against the current code: wrong claims get corrected, stale paths get updated.

## Requirements

- [Claude Code](https://docs.claude.com/en/docs/claude-code) (CLI, desktop, web, or IDE extension)

## Install — Skills

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

```bash
cd /path/to/your-project
mkdir -p .claude/skills

git clone https://github.com/junimnjw/oh-my-godness.git /tmp/oh-my-godness
cp -r /tmp/oh-my-godness/skills/pre-commit-review .claude/skills/

# Optional: commit it so your teammates get it too
git add .claude/skills/pre-commit-review
```

### Symlink option (auto-updates with `git pull`)

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/junimnjw/oh-my-godness.git ~/code/oh-my-godness

ln -s ~/code/oh-my-godness/skills/pre-commit-review ~/.claude/skills/pre-commit-review
```

### Verify

```bash
ls ~/.claude/skills/pre-commit-review/SKILL.md
```

If the file exists, try a trigger phrase in Claude Code like "review my staged changes". If Claude doesn't pick it up, restart Claude Code once (see heads-up above).

## Install — Agents

Agents follow the same two-location model, but this repo ships `code-wiki-writer` as a **project-level** agent. For project scope:

```bash
cd /path/to/your-project
mkdir -p .claude/agents

git clone https://github.com/junimnjw/oh-my-godness.git /tmp/oh-my-godness
cp /tmp/oh-my-godness/.claude/agents/code-wiki-writer.md .claude/agents/

# Share with your team
git add .claude/agents/code-wiki-writer.md
```

If you want the agent across all your projects, copy to `~/.claude/agents/` instead (same first-install restart caveat applies).

### Verify

```bash
ls .claude/agents/code-wiki-writer.md
```

Then in Claude Code:

```
> src/ 폴더 온보딩 문서 만들어줘
> write onboarding docs for ./packages/core
```

Claude will delegate to the agent, which writes pages under `docs/wiki/ko/` and `docs/wiki/en/`.

## Usage

### `pre-commit-review`

Stage some changes, then ask for a review:

```bash
git add .
# then in Claude Code:
# > review my staged changes
# > 커밋 전에 리뷰해줘
# > diff 리뷰해줘
```

Output: findings grouped as **Critical / Warning / Suggestion**, with a final verdict.

### `code-wiki-writer`

Point the agent at a source folder:

```
# in Claude Code:
> @code-wiki-writer for src/
> src/billing 폴더 온보딩 위키 만들어줘
> docs/wiki 내용이 오래됐어, 업데이트해줘
```

Output: sectioned Markdown in `docs/wiki/ko/` and `docs/wiki/en/`. Re-running on an existing wiki reconciles stale claims against the current code rather than overwriting the whole thing.

## Uninstall

```bash
# Skill
rm -rf ~/.claude/skills/pre-commit-review
rm -rf /path/to/your-project/.claude/skills/pre-commit-review

# Agent
rm /path/to/your-project/.claude/agents/code-wiki-writer.md
rm ~/.claude/agents/code-wiki-writer.md
```

## Contributing

Issues and PRs welcome. New skills go under `skills/<skill-name>/SKILL.md`; new agents go under `.claude/agents/<agent-name>.md`. See [CLAUDE.md](CLAUDE.md) for repo conventions.

## License

[MIT](LICENSE)
