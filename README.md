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
| Commands | `.claude/commands/` | 10 Claude Code slash commands |
| Skills | `.claude/skills/` | Auto-triggered Claude Code skills |
| Workflows | `.github/workflows/` | GitHub Actions for CI-driven AI |

That's it. Start using the commands immediately.

## Commands

Every command is a structured Claude Code instruction that guides AI behavior through well-defined phases. No prompt engineering required — just invoke and go.

| Command | What it does |
|---------|-------------|
| `/architect [--adr] <goal>` | 6-phase structured system design. Discovers codebase context, identifies gaps, then works through requirements, components, data, interfaces, cross-cutting concerns, and tradeoff analysis. Optionally generates ADR files. Saves a design document — never generates code. |
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
| **System Design** | "design the architecture for X", "how should we structure this?", "help me think through the technical design", "plan the system design" |

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
│   ├── architect.md
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
│   ├── brainstorm.md
│   └── system-design.md
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
