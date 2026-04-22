# oh-my-goodness

Claude Code skills and agents by [@junimnjw](https://github.com/junimnjw).

🇰🇷 [한국어 README](README.ko.md)

## Contents

### Skills

- [`pre-commit-review`](skills/pre-commit-review) — Reviews staged changes (`git diff --staged`) for bugs and logic errors before you commit. Focused on Python and TypeScript, grouped by severity (Critical / Warning / Suggestion).

### Agents

- [`code-wiki-writer`](.claude/agents/code-wiki-writer.md) — Generates bilingual (Korean + English) onboarding documentation for a source folder. Writes sectioned Markdown into `docs/wiki/ko/` and `docs/wiki/en/`, picking sections based on project shape. Re-running reconciles the wiki against the current code: wrong claims get corrected, stale paths get updated.
- [`weekly-report-writer`](.claude/agents/weekly-report-writer.md) — Turns mixed evidence such as lab updates, notes, PR titles with links, source folders, and KPI tables into a concise management-grade weekly report. It can also work interview-style: asking one focused question at a time, collecting missing details progressively, then synthesizing them into a final report. When the user does not specify a week, it defaults to the current workweek based on the run date, and it can report operational metrics such as active sessions as well as user counts.

## Requirements

- [Claude Code](https://docs.claude.com/en/docs/claude-code) (CLI, desktop, web, or IDE extension)

## Quick install

Copy-paste one block. Each block is self-contained: clones, installs, cleans up. Run from the target project directory for the "project" variants.

```bash
# pre-commit-review — personal (available in every project)
TMP=$(mktemp -d) && \
  git clone --depth 1 https://github.com/junimnjw/oh-my-goodness.git "$TMP" && \
  mkdir -p ~/.claude/skills && \
  cp -r "$TMP/skills/pre-commit-review" ~/.claude/skills/ && \
  rm -rf "$TMP"
```

```bash
# pre-commit-review — project (this repo only; run from the project root)
TMP=$(mktemp -d) && \
  git clone --depth 1 https://github.com/junimnjw/oh-my-goodness.git "$TMP" && \
  mkdir -p .claude/skills && \
  cp -r "$TMP/skills/pre-commit-review" .claude/skills/ && \
  rm -rf "$TMP"
```

```bash
# code-wiki-writer — project (run from the project root)
TMP=$(mktemp -d) && \
  git clone --depth 1 https://github.com/junimnjw/oh-my-goodness.git "$TMP" && \
  mkdir -p .claude/agents && \
  cp "$TMP/.claude/agents/code-wiki-writer.md" .claude/agents/ && \
  rm -rf "$TMP"
```

```bash
# weekly-report-writer — project (run from the project root)
TMP=$(mktemp -d) && \
  git clone --depth 1 https://github.com/junimnjw/oh-my-goodness.git "$TMP" && \
  mkdir -p .claude/agents && \
  cp "$TMP/.claude/agents/weekly-report-writer.md" .claude/agents/ && \
  rm -rf "$TMP"
```

> If `~/.claude/skills/` or `~/.claude/agents/` didn't exist before, **restart Claude Code once** after the install so it starts watching the new directory.

Want to symlink instead of copy (for `git pull` auto-updates), or understand the priority between personal and project scope? See the detailed sections below.

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
git clone https://github.com/junimnjw/oh-my-goodness.git /tmp/oh-my-goodness

# 3. Copy just the skill you want
cp -r /tmp/oh-my-goodness/skills/pre-commit-review ~/.claude/skills/
```

> **Heads-up on first install:** if `~/.claude/skills/` didn't exist when your Claude Code session started, you'll need to **restart Claude Code** once before it starts watching the directory. After that, adding/editing/removing skills takes effect live in the current session.

### Project install (scoped to one repo)

```bash
cd /path/to/your-project
mkdir -p .claude/skills

git clone https://github.com/junimnjw/oh-my-goodness.git /tmp/oh-my-goodness
cp -r /tmp/oh-my-goodness/skills/pre-commit-review .claude/skills/

# Optional: commit it so your teammates get it too
git add .claude/skills/pre-commit-review
```

### Symlink option (auto-updates with `git pull`)

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/junimnjw/oh-my-goodness.git ~/code/oh-my-goodness

ln -s ~/code/oh-my-goodness/skills/pre-commit-review ~/.claude/skills/pre-commit-review
```

### Verify

```bash
ls ~/.claude/skills/pre-commit-review/SKILL.md
```

If the file exists, try a trigger phrase in Claude Code like "review my staged changes". If Claude doesn't pick it up, restart Claude Code once (see heads-up above).

## Install — Agents

Agents follow the same two-location model, and this repo currently ships `code-wiki-writer` and `weekly-report-writer` as **project-level** agents. For project scope:

```bash
cd /path/to/your-project
mkdir -p .claude/agents

git clone https://github.com/junimnjw/oh-my-goodness.git /tmp/oh-my-goodness
cp /tmp/oh-my-goodness/.claude/agents/code-wiki-writer.md .claude/agents/
cp /tmp/oh-my-goodness/.claude/agents/weekly-report-writer.md .claude/agents/

# Share with your team
git add .claude/agents/code-wiki-writer.md .claude/agents/weekly-report-writer.md
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

### `weekly-report-writer`

Pass the reporting window plus whatever evidence you have:

```
# in Claude Code:
> @weekly-report-writer summarize this week's progress from this repo
> 이번 주 작업 내역 바탕으로 팀 공유용 주간 보고 써줘
> 지난주 커밋 + 메모 보고 매니저용 업데이트 만들어줘
> 해외 연구소 메일, PR 링크, 지표 테이블 기준으로 임원 보고용 주간 보고 정리해줘
```

Output: a concise report body that can be pasted into Confluence, an internal discussion, or a PR description. Metrics stay inside each workstream section at the top, values are preserved exactly, and one representative PR link can be appended to each relevant bullet.

If you do not have the full material ready, you can also let it interview you step by step instead of pasting everything at once.

If you do not specify the reporting window, the agent defaults to the current workweek based on the run date, using Monday through Friday in the user's local timezone.

By default, the narrative covers the current workweek, while KPI tables use the previous completed week (`W-1`) unless the user specifies a different metric window.

If your source materials use broader program names than the report should show, the agent can preserve your preferred display names in the final report.

The agent also favors readability over implementation-heavy phrasing. If a sentence becomes too dense, it should lead with the business or operational outcome and move technical detail later in the line.

## Uninstall

```bash
# Skill
rm -rf ~/.claude/skills/pre-commit-review
rm -rf /path/to/your-project/.claude/skills/pre-commit-review

# Agent
rm /path/to/your-project/.claude/agents/code-wiki-writer.md
rm /path/to/your-project/.claude/agents/weekly-report-writer.md
rm ~/.claude/agents/code-wiki-writer.md
rm ~/.claude/agents/weekly-report-writer.md
```

## Contributing

Issues and PRs welcome. New skills go under `skills/<skill-name>/SKILL.md`; new agents go under `.claude/agents/<agent-name>.md`. See [CLAUDE.md](CLAUDE.md) for repo conventions.

## License

[MIT](LICENSE)
