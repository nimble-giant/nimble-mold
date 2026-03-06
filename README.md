<div align="center">

# nimble-mold

### The official [ailloy](https://github.com/nimble-giant/ailloy) mold for AI-assisted development.

**Curated commands. Opinionated workflows. Zero config to start.**

[![ailloy >=0.2.0](https://img.shields.io/badge/ailloy-%3E%3D0.2.0-blue?style=flat-square)](https://github.com/nimble-giant/ailloy)
[![License](https://img.shields.io/badge/license-Apache--2.0-green?style=flat-square)](LICENSE)
[![Version](https://img.shields.io/badge/version-0.1.10-orange?style=flat-square)](mold.yaml)

</div>

---

## What is this?

**nimble-mold** is a batteries-included mold (template package) for [ailloy](https://github.com/nimble-giant/ailloy) — the package manager for AI instructions. It gives your project a complete, production-ready toolkit of Claude Code commands, skills, and GitHub Actions workflows designed around agile engineering practices.

Think of it this way: ailloy is Helm, and nimble-mold is the official chart. Cast it into your project and get an instant, standardized AI-assisted development workflow — from brainstorming ideas to shipping pull requests.

## Why nimble-mold?

Most teams bolt AI onto their workflow as an afterthought. nimble-mold treats AI collaboration as a first-class engineering discipline:

- **Structured over ad-hoc** — Commands enforce proven techniques (INVEST criteria, BDD acceptance specs, 7-phase brainstorming) so you get consistent, high-quality output every time.
- **Plan before you build** — Pre-flight checks and brainstorm sessions ensure you think before you code. Every command that touches implementation enters plan mode first.
- **GitHub-native** — Deep integration with `gh` CLI, GitHub Actions, and GitHub Projects. Issues, PRs, reviews, and project boards all speak the same language.
- **Agile by default** — Conventional commits, BDD specs, INVEST evaluation, sprint-aligned workflows. The mold encodes agile best practices without the ceremony overhead.

## Quick Start

### Prerequisites

- [ailloy](https://github.com/nimble-giant/ailloy) >= 0.2.0
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- [GitHub CLI](https://cli.github.com/) (`gh`) authenticated

### Install

```bash
# Cast the mold into your project
ailloy cast github.com/nimble-giant/nimble-mold
```

This renders and installs:

| Artifact | Destination | Description |
|----------|-------------|-------------|
| Commands | `.claude/commands/` | 9 Claude Code slash commands |
| Skills | `.claude/skills/` | Auto-triggered Claude Code skills |
| Workflows | `.github/workflows/` | GitHub Actions for CI-driven AI |

That's it. Start using the commands immediately.

## Commands

Every command is a structured Claude Code instruction that guides AI behavior through well-defined phases. No prompt engineering required — just invoke and go.

| Command | What it does |
|---------|-------------|
| `/brainstorm <idea>` | 7-phase structured analysis of any idea. Freewriting, cubing, journalistic questions, feasibility assessment, hypothesis extraction, solution proposals, and a direct go/no-go verdict. Saves a report — never generates code. |
| `/preflight` | Pre-flight planning ceremony. Evaluates work against INVEST criteria, generates BDD acceptance specs (Given/When/Then), sketches architecture, and presents a go/no-go decision before any code is written. |
| `/start-issue <number>` | Fetches a GitHub issue and begins focused implementation. Builds a task list from requirements and stays scoped to that single issue. |
| `/create-issue` | Creates well-formatted GitHub issues with conventional commit-style titles (`feat`, `fix`, `chore`, `docs`, `refactor`, `epic`). Supports labels and project board assignment. |
| `/open-pr` | Opens a new pull request or updates an existing one. Auto-generates descriptions, detects duplicates, and supports draft mode. |
| `/update-pr` | Regenerates the PR description for the current branch from scratch for accuracy. |
| `/pr-description` | Generates a comprehensive PR description by diffing the current branch against `main`. Saves to `/pr-descriptions/` for review before submission. |
| `/pr-review` | Conducts a full code review. Silent mode (default) outputs a markdown report. Interactive mode posts inline comments to the PR after plan approval. Supports focused reviews: security, performance, testing, or architecture. |
| `/pr-comments` | Fetches all PR review comments, plans strategic responses, makes code changes, and posts replies — all in one pass. |

## Skills

Skills are auto-triggered capabilities that activate based on natural language context.

| Skill | Triggers on |
|-------|------------|
| **Brainstorm** | "brainstorm X", "is this idea worth building?", "help me think through this", "evaluate this concept" |

Skills run the same structured methodology as their command counterparts but activate conversationally — no slash prefix needed.

## Workflows

Pre-built GitHub Actions that bring Claude into your CI/CD pipeline.

### `claude-code.yml` — Agentic Responder

Triggers when `@claude` is mentioned in:
- Issue comments
- PR review comments
- PR reviews
- Newly assigned issues

Claude checks out the repo, reads CI results, and responds with full context.

### `claude-code-review.yml` — Automated PR Review

Triggers on:
- PR opened or synchronized
- PR comments mentioning Claude

Maintains a **single summary review comment** per PR (updated on each push) and creates **reply comments** for direct mentions. Reviews include collapsible detail sections so feedback stays scannable.

## The AI-Embedded SDLC

nimble-mold is not a grab-bag of prompts. It's an opinionated software development lifecycle that weaves AI into every phase — from the first spark of an idea to merged code in `main`. Each command maps to a phase, and the phases connect into a repeatable cycle.

```
  Ideate ──► Plan ──► Track ──► Build ──► Ship ──► Review ──► Respond
    │                                                            │
    ◄────────────────────────────────────────────────────────────┘
```

### Phase 1: Ideate — `/brainstorm`

**When you have an idea and need to know if it's worth building.**

```bash
/brainstorm a service that monitors GitHub Actions costs and suggests optimizations
```

The brainstorm command runs a 7-phase structured analysis: freewriting, cubing (examining the idea from six angles), journalistic questions, feasibility/scope/value assessment, hypothesis extraction, solution proposals, and a direct verdict. It saves a report to `.claude/plans/` and stops. No code is generated — this is a thinking tool.

You can also trigger brainstorming conversationally. Just say "help me think through this" or "is this idea worth building?" and the brainstorm skill activates automatically.

**Output**: A saved analysis report with a go/no-go verdict.
**Next step**: If the verdict is "worth pursuing," move to planning.

### Phase 2: Plan — `/preflight`

**When you're ready to commit to a piece of work and need to plan it.**

```bash
/preflight
/preflight 42
```

Preflight is the planning ceremony. It evaluates the work against INVEST criteria (Independent, Negotiable, Valuable, Estimable, Small, Testable), generates BDD acceptance specs in Given/When/Then format, sketches architecture if helpful, and presents a go/no-go decision. Everything happens in plan mode — you approve before anything changes.

Run preflight standalone for ad-hoc work, or pass an issue number to plan against a specific ticket.

**Output**: INVEST evaluation, acceptance criteria, optional design sketch.
**Next step**: Create an issue to track the work.

### Phase 3: Track — `/create-issue`

**When planning is done and you need a trackable work item.**

```bash
/create-issue feat(api): add rate limiting middleware
/create-issue --board "Sprint 4" -l enhancement fix(web): form validation on checkout
```

Creates well-formatted GitHub issues with conventional commit-style titles. The command enters plan mode, formats the issue with a clear summary and acceptance criteria (checkboxes), presents it for your approval, then creates it via `gh`. Supports labels, project board assignment, and an interactive `--prompt` mode for teams with complex configurations.

**Output**: A GitHub issue with structured acceptance criteria.
**Next step**: Start implementation.

### Phase 4: Build — `/start-issue`

**When the issue exists and you're ready to write code.**

```bash
/start-issue 42
```

Fetches the GitHub issue, builds a task list from its requirements, and begins focused implementation. The key constraint: it stays scoped to that single issue. It won't follow references to other issues, expand scope, or wander. One issue, one unit of work.

**Output**: Working code scoped to the issue's acceptance criteria.
**Next step**: Open a pull request.

### Phase 5: Ship — `/open-pr`

**When the code is ready for review.**

```bash
/open-pr
/open-pr draft
```

Opens a pull request with an auto-generated description. It diffs your branch against `main`, generates a summary with changes and UAT instructions, detects if a PR already exists (and updates it instead of creating a duplicate), and supports draft mode. Titles follow conventional commit format.

If you just need the description without opening the PR, use `/pr-description` to save it for review first. Use `/update-pr` to regenerate the description after pushing more commits.

**Output**: A pull request on GitHub ready for review.
**Next step**: Get it reviewed.

### Phase 6: Review — `/pr-review`

**When a PR needs a thorough code review.**

```bash
/pr-review 57
/pr-review 57 --focus=security
/pr-review 57 --silent-mode=false
```

Conducts a comprehensive code review covering code quality, security, performance, testing, and architecture. In silent mode (default), it saves a detailed markdown report. In interactive mode (`--silent-mode=false`), it enters plan mode, presents each finding for your approval, and posts inline comments directly to the PR.

Focus flags let you narrow the review: `--focus=security`, `--focus=performance`, `--focus=testing`, or `--focus=architecture`.

**Output**: Review findings as a saved report or PR comments.
**Next step**: Address the feedback.

### Phase 7: Respond — `/pr-comments`

**When review comments are in and you need to address them.**

```bash
/pr-comments 57
```

Fetches all PR review comments, enters plan mode, and builds a response strategy for each one. For every comment, you choose: make code changes, reply with an explanation, ask for clarification, or mark as won't-fix. After you approve the plan, it makes the code changes, runs tests, commits, pushes, and posts replies to each comment — all in one pass.

**Output**: Code changes committed, replies posted, PR updated.
**Next step**: Merge or iterate.

### Continuous: Automated CI/CD

The two GitHub Actions workflows run alongside the manual commands, providing automated AI participation in your GitHub workflow.

**`claude-code.yml` (Agentic Responder)** — Mention `@claude` in any issue comment, PR review comment, or PR review, and Claude responds with full repo context. Also triggers on newly assigned issues.

**`claude-code-review.yml` (Automated PR Review)** — Every PR gets an automated review comment. Claude maintains a single summary comment per PR (updated on each push) with key findings in collapsible sections. Mention `@claude` in a PR comment for a direct reply.

### Putting It All Together

Here's what a typical cycle looks like in practice:

```bash
# 1. You have an idea — pressure-test it
/brainstorm a webhook retry system with exponential backoff

# 2. Verdict is "worth pursuing" — plan the work
/preflight

# 3. Planning looks good — create the issue
/create-issue feat(webhooks): add retry system with exponential backoff

# 4. Issue #42 is created — start building
/start-issue 42

# 5. Code is ready — open the PR
/open-pr

# 6. Review the PR (or let CI do it automatically)
/pr-review 57

# 7. Address review feedback
/pr-comments 57

# 8. Merge. Repeat.
```

Not every task needs every phase. A quick bug fix might skip straight to `/start-issue` and `/open-pr`. A complex feature might run `/brainstorm` twice before settling on an approach. Use the phases that fit the work.

## Configuration

nimble-mold is configurable through ailloy's standard flux system. Override defaults at cast time:

```bash
# Cast with custom project board settings
ailloy cast github.com/nimble-giant/nimble-mold \
  --set project.organization=your-org \
  --set project.board=your-board
```

### Configurable surfaces

| Area | What you can tune |
|------|-------------------|
| **Project Board** | Organization, board name, project ID |
| **Ore Fields** | Status, priority, and iteration field mappings for GitHub Projects |
| **SCM** | Base URL, CLI tool, provider |
| **API Templates** | GitHub API command patterns for reviews, comments, issues |

See [`flux.yaml`](flux.yaml) for all available configuration keys and [`flux.schema.yaml`](flux.schema.yaml) for the full schema with discovery commands.

## Project Structure

```
nimble-mold/
├── mold.yaml              # Package manifest (name, version, requirements)
├── flux.yaml              # Configuration defaults & API templates
├── flux.schema.yaml       # Configuration schema with auto-discovery
├── commands/              # Claude Code slash commands
│   ├── brainstorm.md
│   ├── create-issue.md
│   ├── open-pr.md
│   ├── pr-comments.md
│   ├── pr-description.md
│   ├── pr-review.md
│   ├── preflight.md
│   ├── start-issue.md
│   └── update-pr.md
├── skills/                # Auto-triggered Claude Code skills
│   └── brainstorm.md
├── workflows/             # GitHub Actions workflow templates
│   ├── claude-code.yml
│   └── claude-code-review.yml
└── LICENSE
```

## Design Principles

1. **Plan first, build second.** Commands that touch implementation always enter plan mode. You review and approve before anything changes.
2. **Artifacts over actions.** Brainstorming and analysis produce saved documents, not code. Implementation is a separate, deliberate act.
3. **Convention over configuration.** Sensible defaults out of the box. Conventional commits, BDD specs, INVEST evaluation — all baked in.
4. **Transparency over magic.** Every command shows its reasoning. Every workflow posts visible feedback. Nothing runs in the dark.

## Requirements

| Dependency | Version |
|------------|---------|
| [ailloy](https://github.com/nimble-giant/ailloy) | >= 0.2.0 |
| [Claude Code](https://docs.anthropic.com/en/docs/claude-code) | Latest |
| [GitHub CLI](https://cli.github.com/) | Latest (authenticated) |

## License

[Apache License 2.0](LICENSE)

---

<div align="center">

Built by [Nimble Giant](https://github.com/nimble-giant)

*Cast the mold. Ship the code.*

</div>
