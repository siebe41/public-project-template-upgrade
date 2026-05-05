# UPGRADE prompt - bring an existing project up to the template standard

Use this when you already have a real project and want to add the structured AI instructions, skills, and agents that this template provides — without wiping what you've already built.

Point the agent at this file:

- `Read _template/UPGRADE-PROMPT.md and run the upgrade flow.`
- `Review _template/UPGRADE-PROMPT.md and bring this project up to the template standard.`

Works in Claude Code CLI or GitHub Copilot agent mode. The agent will inventory what the project already has, show you a gap report, wait for your approval, then apply only what's missing. Nothing is overwritten without confirmation.

---

I want to upgrade this existing project to use the structured AI instructions, skills, and agents from the project template. Run the flow below.

## Step 1 — Inventory (read-only, no changes yet)

Silently read the following and build a picture of what already exists:

1. **Root files:** `CLAUDE.md`, `README.md`, `.gitignore`.
2. **Room folders:** list every top-level directory. For each, check whether a `CONTEXT.md` exists inside it.
3. **AI harness detection:**
   - Check for `.claude/` folder → indicates **Claude harness** (or mixed).
   - Check for `.github/copilot-instructions.md` → indicates **Copilot harness** (or mixed).
   - If both exist, note the overlap and ask the user which harness to target before proceeding.
   - If neither exists, ask the user which harness to set up.
4. **AI harness files (Claude harness):**
   - `.claude/agents/` — list files present vs. the template agents (`powershell-reviewer.md`, `terraform-reviewer.md`).
   - `.claude/skills/` — list directories present vs. the template skills (`planning-docs/`).
   - `.claude/settings.json` — note which `allow` / `deny` entries are present vs. the template baseline.
5. **AI harness files (Copilot harness):**
   - `.github/copilot-instructions.md` — present or missing.
   - `.github/instructions/powershell-reviewer.instructions.md` — present or missing.
   - `.github/instructions/terraform-reviewer.instructions.md` — present or missing.
   - `.github/prompts/planning-docs.prompt.md` — present or missing.
6. **Planning templates:** check whether `planning/templates/` exists and contains `adr-template.md`, `spec-template.md`, `adr-example.md`, `spec-example.md`.
7. **Runbooks:** check whether `runbooks/CONTEXT.md` exists.

Do **not** make any changes during this step.

## Step 2 — Report gaps and propose changes

Present a single message with:

1. **Project snapshot** — one sentence describing what this project is (inferred from `CLAUDE.md`, `README.md`, or folder names — whichever gives the best signal).
2. **Gap table** — one row per item checked in Step 1:

   | Item | Status | Proposed action |
   |------|--------|-----------------|
   | `CLAUDE.md` | present / partial / missing | merge sections / create / none |
   | **Claude harness** | | |
   | `.claude/agents/powershell-reviewer.md` | present / missing | copy / none |
   | `.claude/agents/terraform-reviewer.md` | present / missing | copy / none |
   | `.claude/skills/planning-docs/` | present / missing | copy / none |
   | `.claude/settings.json` — allow entries | N of M present | merge missing / none |
   | **Copilot harness** | | |
   | `.github/copilot-instructions.md` | present / missing | create / none |
   | `.github/instructions/powershell-reviewer.instructions.md` | present / missing | copy / none |
   | `.github/instructions/terraform-reviewer.instructions.md` | present / missing | copy / none |
   | `.github/prompts/planning-docs.prompt.md` | present / missing | copy / none |
   | **Shared** | | |
   | `planning/templates/` | present / missing | copy templates / none |
   | `runbooks/CONTEXT.md` | present / missing | copy / none |
   | `<room>/CONTEXT.md` | present / missing (one row per room) | create / none |

   Show only the rows relevant to the detected harness (skip the other harness's rows unless both exist).

3. **CLAUDE.md detail** — if `CLAUDE.md` is present, list which of the template's sections are missing (Skills table, Subagents table, Task routing table, Naming, Rules, Avoid, etc.). Propose to append only the missing sections.
4. **Questions** — if anything is ambiguous (e.g. the project has no `CLAUDE.md` at all and you need project name / one-liner / owner to create one), ask those questions here. Otherwise, skip.

Wait for me to accept, edit, or skip individual items before doing anything.

## Step 3 — Apply approved changes

Once I confirm the proposal, apply only the approved items in this order:

### 3a — AI harness: agents, skills, and instructions

**Claude harness:**

Copy missing agent files from `_template`'s known locations into `.claude/agents/` (create the folder if it doesn't exist):

- `powershell-reviewer.md` — content defined in `.claude/agents/powershell-reviewer.md` (read it from this repo)
- `terraform-reviewer.md` — content defined in `.claude/agents/terraform-reviewer.md` (read it from this repo)

Copy the missing skill directory into `.claude/skills/`:

- `planning-docs/SKILL.md` — content defined in `.claude/skills/planning-docs/SKILL.md` (read it from this repo)

The `planning-docs` skill references template files. If this project does not have `_template/` (i.e. it was never a fresh copy of this template), update the links in the **Source files** section at the bottom of `SKILL.md` — the lines that look like `[_template/skeleton/planning/templates/adr-template.md](../../../_template/skeleton/planning/templates/adr-template.md)` — to point to `planning/templates/` instead (e.g. `[planning/templates/adr-template.md](../../../planning/templates/adr-template.md)`).

**Copilot harness:**

Copy missing instruction files into `.github/instructions/` (create the folder if it doesn't exist). Read each from `_template/skeleton/.github/instructions/` in this repo and write verbatim:

- `powershell-reviewer.instructions.md`
- `terraform-reviewer.instructions.md`

Copy the missing prompt file into `.github/prompts/` (create the folder if it doesn't exist). Read from `_template/skeleton/.github/prompts/` in this repo and write verbatim:

- `planning-docs.prompt.md`

If `.github/copilot-instructions.md` is missing, note this in the gap report and ask the user if they want to create it. If yes, ask for project name, one-liner, and owner, then generate it using `_template/skeleton/.github/copilot-instructions.md.template` as the base (or the structure from Step 3e below if `_template/` is absent).

### 3b — AI harness: settings

**Claude harness:**

If `.claude/settings.json` is missing, create it with the full baseline content:

```json
{
  "permissions": {
    "allow": [
      "Bash(git status:*)",
      "Bash(git diff:*)",
      "Bash(git log:*)",
      "Bash(git show:*)",
      "Bash(git branch:*)",
      "Bash(git remote -v)",
      "Bash(git remote get-url:*)",
      "Bash(git rev-parse:*)",
      "Bash(git config --get:*)",
      "Bash(pwsh --version)",
      "Bash(node --version)",
      "Bash(npm --version)",
      "Bash(python --version)",
      "Bash(python -V)",
      "Bash(terraform version)",
      "Bash(tofu version)",
      "Bash(az --version)"
    ],
    "deny": [
      "Read(./.env)",
      "Read(./.env.*)",
      "Read(./secrets/**)",
      "Read(**/*.pfx)",
      "Read(**/*.key)"
    ]
  }
}
```

If `.claude/settings.json` already exists, merge only the missing `allow` and `deny` entries into the existing JSON — do not remove any entries that are already there.

Ensure `.claude/settings.local.json` is listed in `.gitignore`. If it's not, append two lines: first `# Claude Code CLI local state` and then `.claude/settings.local.json`. Place them below the `=== STACK-SPECIFIC ===` marker if that marker is present, otherwise at the end of the file.

**Copilot harness:**

Check whether `.vscode/settings.json` exists and contains a `chat.tools.terminal.autoApprove` key. If it's missing, create `.vscode/settings.json` with the baseline conservative allow-list:

```json
{
  "chat.tools.terminal.autoApprove": {
    "/^git\\s+(status|diff|log|show|branch|remote(\\s+-v)?|rev-parse|config\\s+--get)\\b/": true,
    "/^Get-ChildItem\\b/": true,
    "/^ls\\b/": true,
    "/^dir\\b/": true,
    "/^pwsh\\s+--version\\b/": true,
    "/^node\\s+--version\\b/": true,
    "/^python\\s+(--version|-V)\\b/": true
  }
}
```

If `.vscode/settings.json` already exists, merge only the missing regex entries under `chat.tools.terminal.autoApprove` — do not remove entries that are already there.

### 3c — Planning templates

If `planning/` exists but `planning/templates/` does not, create the folder and copy the four template files into it. Read each from its source in this repo and write it verbatim:

- `_template/skeleton/planning/templates/adr-template.md` → `planning/templates/adr-template.md`
- `_template/skeleton/planning/templates/spec-template.md` → `planning/templates/spec-template.md`
- `_template/skeleton/planning/templates/adr-example.md` → `planning/templates/adr-example.md`
- `_template/skeleton/planning/templates/spec-example.md` → `planning/templates/spec-example.md`

If `planning/` does not exist at all, skip this step — don't create the room unless the user explicitly asked for it.

### 3d — Room CONTEXT.md files

For each room folder that is missing a `CONTEXT.md`, create one using the template below. Replace `{{ROOM_NAME}}` and `{{ROOM_PURPOSE}}` with the actual folder name and a one-line purpose inferred from the folder's contents (or from what the user confirmed in Step 2).

```markdown
# {{ROOM_NAME}}

{{ROOM_PURPOSE}}

## File naming
`description_status.extension` — e.g. `do-thing_draft.md`, `script-name_v1.ps1`.

## Rules
- No hard-coded credentials, IDs, or hostnames. Parameterize.
- ASCII only in `.ps1` / `.psm1` / `.psd1`.

## Promotion candidates
| Date | What I did | Why it might be reusable |
|------|------------|--------------------------|
| _example_ | - | - |

## Current state
_What's in progress, what's blocked. Update as work moves._
```

If `planning/CONTEXT.md` is missing, use the richer planning-specific template from `_template/skeleton/planning/CONTEXT.md.template` instead.

If `runbooks/CONTEXT.md` is missing, create it with this content:

```markdown
# Runbooks

## What happens here
Operational procedures — how to deploy, recover, restart, or troubleshoot. One file per procedure.

## File naming
`<verb>-<thing>_runbook.md` — e.g. `restart-service_runbook.md`, `recover-failed-vm_runbook.md`.

## Required sections per runbook
1. **When to use** — symptoms or trigger conditions
2. **Prerequisites** — access, tools, who to notify
3. **Steps** — numbered, copy-pasteable commands with expected output
4. **Verification** — how to confirm it worked
5. **Rollback** — what to do if it didn't
6. **Last verified** — date + initials

## What good looks like
- A teammate at 2 a.m. can follow it without calling you.
- Commands are exact, with placeholders clearly marked (`<resource-name>`).
- Failure modes are listed, not glossed over.
```

### 3e — Instructions file (CLAUDE.md or .github/copilot-instructions.md)

**Claude harness — CLAUDE.md:**

**If `CLAUDE.md` is missing entirely:**

Ask me for:
1. Project name
2. One-liner
3. Owner

Then generate a minimal `CLAUDE.md` using the structure from `_template/skeleton/CLAUDE.md.template` (read it from this repo if it exists; otherwise use the section headings listed below as a baseline). Populate only the sections where you have real information. Use `_TBD_` for anything you can't infer. Do not invent commands, rooms, or avoid-list items — leave those sections as examples for the user to fill in.

**If `CLAUDE.md` is present but missing sections:**

Append only the missing sections from the list below at the end of the file. Don't restructure or rewrite existing content.

Missing-section templates to append (Claude):

```markdown
## Skills (`.claude/skills/`)
| Skill | When invoked |
|-------|--------------|
| `planning-docs` | Authoring or reviewing ADRs and technical specs |
```

```markdown
## Subagents (`.claude/agents/`)
| Agent | When invoked |
|-------|--------------|
| `powershell-reviewer` | Review of `.ps1` / `.psm1` / `.psd1` files |
| `terraform-reviewer` | Review of `.tf` / `.tfvars` / `.hcl` files |
| `Explore` (built-in, Claude Code CLI only) | Read-only codebase Q&A |
```

```markdown
## Naming
Pattern: `description_status.extension`. Markdown/HTML use kebab-case. Code uses language idiom (PowerShell `Verb-Noun.ps1`, Python `snake_case.py`).
```

```markdown
## Rules
- Plain language. Ask before assuming. Say so when unsure.
- Prefer editing existing files. Don't create docs to summarize work — say it in chat.
- Verify before declaring done. Run the actual command or test.
- No credentials, tenant/subscription/account IDs, or hostnames in source. Parameterize.
- Don't push commits unless asked. Run `git status` first and confirm the remote + target branch.
- ASCII only in `.ps1` / `.psm1` / `.psd1`.
```

**Copilot harness — .github/copilot-instructions.md:**

**If `.github/copilot-instructions.md` is missing entirely:**

Ask me for:
1. Project name
2. One-liner
3. Owner

Then generate a minimal `.github/copilot-instructions.md` using the structure from `_template/skeleton/.github/copilot-instructions.md.template` (read it from this repo if it exists; otherwise use the same section headings but replace the Skills/Subagents sections with Instructions/Prompts sections as shown in the template). Populate only the sections where you have real information. Use `_TBD_` for anything you can't infer.

**If `.github/copilot-instructions.md` is present but missing sections:**

Append only the missing sections at the end of the file. Don't restructure or rewrite existing content.

Missing-section templates to append (Copilot):

```markdown
## Instructions (`.github/instructions/`)
Applied automatically by Copilot when the file pattern matches. No explicit invocation needed.

| File | Applied to |
|------|-----------|
| `powershell-reviewer.instructions.md` | `**/*.ps1`, `**/*.psm1`, `**/*.psd1` |
| `terraform-reviewer.instructions.md` | `**/*.tf`, `**/*.tfvars`, `**/*.hcl` |
```

```markdown
## Prompts (`.github/prompts/`)
Reusable prompts — invoke with `/` in Copilot chat (e.g. `/planning-docs`).

| Prompt | When to use |
|--------|-------------|
| `planning-docs` | Authoring or reviewing ADRs and technical specs |
```

```markdown
## Naming
Pattern: `description_status.extension`. Markdown/HTML use kebab-case. Code uses language idiom (PowerShell `Verb-Noun.ps1`, Python `snake_case.py`).
```

```markdown
## Rules
- Plain language. Ask before assuming. Say so when unsure.
- Prefer editing existing files. Don't create docs to summarize work — say it in chat.
- Verify before declaring done. Run the actual command or test.
- No credentials, tenant/subscription/account IDs, or hostnames in source. Parameterize.
- Don't push commits unless asked. Run `git status` first and confirm the remote + target branch.
- ASCII only in `.ps1` / `.psm1` / `.psd1`.
```

Only add a section if it genuinely doesn't exist yet (check for heading text, not exact wording).

## Step 4 — Verify and summarise

After all changes are applied:

1. List every file created or modified.
2. Flag anything that was skipped and why.
3. If `planning/` exists, print the current ADR and spec count so the user knows where the queue stands.
4. Print the next 3 recommended actions (e.g. "fill in CLAUDE.md Avoid section", "run powershell-reviewer on `automation/`").

## Hard rules

- Never overwrite a file that already has content without showing the diff first and getting explicit confirmation.
- Never delete files. This flow is additive only.
- Don't run `git init` or push. The user controls git.
- Don't add rooms, code, or scripts beyond what's listed above. Structural additions only.
- Don't invent placeholder values. If you can't infer a value, use `_TBD_` and note it in Step 4.
