# public-project-template

A standalone starter for a single project. Copy this repo to a new folder, point your agent at [`_template/INIT-PROMPT.md`](_template/INIT-PROMPT.md), and it becomes the project. No copy-paste — the agent reads the file directly from the workspace.

> This README is what you see *before* bootstrapping. Once the INIT flow runs, it gets overwritten by the project's own README.

## Model

One repo per project. Everything the project needs - planning, rooms, runbooks, reference, skills, agents - lives in the same repo. No multi-root, no sibling-folder setup, no shared knowledge base to mount. If you build a skill or agent worth reusing, copy it into the next project by hand when you start it.

## Two ways to use this template

| Goal | Flow |
|------|------|
| Start a brand-new project | Follow **Quick start** below — copy the template, run INIT |
| Bring an existing project up to the standard | Follow **Upgrade an existing project** below — drop the template files in, run UPGRADE |

---

## Quick start — new project (AI-assisted)

1. **Copy this repo to a new folder.** Easiest:
   ```powershell
   git clone <this-template-url> <new-project>
   Remove-Item "<new-project>\.git" -Recurse -Force
   ```
   Removing `.git` detaches the new folder from the template's history so you start clean.
2. **Open the new folder in VS Code.**
3. **Point the agent at the prompt.** In a chat opened in the new folder (Claude Code CLI or GitHub Copilot agent mode), ask it to follow `_template/INIT-PROMPT.md` (e.g. `Read _template/INIT-PROMPT.md and run it`). No need to paste the contents - the file is right there in the workspace. The agent will:
   - ask for project name, one-liner, and goals
   - propose slug, tech stack, rooms, commands, and an avoid-list for you to confirm
   - fill every `*.template` under `_template/skeleton/`, prune unpicked rooms, move the skeleton to the repo root, and delete `_template/`
4. **Initialize git and push:**
   ```powershell
   git init
   git remote add origin <url>
   git add .
   git commit -m "Initial commit from template"
   git push -u origin main
   ```

## What INIT does (and doesn't) do

INIT scaffolds files only - it does **not** run `git init`, install dependencies, or write code. After it finishes, you'll have:

- A filled `CLAUDE.md` and `README.md` at the repo root
- A `<slug>.code-workspace` file
- The room folders you picked (each with its own `CONTEXT.md`)
- `runbooks/`, `reference/`, and `.claude/` carried over as project content
- A `.gitignore` with stack-specific entries appended below the `=== STACK-SPECIFIC ===` marker
- No `_template/` folder

## Manual path

<details>
<summary>If you'd rather not use AI, expand for the 8-step manual flow.</summary>

1. Copy this repo to a new folder.
2. Open every `*.template` under `_template/skeleton/` (recurse into subfolders), replace placeholders, save without the `.template` suffix. The full placeholder list lives in `_template/INIT-PROMPT.md` under "Placeholders."
3. Pick rooms from `_template/ROOMS.md`. Keep what you need, delete the rest. `planning/` is always included.
4. Move the contents of `_template/skeleton/` up to the repo root, overwriting `CLAUDE.md`, `README.md`, and `.gitignore`. `runbooks/`, `reference/`, `.claude/`, and `.gitattributes` stay as-is.
5. Rename `workspace.code-workspace` to `<slug>.code-workspace`.
6. Append stack-specific ignores to the new `.gitignore` below the `=== STACK-SPECIFIC ===` marker (e.g. `.terraform/`, `node_modules/`, `__pycache__/`).
7. Delete `_template/`.
8. `git init`, add a remote, push.

</details>

## Rules while you're still in template mode

- **Don't `git add` / `commit` / `push` to this repo.** The template maintainer pushes template changes manually. Once you've copied it to a new project folder and removed `.git`, normal git rules apply.
- **Don't edit `reference/` to fix a bug.** It's read-only examples - rebuild cleanly in a project context.
- **ASCII only in `.ps1` / `.psm1` / `.psd1`.** PS 5.1 in CI mangles non-ASCII.

## What this template imposes on every new project

These rules carry through INIT into the project's own `CLAUDE.md` and room `CONTEXT.md` files:

- **One screen max for the project's `CLAUDE.md`.** Push detail into room `CONTEXT.md` files.
- **Each room has a "Promotion candidates" table.** When an item appears twice, formalize it as a skill, agent, or shared module within this project.
- **File naming:** `description_status.extension`. Code uses language idiom (`Verb-Noun.ps1`, `snake_case.py`). Docs use `kebab-case`.
- **Scripts and IaC are parameters-only** - no hard-coded creds, IDs, or paths.
- **Customization files (skills, agents) go in `.claude/`.** Both Claude Code CLI and GitHub Copilot agent mode read from `.claude/agents/` and `.claude/skills/`. Avoid splitting the same project across `.github/` and `.claude/` unless you need a Copilot-only feature (e.g. agent hooks in `.agent.md` frontmatter).

## Upgrade an existing project (AI-assisted)

Use this when you already have a working repo and want to add the template's structured AI instructions, skills, and agents — without touching your code or overwriting your existing `CLAUDE.md`.

1. **Copy the template files you need into your existing repo.** The minimum set:
   ```powershell
   # From a clone of this template repo, copy these into your project:
   Copy-Item "_template"         "<your-project>\_template" -Recurse
   Copy-Item ".claude\agents"    "<your-project>\.claude\agents" -Recurse -Force
   Copy-Item ".claude\skills"    "<your-project>\.claude\skills" -Recurse -Force
   Copy-Item ".claude\settings.json" "<your-project>\.claude\settings.json"
   ```
   Skip any file that already exists in the target and that you don't want to overwrite — the upgrade agent will merge rather than replace.

2. **Open your project in VS Code.**

3. **Point the agent at the upgrade prompt:**
   ```
   Read _template/UPGRADE-PROMPT.md and run the upgrade flow.
   ```
   The agent will:
   - inventory what you already have (CLAUDE.md, rooms, `.claude/` files)
   - show a gap table and proposed changes
   - wait for your approval before touching anything
   - add only what's missing (agents, skills, settings, CONTEXT.md files, planning templates)

4. **Review and commit:**
   ```powershell
   git add .
   git commit -m "Add AI instructions and skills from project template"
   git push
   ```

> Nothing is overwritten without showing you the diff first. The upgrade flow is additive only — it never deletes files.

## Folder map (before INIT)

| Path | Purpose | Survives INIT? |
|------|---------|----------------|
| `CLAUDE.md` | Template-mode project instructions | replaced |
| `README.md` | This file | replaced |
| `_template/INIT-PROMPT.md` | Bootstrap prompt for new projects | deleted |
| `_template/UPGRADE-PROMPT.md` | Upgrade prompt for existing projects | deleted |
| `_template/ROOMS.md` | Catalog of room archetypes | deleted |
| `_template/skeleton/` | Project skeleton (templated) | promoted to root |
| `runbooks/` | Starter operational procedures | kept |
| `reference/` | Starter examples and vendor docs | kept |
| `.claude/` | Starter skills, subagents, and permission allowlist (read by both Claude Code CLI and Copilot) | kept |
| `.gitattributes` | Line-ending rules | kept |

## See also

- [`_template/INIT-PROMPT.md`](_template/INIT-PROMPT.md) - the bootstrap prompt the agent reads
- [`_template/ROOMS.md`](_template/ROOMS.md) - room archetypes INIT picks from
- [`CLAUDE.md`](CLAUDE.md) - instructions agents follow while the template is unused
