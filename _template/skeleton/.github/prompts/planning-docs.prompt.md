---
mode: agent
description: "Author or review planning documents — Architecture Decision Records (ADRs) and technical specs — using the templates in this project."
tools:
  - read_file
  - create_file
  - insert_edit_into_file
  - semantic_search
  - file_search
---

# Planning docs (ADRs and specs)

Two document types, one prompt. Both are short, opinionated, and written **before** code.

## When to use which

| Question | Document |
|----------|----------|
| "Why did we choose X over Y?" — a single decision with trade-offs you'll forget in six months | **ADR** |
| "What are we building, for whom, and how do we know it works?" — a feature or system design | **Spec** |
| Both at once (a spec that contains a major decision worth surfacing) | Spec + a separate ADR linked from it |

If neither fits, you don't need a planning doc — write a runbook, a README, or just code.

## File locations and naming

- ADRs live in `planning/decisions/` (or whatever the project's planning room calls its decisions folder — check `planning/CONTEXT.md` first).
- Specs live in `planning/specs/`.
- ADR filename: `ADR-NNN-kebab-case-title.md` where `NNN` is the next zero-padded number (look at the existing files; don't reuse a number).
- Spec filename: `<topic>-spec.md` (kebab-case, no date prefix — use the `Last updated` field for that).

The templates are at:
- `planning/templates/adr-template.md`
- `planning/templates/spec-template.md`

Worked examples (filled-in references) sit beside the templates at:
- `planning/templates/adr-example.md`
- `planning/templates/spec-example.md`

Read the example first when you need a sense of the right level of detail; copy from the *template* (not the example) when starting a new doc.

## Templates (canonical structure)

### ADR

```markdown
# ADR-NNN: [Decision title]

**Status:** proposed | accepted | superseded by ADR-XXX
**Date:** YYYY-MM-DD
**Deciders:** [names]

## Context
What is the situation that forces a decision? Constraints, requirements, what's already been tried. Keep it short — this is background, not a story.

## Decision
The choice we're making, stated as a clear sentence: "We will use X for Y because Z."

## Consequences
What changes as a result. Both good and bad.

**Positive:**
- [outcome]

**Negative / trade-offs:**
- [outcome]

**Follow-ups required:**
- [action item]

## Alternatives considered
| Option | Why not |
|--------|---------|
| [A] | [reason] |
| [B] | [reason] |
```

### Spec

```markdown
# [Topic] — Spec

**Status:** draft | review | final
**Owner:** [name]
**Last updated:** YYYY-MM-DD

## Problem
What are we solving and for whom? One paragraph. If you can't state it, you don't have a spec yet.

## Goals
- [Measurable outcome 1]
- [Measurable outcome 2]

## Non-goals
What this spec is *not* doing. Things people might assume are in scope but aren't.

## Approach
The chosen design. Plain English. Mermaid diagrams when they help. Describe what the system does, not how every line of code works.

## Alternatives considered
| Option | Why rejected |
|--------|--------------|
| [A] | [reason] |
| [B] | [reason] |

## Risks and mitigations
| Risk | Likelihood | Mitigation |
|------|------------|------------|
| [risk] | low/med/high | [what we do] |

## Success criteria
How we know it's working. Concrete and testable.
- [ ] [criterion]
- [ ] [criterion]

## Open questions
Anything still TBD. Resolve before status moves to `final`.
```

## Rules

1. **No code without a spec or ADR for any non-trivial change.** "Non-trivial" = touches more than one component, costs money, changes a contract, or you'd struggle to explain it in one sentence. Trivial bugfixes don't need a spec.
2. **One decision per ADR.** If you're tempted to write "ADR-007: data model and auth and rate limiting," split it into three.
3. **Status is a lifecycle, not decoration.** ADRs go `proposed → accepted` (or `superseded by ADR-XXX`). Specs go `draft → review → final`. If a doc has been "draft" for three months, it's abandoned — delete it or close it.
4. **Date the change.** Every status change updates the `Date` (ADR) or `Last updated` (spec) field.
5. **Supersede, don't rewrite.** When an accepted ADR is wrong, write a new ADR that supersedes it. Mark the old one `superseded by ADR-XXX` and leave its body intact. The history is the value.
6. **Link, don't duplicate.** Specs reference ADRs they depend on; ADRs reference the spec that motivated them. Use relative markdown links.
7. **Keep it one screen where possible.** If a spec passes ~400 lines, you're writing implementation notes, not a spec. Move detail into the code or into linked sub-docs.
8. **Plain English, not jargon.** A new teammate should be able to read the doc and understand the decision without a glossary.

## Authoring workflow

When the user asks for a new ADR or spec:

1. **Check `planning/CONTEXT.md`** (if it exists) for project-specific routing — sometimes there's a `decisions/` vs `adrs/` naming preference, or an ADR ledger to update.
2. **Find the next ADR number** by listing `planning/decisions/` (or equivalent). Use `NNN` zero-padded. For specs, just pick a kebab-case topic name.
3. **Copy the template verbatim**, then fill in. Don't restructure the headings — downstream tooling and skim-readers depend on them.
4. **Fill in every section.** If a section genuinely doesn't apply, write `_None._` rather than deleting the heading.
5. **Status = `proposed` (ADR) or `draft` (spec)** on first write. The author doesn't accept their own ADR.
6. **Cross-link.** If this doc supersedes another, update both files in the same change.

## Reviewing workflow

When asked to review an existing planning doc, check for:

- [ ] Status field present and current
- [ ] Date / Last updated reflects the latest change
- [ ] Problem (spec) or Context (ADR) is one paragraph, no story-mode
- [ ] Decision is stated as one sentence (ADR), or Goals are measurable (spec)
- [ ] Alternatives section actually lists alternatives — "considered nothing else" is a smell
- [ ] Consequences (ADR) include negatives, not only positives
- [ ] Success criteria (spec) are testable, not vibes
- [ ] No accidental ADR-merging (one decision per ADR)
- [ ] Links resolve; superseded ADRs point to the replacement
- [ ] Filename matches the `ADR-NNN-...` / `<topic>-spec.md` convention

Report findings as a bulleted list. Don't rewrite the doc unless asked.

## Anti-patterns to flag

- **Rationalization, not decision.** ADR written after the code shipped, with "Alternatives considered" backfilled to make the chosen path look inevitable. Push back.
- **Spec-as-design-doc.** 800 lines of class diagrams and method signatures. That belongs in code or an architecture doc, not a spec. A spec answers "what and why," not "how every line works."
- **Status drift.** A folder full of `proposed` ADRs and `draft` specs from a year ago. Either accept, supersede, or delete.
- **The mega-spec.** One spec covering five features. Split it.
- **No non-goals.** Every spec has things it's deliberately not doing. If the section is empty, the author didn't think hard enough about scope.
