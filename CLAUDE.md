# CLAUDE.md

This file gives Claude Code context when working inside this repository.

## What this repo is

A collection of Claude Code **skills** published for public use. Each skill is a self-contained directory under `skills/` that Claude Code can pick up and trigger. This repo is not a skill itself — it is a container for multiple skills plus install/usage docs.

## Layout

```
.
├── CLAUDE.md                  # this file
├── README.md                  # install guide (English)
├── README.ko.md               # install guide (Korean)
├── LICENSE                    # MIT
└── skills/
    └── <skill-name>/
        ├── SKILL.md           # required — frontmatter + instructions
        ├── evals/             # optional — test prompts and assertions
        ├── scripts/           # optional — bundled executable scripts
        ├── references/        # optional — reference docs loaded on demand
        └── assets/            # optional — templates, icons, fonts
```

`SKILL.md` is the only required file in a skill. Everything else is optional and only used if the skill needs it.

## Conventions

### Skill naming

Use `kebab-case` for the skill directory and the `name` frontmatter field, and keep them identical. Prefer a verb-phrase or concrete noun phrase (`pre-commit-review`, not `reviewer`).

### `SKILL.md` frontmatter

Every `SKILL.md` must start with a YAML block:

```markdown
---
name: skill-name
description: One-paragraph description. Include both what the skill does AND when to use it (trigger phrases, contexts). This is the primary triggering mechanism, so be specific. Claude tends to under-trigger skills, so lean slightly pushy — list concrete user phrases that should invoke the skill.
---
```

Only `name` and `description` are required. The description doubles as the triggering signal; write it so Claude can tell from the description alone when the skill applies.

### SKILL.md body

Keep it under ~500 lines. If it grows beyond that, split domain-specific material into `references/` and point to the files from `SKILL.md` with explicit guidance on when to read each one (progressive disclosure). Explain *why* instructions exist, not just *what* — today's models do better with reasoning than with rigid rules. Avoid heavy-handed ALWAYS/NEVER unless the cost of getting it wrong is high.

### Bilingual triggering

Descriptions and example trigger phrases should cover English **and** Korean users, since the author and much of the audience are bilingual. Include realistic Korean phrases in the `description` field (not only in the body) — Claude uses the description to decide whether to load the skill.

## Adding a new skill

1. Create `skills/<new-skill>/SKILL.md` with valid frontmatter.
2. Put any bundled scripts, references, or assets in the matching subdirectory.
3. Add the skill to the **Skills** list in both `README.md` and `README.ko.md` (short one-paragraph blurb + relative link).
4. If the skill has test cases, drop them in `skills/<new-skill>/evals/evals.json` using the skill-creator schema (prompt + assertions). Optional but useful for reviewers.
5. Open a PR.

## Testing a skill locally

There is no per-repo test harness. To try a skill against a real Claude Code session:

```bash
# Install it into your personal skills dir (creates a live symlink)
ln -s "$(pwd)/skills/<skill-name>" ~/.claude/skills/<skill-name>

# Restart Claude Code if ~/.claude/skills/ didn't exist before
# Then trigger the skill with a realistic prompt
```

For more rigorous evaluation (multiple test cases, with/without-skill comparison, pass-rate stats), the `skill-creator` skill in Anthropic's plugin suite can drive the full loop — see its documentation.

## When modifying existing skills

- Preserve the skill directory name and `name` frontmatter field. Both are used as stable identifiers by users who already installed the skill.
- Keep the output format stable across revisions when possible — users and other skills/tools may depend on it.
- If a change is breaking, mention it in the commit message so it surfaces in release notes.

## Out of scope

- No CI/CD in this repo. Skills are plain files; there's nothing to build.
- Do not commit auto-generated workspace directories (evaluation runs, feedback files, etc.). If a skill-creator run produces a `<skill>-workspace/`, keep it outside the repo.
