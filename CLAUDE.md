# project_template

## What this is
A standalone project starter. To begin a new project: copy this whole repo to a new folder, ask the agent to read `_template/INIT-PROMPT.md` and run it, then push to a new git remote.

To bring an **existing project** up to the template standard: copy the `_template/` folder and `.claude/` contents into the existing repo, then ask the agent to read `_template/UPGRADE-PROMPT.md` and run the upgrade flow.

If you are reading this in a freshly copied template (before INIT runs), help the user follow `_template/INIT-PROMPT.md`. After INIT runs, the filled `_template/skeleton/CLAUDE.md.template` overwrites this file.

## Folder structure
```
project_template/
├── CLAUDE.md                       # this file (replaced by the project's CLAUDE.md after INIT runs)
├── _template/                      # bootstrap files (deleted after INIT runs)
│   ├── skeleton/
│   │   ├── CLAUDE.md.template      # Claude harness instructions file
│   │   └── .github/
│   │       ├── copilot-instructions.md.template  # Copilot harness instructions file
│   │       ├── instructions/       # Copilot scoped instruction files
│   │       └── prompts/            # Copilot reusable prompt files
├── runbooks/                       # starter operational procedures (kept)
├── reference/                      # starter examples and vendor docs (kept)
└── .claude/
    ├── skills/                     # starter skills (kept for Claude harness)
    └── agents/                     # starter subagents (kept for Claude harness)
```

## Token rules for this template
- This `CLAUDE.md` is always loaded. Keep it short. Detail goes in the root `README.md` or `_template/INIT-PROMPT.md`.
- `reference/` is examples-on-demand. Don't load files from it unless the user asks.
- `_template/skeleton/` is data, not instructions. Read it when running INIT, otherwise skip.

## Naming conventions
Pattern: `description_status.extension`. Markdown and HTML use `kebab-case`.

## Rules
- ASCII only in `.ps1` / `.psm1` / `.psd1`. PS 5.1 in CI mangles non-ASCII.
- INIT asks for the AI harness (Claude or Copilot). Claude creates `.claude/`; Copilot creates `.github/` with `copilot-instructions.md`, `instructions/`, and `prompts/`.
- When this repo is opened directly as the template, **never `git add` / `git commit` / `git push`** - the maintainer pushes template changes manually. Once copied into a new project, normal git rules apply.
- `reference/` is read-only examples. Never edit files there to fix a bug - rebuild cleanly in a project context.

## Skills (`.claude/skills/`)
| Skill | Activates on |
|-------|--------------|
| `planning-docs` | Authoring or reviewing ADRs and technical specs using the templates in `_template/skeleton/planning/templates/` |

## Subagents (`.claude/agents/`)
| Agent | Purpose |
|-------|---------|
| `powershell-reviewer` | Read-only review of PowerShell scripts/modules |
| `terraform-reviewer` | Read-only review of Terraform/OpenTofu code |
| `Explore` (built-in, Claude Code CLI only) | Read-only codebase Q&A; `quick` / `medium` / `thorough` |

## Bootstrapping a new project
See [`README.md`](README.md) for the quick-start steps. Short version: copy the repo, remove `.git`, ask the agent to follow `_template/INIT-PROMPT.md`, then `git init` and push.

## Upgrading an existing project
Copy the `_template/` folder and `.claude/` contents into the existing repo, then ask the agent to follow `_template/UPGRADE-PROMPT.md`. The upgrade flow is additive — it only adds missing agents, skills, settings, and CONTEXT.md files.
