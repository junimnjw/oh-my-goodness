# CLAUDE.md

This file gives Claude Code context when working inside this repository.

## What this repo is

A collection of Claude Code **skills** and **agents** published for public use.

- A **skill** is a self-contained directory that Claude can trigger on demand to perform a specialized task (e.g., reviewing staged changes).
- An **agent** is a Markdown file defining a subagent the main Claude session can delegate to (e.g., writing a documentation wiki for a package).

This repo is a container for multiple skills and agents plus the docs that explain how to install and use them. It is not itself a skill or an agent.

## Layout

```
.
├── CLAUDE.md                            # this file
├── README.md                            # install guide (English)
├── README.ko.md                         # install guide (Korean)
├── LICENSE                              # MIT
├── skills/
│   └── <skill-name>/
│       ├── SKILL.md                     # required — frontmatter + instructions
│       ├── evals/                       # optional — test prompts and assertions
│       ├── scripts/                     # optional — bundled executable scripts
│       ├── references/                  # optional — reference docs loaded on demand
│       └── assets/                      # optional — templates, icons, fonts
└── .claude/
    └── agents/
        └── <agent-name>.md              # agent definition (frontmatter + system prompt)
```

- `SKILL.md` is the only required file in a skill.
- `<agent-name>.md` is the entire agent — agents are single-file by design.

## Conventions — Skills

### Naming

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

### `SKILL.md` body

Keep it under ~500 lines. If it grows beyond that, split domain-specific material into `references/` and point to the files from `SKILL.md` with explicit guidance on when to read each one (progressive disclosure). Explain *why* instructions exist, not just *what* — today's models do better with reasoning than with rigid rules. Avoid heavy-handed ALWAYS/NEVER unless the cost of getting it wrong is high.

## Conventions — Agents

### Naming

Same as skills: `kebab-case`, identical between filename (without the `.md` extension) and the `name` frontmatter field.

### Agent frontmatter

Every agent file must start with a YAML block:

```markdown
---
name: agent-name
description: When to delegate to this agent. Include trigger phrases and the shape of tasks it handles. Same pushy-enough-to-trigger principle as skills.
tools: Read, Glob, Grep, Write
---
```

Required fields:

- `name` — unique identifier
- `description` — delegation trigger
- `tools` — comma-separated list of tool names the agent may use. **Grant only the tools the agent genuinely needs to do its job** — not "everything in case it helps." Read-heavy agents don't need Write; exploration agents don't need Bash unless they actually shell out.

Optional:

- `model` — override the default model for this agent (usually unnecessary)

### Agent body

The body is the agent's system prompt. Treat it like a focused job description:

- Open with **what the agent does** in one or two sentences.
- Define the **input** (what the main session passes in) and the **output** (what deliverable the agent produces).
- Provide a **workflow** — numbered steps, concrete enough to follow but not so rigid the agent can't adapt.
- List **what NOT to do** — common failure modes, hallucination traps, things a less-careful model would do that waste tokens.
- Keep it under ~300 lines. Long agent prompts get ignored in the middle.

Agents have no bundled resources directory — everything the agent needs must be in the Markdown file itself or reachable through its granted tools.

## Bilingual triggering

Descriptions and example trigger phrases should cover **English and Korean** users, since the author and much of the audience are bilingual. Include realistic Korean phrases in the `description` field (not only in the body) — Claude uses the description to decide whether to load the skill or delegate to the agent.

## Adding a new skill or agent

1. Create the file(s):
   - Skill: `skills/<new-skill>/SKILL.md` with valid frontmatter.
   - Agent: `.claude/agents/<new-agent>.md` with valid frontmatter.
2. Put any bundled scripts, references, or assets in the matching subdirectory (skills only).
3. Add the entry to the **Contents** list in both `README.md` and `README.ko.md` (short one-paragraph blurb + relative link).
4. Add an **Install** block if it's a different install path than existing entries (most won't need this — the existing install section covers `skills/` and `.claude/agents/` uniformly).
5. If the skill has test cases, drop them in `skills/<new-skill>/evals/evals.json` using the skill-creator schema (prompt + assertions). Optional but useful for reviewers.
6. Open a PR.

## Testing locally

No per-repo test harness. To try a skill or agent against a real Claude Code session:

```bash
# Skill — install as a live symlink into your personal skills dir
ln -s "$(pwd)/skills/<skill-name>" ~/.claude/skills/<skill-name>

# Agent — copy into the project's own agents dir (or your personal one)
cp .claude/agents/<agent-name>.md ~/.claude/agents/

# Restart Claude Code if ~/.claude/{skills,agents}/ didn't exist before.
# Then trigger with a realistic prompt from the description.
```

For more rigorous skill evaluation (multiple test cases, with/without-skill comparison, pass-rate stats), the `skill-creator` skill in Anthropic's plugin suite can drive the full loop.

## When modifying existing skills or agents

- Preserve the file/directory name and `name` frontmatter field. Both are used as stable identifiers by users who already installed them.
- Keep the output format stable across revisions when possible — users and other tooling may depend on it.
- If a change is breaking, mention it in the commit message so it surfaces in release notes.

## Out of scope

- No CI/CD in this repo. Skills and agents are plain files; there's nothing to build.
- Do not commit auto-generated workspace directories (evaluation runs, feedback files, etc.). If a skill-creator run produces a `<skill>-workspace/`, keep it outside the repo.
