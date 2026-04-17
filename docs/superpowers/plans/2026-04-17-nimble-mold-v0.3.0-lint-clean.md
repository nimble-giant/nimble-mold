# nimble-mold v0.3.0 Lint-Clean Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Drive `ailloy temper --assay .` to 0 errors / 0 warnings / 0 suggestions across all supported `agent.targets` permutations, migrating the mold to the skill-first progressive-disclosure layout and bumping to v0.3.0.

**Architecture:** Migrate every `commands/<name>.md` to `skills/<name>/SKILL.md`, splitting the four long ones (`brainstorm`, `create-issue`, `pr-comments`, `pr-review`) across `references/` via `@`-imports. Extract the duplicated "get current branch" section to a shared location. Remove the outer `{{- if has "<target>" .agent.targets -}}` wrapper on `CLAUDE.md` and `copilot-instructions.md` so they never render empty. Delete `commands/` and drop `output.commands` from flux. Update README and bump `mold.yaml` to `0.3.0`.

**Tech Stack:** ailloy (`temper`, `assay`, `forge`), Go text/template + Sprig, Markdown, GitHub Actions (unchanged), `gh` CLI (unchanged).

**Reference spec:** [`docs/superpowers/specs/2026-04-17-nimble-mold-v0.3.0-lint-clean-design.md`](../specs/2026-04-17-nimble-mold-v0.3.0-lint-clean-design.md)

**Branch:** `ailloy-assay-refactor` (spec already committed here)

---

## SKILL.md frontmatter convention (used throughout)

Every `SKILL.md` created in this plan uses this frontmatter shape, per the [agentskills.io specification](https://agentskills.io/specification):

```yaml
---
name: <slug matching directory name>
description: <one-line when-to-use trigger, imperative tense>
---
```

If Task 1 verifies the spec requires additional fields (`model`, `allowed-tools`, etc.), add them per-skill as the original command behavior requires. Do **not** invent fields the spec does not define.

## `@`-import convention (used throughout)

Inside a `SKILL.md`, reference a sibling file with `@references/<name>.md` (relative to the `SKILL.md`). For the cross-skill shared fragment (Task 13), Task 1 will verify whether `@_shared/<name>.md` resolves from inside `skills/<other>/SKILL.md`. If not, the fallback is a per-skill `references/get-current-branch.md` duplicate with a leading canonical comment, and the duplicate-topics rule is suppressed via `.ailloyrc.yaml` with a rationale.

---

## Task 1: Preflight — verify environment, baseline, and spec details

**Files:**
- Read: agentskills.io specification (web)
- Read: `flux.yaml`, `mold.yaml`
- No file modifications in this task

- [ ] **Step 1: Confirm ailloy version and baseline assay**

Run:
```bash
ailloy --version
ailloy temper --assay . 2>&1 | tee /tmp/nimble-mold-baseline-assay.txt
```

Expected: ailloy `v0.6.13-7-g3d2f29c` or newer (must include the `has` template-function fix). Baseline assay shows **16 warnings** across 4 categories:
- 10 × `commands-deprecated`
- 4 × `line-count` (brainstorm 263, create-issue 297, pr-comments 334, pr-review 530)
- 1 × `empty-file` (`.github/copilot-instructions.md`)
- 1 × `duplicate-topics` ("get current branch" in pr-review + pr-comments)

If the warning count is not 16, stop and reconcile before proceeding.

- [ ] **Step 2: Fetch agentskills.io spec and confirm SKILL.md frontmatter**

Run: `curl -s https://agentskills.io/specification > /tmp/agentskills-spec.html` (or use WebFetch).

Confirm:
- Required frontmatter fields: `name`, `description` (minimum).
- Optional fields this plan may use: verify presence of `model`, `allowed-tools`, or others.
- `@path/to/file.md` import syntax for progressive disclosure.
- Support for `skills/_shared/` or equivalent cross-skill shared content.

Record the findings inline at the top of this plan file (add a sub-section `## Agentskills spec notes (filled in Task 1)`) so subsequent tasks can reference them. Do not invent fields.

- [ ] **Step 3: Decide shared-fragment location**

Based on Step 2 findings, decide one of:
- **(A)** `skills/_shared/get-current-branch.md` reachable from other skills via `@_shared/get-current-branch.md` or `@../_shared/get-current-branch.md` — preferred.
- **(B)** Per-skill `references/get-current-branch.md` duplicate, with a canonical-source comment, and a `.ailloyrc.yaml` suppression entry for `duplicate-topics` on that specific heading. Fallback only.

Record the chosen approach in the same `## Agentskills spec notes` section.

- [ ] **Step 4: Commit baseline + notes**

```bash
git add docs/superpowers/plans/2026-04-17-nimble-mold-v0.3.0-lint-clean.md
git commit -m "docs(plan): record agentskills spec notes and baseline assay"
```

(Only commit if the plan file was edited to add the notes section; skip otherwise.)

---

## Task 2: Remove always-empty wrapping conditionals

**Files:**
- Modify: `agent_config/CLAUDE.md`
- Modify: `copilot_config/copilot-instructions.md`

- [ ] **Step 1: Strip outer conditional from `agent_config/CLAUDE.md`**

Remove line 1 (`{{- if has "claude" .agent.targets -}}`) and the final `{{- end -}}` line. The remaining content (from `@agents.md` through `Save brainstorm and analysis reports to {{agent.plans_dir}}. Create the directory if it does not exist.`) stays intact and always renders.

Result — file begins with `@agents.md` on line 1.

- [ ] **Step 2: Strip outer conditional from `copilot_config/copilot-instructions.md`**

Remove line 1 (`{{- if has "copilot" .agent.targets -}}`) and the final `{{- end -}}` line. The `{{scm.provider}}` and `{{scm.cli}}` template vars inside the body remain untouched.

Result — file begins with `# Copilot Instructions` on line 1.

- [ ] **Step 3: Run assay to verify the `empty-file` warning is gone**

Run: `ailloy temper --assay . 2>&1 | grep -E 'empty-file|Validation'`

Expected: no `empty-file` category in output; total warnings drops from 16 to **15**.

- [ ] **Step 4: Commit**

```bash
git add agent_config/CLAUDE.md copilot_config/copilot-instructions.md
git commit -m "fix(templates): always emit agent config files, drop empty-render conditional

Removes the outer {{- if has \"<target>\" .agent.targets -}} wrapper from
agent_config/CLAUDE.md and copilot_config/copilot-instructions.md so they
never render to empty files under non-matching agent.targets permutations.
Resolves the empty-file assay warning. Users targeting only one agent get a
harmless second config artifact they can delete post-cast; a follow-up
ailloy issue requests manifest-level conditional emission."
```

---

## Task 3: Migrate `open-pr` to skill directory (short, no split)

**Files:**
- Create: `skills/open-pr/SKILL.md`
- Delete: `commands/open-pr.md`

- [ ] **Step 1: Create `skills/open-pr/SKILL.md` with frontmatter + verbatim content**

Copy the entire body of `commands/open-pr.md`. Prepend frontmatter:

```yaml
---
name: open-pr
description: Open a new pull request or update an existing one on the current branch. Use when the user says "open a PR", "push this up as a PR", or "create a pull request".
---
```

Leave all `{{...}}` template directives, headings, and prose exactly as-is. The file has 60 source lines; after adding ~4 frontmatter lines, well under 150.

- [ ] **Step 2: Delete the old command file**

```bash
git rm commands/open-pr.md
```

- [ ] **Step 3: Run assay, expect one fewer commands-deprecated warning**

Run: `ailloy temper --assay . 2>&1 | grep -c 'commands-deprecated'`

Expected: count drops by 1 (from 10 to 9 `commands-deprecated` warnings). No new warnings introduced.

- [ ] **Step 4: Commit**

```bash
git add skills/open-pr/SKILL.md
git commit -m "refactor(skills): migrate open-pr command to skill directory"
```

---

## Task 4: Migrate `pr-description` to skill directory (short, no split)

**Files:**
- Create: `skills/pr-description/SKILL.md`
- Delete: `commands/pr-description.md`

- [ ] **Step 1: Create `skills/pr-description/SKILL.md`**

Copy the full body of `commands/pr-description.md`. Prepend:

```yaml
---
name: pr-description
description: Generate a comprehensive PR description from the branch diff against main. Use when the user says "write the PR description", "draft the PR body", or asks for PR summary content.
---
```

- [ ] **Step 2: Delete the old command file**

```bash
git rm commands/pr-description.md
```

- [ ] **Step 3: Run assay, verify one fewer commands-deprecated warning**

Run: `ailloy temper --assay . 2>&1 | grep -c 'commands-deprecated'`

Expected: count drops by 1.

- [ ] **Step 4: Commit**

```bash
git add skills/pr-description/SKILL.md
git commit -m "refactor(skills): migrate pr-description command to skill directory"
```

---

## Task 5: Migrate `update-pr` to skill directory (short, no split)

**Files:**
- Create: `skills/update-pr/SKILL.md`
- Delete: `commands/update-pr.md`

- [ ] **Step 1: Create `skills/update-pr/SKILL.md`**

Copy the full body of `commands/update-pr.md`. Prepend:

```yaml
---
name: update-pr
description: Regenerate the PR description for the current branch from scratch. Use when the user says "update the PR", "refresh the PR body", or "regenerate PR description".
---
```

- [ ] **Step 2: Delete the old command file**

```bash
git rm commands/update-pr.md
```

- [ ] **Step 3: Run assay, verify one fewer commands-deprecated warning**

Expected: count drops by 1.

- [ ] **Step 4: Commit**

```bash
git add skills/update-pr/SKILL.md
git commit -m "refactor(skills): migrate update-pr command to skill directory"
```

---

## Task 6: Migrate `preflight` to skill directory (short, no split)

**Files:**
- Create: `skills/preflight/SKILL.md`
- Delete: `commands/preflight.md`

- [ ] **Step 1: Create `skills/preflight/SKILL.md`**

Copy the full body of `commands/preflight.md`. Prepend:

```yaml
---
name: preflight
description: Pre-flight planning ceremony that evaluates work against INVEST criteria, generates BDD acceptance specs, and sketches architecture before any code is written. Use when the user says "preflight", "plan this", or "let's scope this work".
---
```

- [ ] **Step 2: Delete the old command file**

```bash
git rm commands/preflight.md
```

- [ ] **Step 3: Run assay, verify one fewer commands-deprecated warning**

Expected: count drops by 1.

- [ ] **Step 4: Commit**

```bash
git add skills/preflight/SKILL.md
git commit -m "refactor(skills): migrate preflight command to skill directory"
```

---

## Task 7: Migrate `start-issue` to skill directory (short, no split)

**Files:**
- Create: `skills/start-issue/SKILL.md`
- Delete: `commands/start-issue.md`

- [ ] **Step 1: Create `skills/start-issue/SKILL.md`**

Copy the full body of `commands/start-issue.md`. Prepend:

```yaml
---
name: start-issue
description: Fetch a GitHub issue by number and begin focused implementation scoped to that issue. Use when the user says "start issue 42", "work on #42", or provides an issue URL.
---
```

- [ ] **Step 2: Delete the old command file**

```bash
git rm commands/start-issue.md
```

- [ ] **Step 3: Run assay, verify one fewer commands-deprecated warning**

Expected: count drops by 1.

- [ ] **Step 4: Commit**

```bash
git add skills/start-issue/SKILL.md
git commit -m "refactor(skills): migrate start-issue command to skill directory"
```

---

## Task 8: Migrate `init-agents-md` to skill directory (short, no split)

**Files:**
- Create: `skills/init-agents-md/SKILL.md`
- Delete: `commands/init-agents-md.md`

- [ ] **Step 1: Create `skills/init-agents-md/SKILL.md`**

Copy the full body of `commands/init-agents-md.md` (111 source lines; under threshold). Prepend:

```yaml
---
name: init-agents-md
description: Research the project and generate a holistic AGENTS.md file at the repo root. Use when the user says "init agents.md", "create AGENTS.md", or the project lacks agent instructions.
---
```

- [ ] **Step 2: Delete the old command file**

```bash
git rm commands/init-agents-md.md
```

- [ ] **Step 3: Run assay, verify one fewer commands-deprecated warning**

Expected: count drops by 1.

- [ ] **Step 4: Commit**

```bash
git add skills/init-agents-md/SKILL.md
git commit -m "refactor(skills): migrate init-agents-md command to skill directory"
```

---

## Task 9: Migrate + split `brainstorm` to skill directory (absorbs flat `skills/brainstorm.md`)

**Files:**
- Create: `skills/brainstorm/SKILL.md`
- Create: `skills/brainstorm/references/freewriting.md`
- Create: `skills/brainstorm/references/cubing.md`
- Create: `skills/brainstorm/references/journalistic.md`
- Create: `skills/brainstorm/references/feasibility.md`
- Create: `skills/brainstorm/references/hypothesis.md`
- Create: `skills/brainstorm/references/proposals.md`
- Create: `skills/brainstorm/references/verdict.md`
- Delete: `commands/brainstorm.md`
- Delete: `skills/brainstorm.md` (flat file superseded by directory)

- [ ] **Step 1: Map each section of `commands/brainstorm.md` to its destination**

Read `commands/brainstorm.md`. Identify the 7 phase sections (freewriting, cubing, journalistic questions, feasibility assessment, hypothesis extraction, solution proposals, verdict) plus workflow scaffolding. Each phase's prose, examples, and any phase-specific templates move to `references/<phase>.md` **verbatim** — no rewrites, no trims, no line-count gardening.

- [ ] **Step 2: Create `skills/brainstorm/SKILL.md` entrypoint**

Structure:

```markdown
---
name: brainstorm
description: Seven-phase structured brainstorming that analyzes an idea for feasibility, scope, and value and saves a report — never generates code. Use when the user says "brainstorm X", "is this worth building?", "analyze this idea", or "pressure-test this".
---

# Brainstorm

<scope / when-to-use / output-report preamble from the original command, minus per-phase detail>

## Workflow

### Step 1: Begin Analysis
{{- if .agent.plan_mode.enabled }}
{{agent.plan_mode.enter}} immediately. The brainstorm output IS the plan.
{{- else }}
Begin the structured analysis. The brainstorm output will be presented for user review.
{{- end }}

### Step 2: Phase 1 — Freewriting
@references/freewriting.md

### Step 3: Phase 2 — Cubing
@references/cubing.md

### Step 4: Phase 3 — Journalistic Questions
@references/journalistic.md

### Step 5: Phase 4 — Feasibility Assessment
@references/feasibility.md

### Step 6: Phase 5 — Hypothesis Extraction
@references/hypothesis.md

### Step 7: Phase 6 — Solution Proposals
@references/proposals.md

### Step 8: Phase 7 — Verdict
@references/verdict.md

## Output

<report-writing instructions and save-location guidance from the original, preserved verbatim>
```

Target: SKILL.md ≤ 150 rendered lines (well under — the scaffolding alone is <60 lines; most content now lives in references/).

- [ ] **Step 3: Create each `references/<phase>.md`**

For each of the 7 phases, create the reference file with the verbatim phase content lifted from `commands/brainstorm.md`. Include any code blocks, tables, or examples in the original. No content is dropped; no prose is rewritten. Each reference file has no frontmatter (per agentskills — frontmatter is SKILL.md only, confirmed in Task 1).

- [ ] **Step 4: Delete the old command file and the superseded flat skill file**

```bash
git rm commands/brainstorm.md
git rm skills/brainstorm.md
```

If the flat `skills/brainstorm.md` contains any content NOT already represented in the new `SKILL.md` + references (compare before deletion), fold the missing content into the appropriate reference file. The flat file is 220 source lines and was the prior "skill" form of the same brainstorming workflow; complete absorption is required.

- [ ] **Step 5: Run assay, verify deltas**

Run: `ailloy temper --assay . 2>&1 | tail -40`

Expected:
- `commands-deprecated` count drops by 1.
- `line-count` warning for `brainstorm.md` disappears (rendered SKILL.md is well under 150).
- Every new `references/*.md` file is under 150 rendered lines. If any exceeds the threshold, split further within this task — do **not** defer.
- No new warnings (e.g., empty-file, duplicate-topics) introduced.

- [ ] **Step 6: Commit**

```bash
git add skills/brainstorm/
git commit -m "refactor(skills): migrate and split brainstorm into progressive-disclosure skill

Consolidates commands/brainstorm.md and the flat skills/brainstorm.md
into skills/brainstorm/SKILL.md with per-phase references. Content
preserved verbatim across the split."
```

---

## Task 10: Migrate + split `create-issue` to skill directory

**Files:**
- Create: `skills/create-issue/SKILL.md`
- Create: `skills/create-issue/references/issue-format.md`
- Create: `skills/create-issue/references/label-taxonomy.md`
- Create: `skills/create-issue/references/conventional-commits.md`
- Delete: `commands/create-issue.md`

- [ ] **Step 1: Map sections to destinations**

Read `commands/create-issue.md` (387 source lines). Identify:
- Core workflow + invocation contract → stays in `SKILL.md`.
- Full issue title/body template with labels/assignees/project examples → `references/issue-format.md`.
- Label taxonomy (list + descriptions of each label, when to use) → `references/label-taxonomy.md`.
- Conventional commit type reference (feat/fix/chore/docs/refactor/epic) with examples → `references/conventional-commits.md`.

If the command contains content that does not fit any of these three buckets, create additional reference files — never silently drop content.

- [ ] **Step 2: Create `skills/create-issue/SKILL.md` entrypoint**

Frontmatter:

```yaml
---
name: create-issue
description: Create well-formatted GitHub issues with conventional-commit-style titles, labels, and project-board assignment. Use when the user says "create an issue", "file a ticket", or "open a GitHub issue".
---
```

Body: the workflow steps, invocation contract, and short inline references like `See @references/issue-format.md for the full title/body template.` at each point the detail is needed. Preserve every `{{...}}` template directive, `{{issue.create}}` / `{{issue.create_with_labels}}` / `{{project.*}}` var, etc.

Target: SKILL.md ≤ 150 rendered lines.

- [ ] **Step 3: Create each `references/*.md`**

Lift the corresponding sections verbatim. Ensure each reference renders to <150 lines; if `references/issue-format.md` (likely the largest) exceeds, split it further (e.g., `issue-format-body.md` + `issue-format-labels.md`).

- [ ] **Step 4: Delete the old command file**

```bash
git rm commands/create-issue.md
```

- [ ] **Step 5: Run assay, verify deltas**

Expected:
- `commands-deprecated` count drops by 1.
- `line-count` warning for `create-issue.md` disappears.
- No new warnings.

- [ ] **Step 6: Commit**

```bash
git add skills/create-issue/
git commit -m "refactor(skills): migrate and split create-issue into progressive-disclosure skill"
```

---

## Task 11: Migrate + split `pr-comments` to skill directory

**Files:**
- Create: `skills/pr-comments/SKILL.md`
- Create: `skills/pr-comments/references/response-strategies.md`
- Create: `skills/pr-comments/references/reply-templates.md`
- Create: `skills/pr-comments/references/threading.md`
- Delete: `commands/pr-comments.md`

- [ ] **Step 1: Map sections to destinations**

Read `commands/pr-comments.md` (339 source lines). Identify:
- Overall workflow (fetch → plan → edit → reply loop) → stays in `SKILL.md`.
- How to categorize and respond to different kinds of review feedback (agree/disagree/clarify/defer) → `references/response-strategies.md`.
- Concrete reply-comment templates → `references/reply-templates.md`.
- The review-comment vs. conversation-comment distinction + the corresponding `gh api` endpoints and when to use each (`{{api.reply_to_conversation_comment}}` vs `{{api.reply_to_review_comment}}`) → `references/threading.md`.

The "get current branch" section stays inline in `SKILL.md` for now — **Task 13** extracts it to the shared location.

- [ ] **Step 2: Create `skills/pr-comments/SKILL.md` entrypoint**

Frontmatter:

```yaml
---
name: pr-comments
description: Fetch all PR review comments, plan strategic responses, apply code changes, and post replies in one pass. Use when the user says "respond to PR comments", "address the review", or "reply to the feedback on PR 42".
---
```

Body: the workflow scaffolding with inline `@references/...` imports where detail is needed. Preserve all `{{api.*}}` / `{{pr.*}}` / `{{scm.*}}` vars.

- [ ] **Step 3: Create each `references/*.md`**

Lift content verbatim. Any reference that lands over 150 lines must be subdivided inside this task.

- [ ] **Step 4: Delete the old command file**

```bash
git rm commands/pr-comments.md
```

- [ ] **Step 5: Run assay, verify deltas**

Expected:
- `commands-deprecated` count drops by 1.
- `line-count` warning for `pr-comments.md` disappears.
- `duplicate-topics` warning still present (not yet fixed — Task 13 addresses).

- [ ] **Step 6: Commit**

```bash
git add skills/pr-comments/
git commit -m "refactor(skills): migrate and split pr-comments into progressive-disclosure skill"
```

---

## Task 12: Migrate + split `pr-review` to skill directory

**Files:**
- Create: `skills/pr-review/SKILL.md`
- Create: `skills/pr-review/references/categories.md`
- Create: `skills/pr-review/references/severity.md`
- Create: `skills/pr-review/references/output-template.md`
- Create: `skills/pr-review/references/posting-workflow.md`
- Delete: `commands/pr-review.md`

- [ ] **Step 1: Map sections to destinations**

Read `commands/pr-review.md` (519 source lines, largest). Identify:
- Silent/interactive mode toggle, review workflow skeleton, approval gate → stays in `SKILL.md`.
- Review category definitions (security / performance / testing / architecture) with examples → `references/categories.md`.
- Severity definitions and when to assign which level → `references/severity.md`.
- The markdown review-report output template → `references/output-template.md`.
- The `gh api` commands for posting review summary + inline comments (`{{api.post_review}}`) and the interactive-mode posting flow → `references/posting-workflow.md`.

The "get current branch" section stays inline in `SKILL.md` for now — **Task 13** extracts.

- [ ] **Step 2: Create `skills/pr-review/SKILL.md` entrypoint**

Frontmatter:

```yaml
---
name: pr-review
description: Conduct a full code review with silent (markdown report) or interactive (posts inline comments) mode. Supports focused reviews by category (security, performance, testing, architecture). Use when the user says "review this PR", "security-review PR 42", or "do a code review".
---
```

Body: workflow + mode toggle + approval gate with inline `@references/...` imports at each point.

- [ ] **Step 3: Create each `references/*.md`**

Lift verbatim. Subdivide any reference that exceeds 150 rendered lines (likely `references/output-template.md` given the original 530-line flagged size — check carefully).

- [ ] **Step 4: Delete the old command file**

```bash
git rm commands/pr-review.md
```

- [ ] **Step 5: Run assay, verify deltas**

Expected:
- `commands-deprecated` count drops by 1 — to **0** (this is the last command).
- `line-count` warning for `pr-review.md` disappears — all line-count warnings now resolved.
- `duplicate-topics` warning still present (Task 13 fixes).
- 1 `empty-file` warning still resolved (from Task 2).
- Remaining total: **1 warning** (`duplicate-topics`).

- [ ] **Step 6: Commit**

```bash
git add skills/pr-review/
git commit -m "refactor(skills): migrate and split pr-review into progressive-disclosure skill"
```

---

## Task 13: Extract duplicated "get current branch" to shared location

**Files:**
- Create: `skills/_shared/get-current-branch.md` (if Task 1 confirmed approach A) — OR: `skills/pr-review/references/get-current-branch.md` + `skills/pr-comments/references/get-current-branch.md` with `.ailloyrc.yaml` suppression (fallback B)
- Modify: `skills/pr-review/SKILL.md`
- Modify: `skills/pr-comments/SKILL.md`
- Possibly create: `.ailloyrc.yaml` (fallback B only)

- [ ] **Step 1: Lift the exact duplicated content**

From `skills/pr-review/SKILL.md` and `skills/pr-comments/SKILL.md`, isolate the "get current branch" heading and its body. Confirm they are byte-identical (or near-identical — normalize if not). Copy to the shared location chosen in Task 1.

- [ ] **Step 2: Replace the duplicated section in both SKILL files with an import**

Under approach **A**, replace the inline section in each SKILL.md with:

```markdown
## Get Current Branch

@../_shared/get-current-branch.md
```

(or whatever relative path Task 1 confirmed works for cross-skill imports.)

Under approach **B**, each SKILL.md imports from its own `references/get-current-branch.md`, both copies have a leading comment noting the canonical origin, and `.ailloyrc.yaml` suppresses the `duplicate-topics` rule for exactly this heading — with a rationale comment.

- [ ] **Step 3: Run assay, verify duplicate-topics is gone**

Run: `ailloy temper --assay . 2>&1 | grep -E 'duplicate-topics|Validation'`

Expected:
- No `duplicate-topics` category.
- Total warnings: **0**.
- Total errors: **0**.
- Total suggestions: **0**.

If any warning remains, stop and investigate before proceeding.

- [ ] **Step 4: Commit**

```bash
git add skills/
# also add .ailloyrc.yaml if approach B was taken
git commit -m "refactor(skills): extract shared 'get current branch' to single source

Dedupes the 'get current branch' guidance shared by pr-review and pr-comments
skills. Resolves the final duplicate-topics assay warning."
```

---

## Task 14: Remove `commands/` from mold and `output.commands` from flux

**Files:**
- Modify: `flux.yaml`
- Delete: `commands/` (should already be empty from Tasks 3-12)

- [ ] **Step 1: Confirm `commands/` directory is empty**

Run: `ls commands/ 2>&1`

Expected: either "No such file or directory" (already removed by `git rm` in earlier tasks) or empty listing. If any file remains, check which task missed it before proceeding.

- [ ] **Step 2: Delete the `commands/` directory from the mold (if still present)**

```bash
# Only if the directory exists after the per-file git rm's
git rm -r commands/ 2>/dev/null || true
```

- [ ] **Step 3: Remove `output.commands` from `flux.yaml`**

Edit `flux.yaml`. Delete the single line: `  commands: .claude/commands`. Verify the surrounding `output:` block still parses: keep `agents`, `agent_config`, `copilot_config`, `skills`, `workflows` entries intact.

- [ ] **Step 4: Run temper --assay across the default flux**

Run: `ailloy temper --assay . 2>&1 | tail -5`

Expected: `Validation passed: 0 errors, 0 warning(s)` at temper level AND `0 error(s), 0 warning(s), 0 suggestion(s)` at assay level.

- [ ] **Step 5: Commit**

```bash
git add flux.yaml
# commands/ directory removal is already staged from earlier tasks
git commit -m "refactor(flux): drop output.commands, remove legacy commands/ directory

The mold no longer emits anything to .claude/commands/. Every former
command is now a skill under .claude/skills/<name>/SKILL.md."
```

---

## Task 15: Bump version, update requires, update README

**Files:**
- Modify: `mold.yaml`
- Modify: `README.md`

- [ ] **Step 1: Bump `mold.yaml` version and requires.ailloy**

Edit `mold.yaml`:

```yaml
apiVersion: v1
kind: mold
name: ailloy-defaults
version: 0.3.0
description: "Agent-agnostic AI development commands, skills, and workflows using the AGENTS.md standard"
author:
  name: Nimble Giant
  url: https://github.com/nimble-giant/ailloy
requires:
  ailloy: ">=X.Y.Z"
```

Where `X.Y.Z` is the first ailloy tag containing commit `3d2f29c` (the `has` template fix). If no tag exists at execution time, use the developer's best pin (e.g., `">=0.7.0"` once released) — confirm the tag with the user before committing. Do **not** pin to a dev-version string like `v0.6.13-7-g3d2f29c`.

- [ ] **Step 2: Update `README.md` — replace commands table with skills table**

In `README.md`, the current "Commands" section (with a table of 10 slash commands) becomes a "Skills" section with the same 10 entries re-labelled as skills. The existing one-line descriptions carry over; just change "Commands" → "Skills" and update the invocation heading.

Also update the skills row in the "This renders and installs" table to reflect that skills now cover what commands used to — and delete the "Commands" row entirely.

- [ ] **Step 3: Update `README.md` — project structure tree**

Replace the `commands/` block in the project structure snippet with the new `skills/<name>/` layout. Reference `references/` and `_shared/` (if used). Roughly:

```text
nimble-mold/
├── mold.yaml
├── flux.yaml
├── flux.schema.yaml
├── agents/
│   └── agents.md
├── agent_config/
│   └── CLAUDE.md
├── copilot_config/
│   └── copilot-instructions.md
├── skills/
│   ├── _shared/
│   │   └── get-current-branch.md
│   ├── brainstorm/
│   │   ├── SKILL.md
│   │   └── references/
│   ├── create-issue/
│   │   ├── SKILL.md
│   │   └── references/
│   ├── init-agents-md/
│   │   └── SKILL.md
│   ├── open-pr/
│   │   └── SKILL.md
│   ├── pr-comments/
│   │   ├── SKILL.md
│   │   └── references/
│   ├── pr-description/
│   │   └── SKILL.md
│   ├── pr-review/
│   │   ├── SKILL.md
│   │   └── references/
│   ├── preflight/
│   │   └── SKILL.md
│   ├── start-issue/
│   │   └── SKILL.md
│   └── update-pr/
│       └── SKILL.md
├── workflows/
│   ├── claude-code.yml
│   └── claude-code-review.yml
└── LICENSE
```

- [ ] **Step 4: Update `README.md` — add "Breaking changes in v0.3.0" section**

Add a short section near the top (just after the "What is this?" section or inside "Quick Start"):

```markdown
## Breaking changes in v0.3.0

- `.claude/commands/` is no longer emitted. All former slash commands are now skills at `.claude/skills/<name>/SKILL.md` — Claude Code invokes them both as `/<name>` and autonomously via the skill description.
- Consumers upgrading from v0.2.x must re-cast:
  ```bash
  ailloy cast github.com/nimble-giant/nimble-mold
  ```
- The outer `agent.targets` conditional on `CLAUDE.md` and `copilot-instructions.md` is removed; both files always emit. Delete whichever you do not need post-cast.
- Minimum `ailloy` version is bumped to the first tag containing the `has` template-function fix.
```

- [ ] **Step 5: Update the version badge in `README.md`**

The badge currently reads `version-0.2.0-orange`. Update to `version-0.3.0-orange`.

- [ ] **Step 6: Run temper --assay; expect 0/0/0**

```bash
ailloy temper --assay .
```

Expected: `0 error(s), 0 warning(s), 0 suggestion(s)`.

- [ ] **Step 7: Commit**

```bash
git add mold.yaml README.md
git commit -m "chore: bump to v0.3.0, update README for skill-first layout

Documents the breaking change, updates the skills table, refreshes the
project-structure tree, and bumps the minimum ailloy version to the tag
containing the has-template-function fix."
```

---

## Task 16: Cross-permutation verification sweep

**Files:** No file modifications; verification only.

- [ ] **Step 1: Assay default flux**

```bash
ailloy temper --assay . 2>&1 | tail -3
```

Expected: `0 error(s), 0 warning(s), 0 suggestion(s)`.

- [ ] **Step 2: Assay with `agent.targets=[copilot]`**

```bash
ailloy temper --assay . --set agent.targets="[copilot]" 2>&1 | tail -3
```

Expected: 0/0/0. (CLAUDE.md now always emits; this is the intended behavior, and assay should no longer see this as empty since the template body is unconditional.)

- [ ] **Step 3: Assay with `agent.targets=[claude,copilot]`**

```bash
ailloy temper --assay . --set agent.targets="[claude,copilot]" 2>&1 | tail -3
```

Expected: 0/0/0.

- [ ] **Step 4: Assay with `agent.targets=[codex]`**

```bash
ailloy temper --assay . --set agent.targets="[codex]" 2>&1 | tail -3
```

Expected: 0/0/0.

- [ ] **Step 5: Assay with `agent.targets=[aider]`**

```bash
ailloy temper --assay . --set agent.targets="[aider]" 2>&1 | tail -3
```

Expected: 0/0/0.

If ANY permutation reports a warning, fix it and re-run **all five** before continuing. No permutation is "accepted" with warnings.

- [ ] **Step 6: Forge (dry-run) on the default flux and spot-check output**

```bash
ailloy forge . 2>&1 | head -40
```

Confirm:
- `.claude/skills/brainstorm/SKILL.md` renders with frontmatter intact and `@references/*` imports literal.
- `.github/copilot-instructions.md` renders non-empty.
- `.claude/commands/` does not appear in output at all.
- `agents.md` renders at repo root.

- [ ] **Step 7: Spot-invoke one skill in a Claude Code scratch session**

In a new temporary directory, run `ailloy cast <path-to-this-mold-repo>` then open Claude Code there. Type `/brainstorm "a test idea"`. Confirm the skill loads, the `@references/freewriting.md` import resolves (the model reads phase 1 content), and the end-to-end flow works. Cancel out of the actual brainstorm — this is an availability check, not a full run.

- [ ] **Step 8: Write a brief release-notes snippet**

Save to `/tmp/v0.3.0-release-notes.md` for pasting into the eventual GitHub release description — NOT committed to the repo per the spec's "no CHANGELOG file" decision:

```markdown
# v0.3.0 — Lint-clean, skill-first

## Breaking changes
- `.claude/commands/` is no longer emitted. All slash commands are now skills at `.claude/skills/<name>/SKILL.md`. Re-cast to migrate.
- `CLAUDE.md` and `.github/copilot-instructions.md` always emit regardless of `agent.targets`. Delete whichever you don't need post-cast.
- Minimum ailloy bumped to <tag>.

## What's new
- Every skill uses progressive disclosure: `SKILL.md` entrypoints under 150 lines, detail in `references/`.
- Shared `skills/_shared/get-current-branch.md` dedups the PR-branch snippet.
- `ailloy temper --assay .` reports 0 errors, 0 warnings, 0 suggestions across all supported `agent.targets` permutations.
```

- [ ] **Step 9: Final git status check, then hand back to the user**

Run: `git status && git log --oneline ailloy-assay-refactor ^main | cat`

Expected: clean working tree; ~14 commits ahead of `main` (one per refactor task plus docs). Report back to the user: warnings resolved, commits summary, and confirmation that `/open-pr` is the next step if they want to ship.

---

## Self-review notes

**Spec coverage check:**
- §1 migrate commands → skills: Tasks 3-12 (one per command) + Task 14 (drop `output.commands`). ✓
- §2 split long files: Tasks 9, 10, 11, 12 (brainstorm, create-issue, pr-comments, pr-review). ✓
- §3 dedup "get current branch": Task 13. ✓
- §4 empty copilot-instructions + latent CLAUDE.md issue: Task 2. ✓
- §5 version bump + README: Task 15. ✓
- §Testing/Verification (5 permutations + forge + scratch cast): Task 16. ✓
- Rollout step 4 (file upstream ailloy issue for conditional emission): **out of scope for this plan** — the spec explicitly marks it as non-blocking follow-up. The user will file it separately; this plan does not include that task.

**Placeholder scan:** `"X.Y.Z"` ailloy pin in Task 15 is a deliberate known-unknown (depends on the upstream release tag), not a plan failure — it is resolved by asking the user at execution time. Task 1 spec-details-lookup is parameterized by the current agentskills.io spec at execution time, also intentional.

**Type / name consistency:** skill slug matches directory name matches frontmatter `name` field for all 10 skills. `@references/<file>.md` is used consistently inside `SKILL.md`. `@../_shared/get-current-branch.md` (or fallback) is consistent across both cross-skill consumers.
