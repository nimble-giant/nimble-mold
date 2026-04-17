# nimble-mold v0.3.0 — Lint-Clean Refactor

**Date:** 2026-04-17
**Status:** Approved for planning
**Ailloy reference:** `ailloy assay` / `ailloy temper --assay` (v0.6.13+)

## Goal

`ailloy temper --assay .` returns **0 errors, 0 warnings, 0 suggestions** against
nimble-mold on the default flux and across the supported `agent.targets`
permutations (`[claude]`, `[copilot]`, `[claude,copilot]`, `[codex]`, `[aider]`).

## Why

The assay linter (new in ailloy) flags the current v0.2.0 mold with 16 warnings:

| Category | Count | Summary |
| --- | --- | --- |
| `commands-deprecated` | 10 | All `.claude/commands/*.md` outputs should migrate to `.claude/skills/<name>/SKILL.md` (new format supports both `/name` invocation and autonomous skill triggering) |
| `line-count` | 4 | `brainstorm.md` (263), `create-issue.md` (297), `pr-comments.md` (334), `pr-review.md` (530) exceed the 150-line progressive-disclosure threshold |
| `empty-file` | 1 | `.github/copilot-instructions.md` renders empty when `copilot` is not in `agent.targets` |
| `duplicate-topics` | 1 | "Get current branch" guidance duplicated in `pr-review.md` and `pr-comments.md` |

The fix doubles as a natural v0.3.0 milestone: the official ailloy mold becomes a
reference example of the skill-first, progressive-disclosure layout that
`agentskills.io` prescribes.

This is a **breaking change** for existing consumers. No transitional rendering —
`.claude/commands/` emission is removed entirely. Consumers re-cast.

## Non-goals

- Rewriting command/skill prose for style. Content is preserved verbatim unless
  a split or dedup requires light edits for flow.
- Changing flux schema for project/ore/api/scm surfaces. Those are orthogonal.
- Adding a CHANGELOG file. Release notes live in the GitHub release.
- Solving the `copilot-instructions.md` empty-file issue at the ailloy level.
  We ship a workaround and file a follow-up issue; a manifest-level `when:`
  directive in ailloy is out of scope for this release.

## Design

### 1. Migrate commands → skills (resolves 10 `commands-deprecated` warnings)

Every file in `commands/<name>.md` becomes a directory `skills/<name>/SKILL.md`:

| Current | New |
| --- | --- |
| `commands/brainstorm.md` | `skills/brainstorm/SKILL.md` + `skills/brainstorm/references/*.md` |
| `commands/create-issue.md` | `skills/create-issue/SKILL.md` + `skills/create-issue/references/*.md` |
| `commands/init-agents-md.md` | `skills/init-agents-md/SKILL.md` |
| `commands/open-pr.md` | `skills/open-pr/SKILL.md` |
| `commands/pr-comments.md` | `skills/pr-comments/SKILL.md` + `skills/pr-comments/references/*.md` |
| `commands/pr-description.md` | `skills/pr-description/SKILL.md` |
| `commands/pr-review.md` | `skills/pr-review/SKILL.md` + `skills/pr-review/references/*.md` |
| `commands/preflight.md` | `skills/preflight/SKILL.md` |
| `commands/start-issue.md` | `skills/start-issue/SKILL.md` |
| `commands/update-pr.md` | `skills/update-pr/SKILL.md` |

The existing flat `skills/brainstorm.md` is **removed** — its content is
absorbed into the new `skills/brainstorm/SKILL.md`, resolving the
command/skill duplication present in v0.2.0.

Each `SKILL.md` gets frontmatter per the
[agentskills.io specification](https://agentskills.io/specification):

```yaml
---
name: <slug>
description: <one-line trigger description>
---
```

Exact frontmatter field set is verified during implementation against the
current agentskills spec; additional fields (e.g., `model`, `allowed-tools`)
are added only when the original command behavior requires them.

**Flux and mold updates:**

- `flux.yaml`: remove `output.commands` key entirely.
- `flux.yaml`: `output.skills: .claude/skills` stays as-is.
- `mold.yaml`: no structural change required. Version bump only (see §5).
- `commands/` directory: deleted from the repo.

### 2. Split long files via progressive disclosure (resolves 4 `line-count` warnings)

Content is preserved — just redistributed so each `SKILL.md` is ≤150 lines and
pulls detail on demand via `@references/...` imports. Directory layout
(illustrative, brainstorm shown; others follow the same shape):

```text
skills/brainstorm/
├── SKILL.md                 # entrypoint: workflow overview + @imports
└── references/
    ├── freewriting.md       # phase 1 detail
    ├── cubing.md            # phase 2 detail
    ├── journalistic.md      # phase 3 detail
    ├── feasibility.md       # phase 4 detail
    ├── hypothesis.md        # phase 5 detail
    ├── proposals.md         # phase 6 detail
    └── verdict.md           # phase 7 detail
```

**Split plan per file (content-preserving):**

- **`brainstorm/SKILL.md`** (from 263 lines): keep the trigger description, the
  ordered step list, and the output-report schema. Move each phase's prose and
  examples into its own `references/<phase>.md`. SKILL.md references each phase
  at the corresponding step.
- **`create-issue/SKILL.md`** (from 297 lines): keep the workflow and invocation
  contract. Split out `references/issue-format.md` (full title/body template
  with labels/assignees examples), `references/label-taxonomy.md`, and
  `references/conventional-commits.md`.
- **`pr-comments/SKILL.md`** (from 334 lines): keep the overall flow
  (fetch → plan → edit → reply). Split out `references/response-strategies.md`,
  `references/reply-templates.md`, and `references/threading.md` (review-comment
  vs. conversation-comment distinction).
- **`pr-review/SKILL.md`** (from 530 lines): keep the silent/interactive mode
  toggle, the review workflow, and the approval gate. Split out
  `references/categories.md` (security/performance/testing/architecture),
  `references/severity.md`, `references/output-template.md`, and
  `references/posting-workflow.md` (API commands for posting reviews).

Content moves are copy-paste-preserve: no editing prose for terseness. If a
reference file also exceeds 150 lines after the split, subdivide further. The
150-line threshold applies to every rendered file; the linter enforces it.

### 3. Dedup "get current branch" (resolves 1 `duplicate-topics` warning)

Extract the shared section to a single reference file at
`skills/_shared/get-current-branch.md`. Both `skills/pr-review/SKILL.md` and
`skills/pr-comments/SKILL.md` reference it via `@_shared/get-current-branch.md`
(or whatever path the agentskills spec recommends for cross-skill shared
fragments — confirmed during implementation).

If agentskills disallows cross-skill imports, the fallback is: duplicate the
snippet into each skill's own `references/` with an explicit canonical comment,
and suppress the duplicate-topics warning via `.ailloyrc.yaml` with a clear
rationale. This fallback is a last resort.

### 4. Empty `copilot-instructions.md` (resolves 1 `empty-file` warning)

**Root cause:** the template body is wrapped in
`{{- if has "copilot" .agent.targets -}}`. When `agent.targets` does not include
`copilot` (the default), the file still emits but has zero bytes of content.
The same latent bug exists on `agent_config/CLAUDE.md` for
`copilot`-only casts.

**Fix:** remove the outer `{{- if has ... -}}` wrapper from both templates and
always emit the content. Downside: a user casting `copilot`-only gets a
`CLAUDE.md` artifact in their project, and vice versa. This is acceptable: the
file is harmless, trivial to delete, and the lint is clean across all target
permutations.

**Follow-up upstream:** file an ailloy issue requesting a manifest-level
conditional-emission directive (something like `when: has "copilot" .agent.targets`
on output paths, or automatic skipping of files that render to empty/whitespace
content). When that lands, reinstate the conditional cleanly in a future
nimble-mold release. This follow-up is **not blocking** for v0.3.0.

### 5. Version bump and documentation

- **`mold.yaml`**:
  - `version: 0.3.0`
  - `requires.ailloy: ">=X.Y.Z"` — pinned to the first ailloy tag containing the
    `has` template-function fix (tag TBD by ailloy maintainer; substitute at
    implementation time).
- **`README.md`**:
  - Replace the commands table with a skills table listing the same 10
    skills by their new slugs and one-line descriptions.
  - Update the project-structure tree to reflect `skills/<name>/SKILL.md` +
    `references/` + `_shared/`.
  - Remove all mentions of `.claude/commands/`.
  - Add a "Breaking changes in v0.3.0" section near the top: summary, why
    (lint compliance + agentskills alignment), migration instruction
    (`ailloy cast` again).
- **No CHANGELOG file.** Release notes go in the GitHub release description.

## Testing / Verification

Acceptance is lint-based:

1. `ailloy temper --assay .` → 0 errors, 0 warnings, 0 suggestions on default flux.
2. `ailloy temper --assay . --set agent.targets="[copilot]"` → 0/0/0.
3. `ailloy temper --assay . --set agent.targets="[claude,copilot]"` → 0/0/0.
4. `ailloy temper --assay . --set agent.targets="[codex]"` → 0/0/0.
5. `ailloy temper --assay . --set agent.targets="[aider]"` → 0/0/0.
6. `ailloy forge` (dry-run render, alias of `blank`/`template`) on each
   permutation — confirm files land at expected paths and contain non-empty,
   readable content.
7. Sanity-invoke one migrated skill (`/brainstorm` in a Claude Code scratch
   session) to verify the SKILL.md frontmatter and `@`-imports resolve
   correctly in practice.

If any permutation fails the lint, the fix is applied and all permutations
re-verified — no category of warning is "accepted" by suppression unless
explicitly justified in this spec (currently: none).

## Rollout

1. Land all changes on `main` in a single PR titled
   `feat!: migrate to skills, split long files, lint clean for v0.3.0`.
2. Tag `v0.3.0` once merged.
3. Update the GitHub release description with the breaking-changes summary
   and the `ailloy cast` re-installation instruction.
4. File the upstream ailloy issue for manifest-level conditional emission
   (see §4 follow-up).
