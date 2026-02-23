# Init AGENTS.md

## Purpose

This command researches the current project and generates a holistic `AGENTS.md` file following [AGENTS.md best practices](https://agents.md/). If a `CLAUDE.md` file exists, it is used as a source for content. The result is a portable, agent-agnostic instruction file that works across AI coding agents.

## Command Name

`init-agents-md`

## Invocation Syntax

```bash
/init-agents-md
```

## Workflow

When this command is invoked:

### Phase 1: Research the Project

Thoroughly explore the project to understand its structure, conventions, and tooling:

1. **Project overview** — Read `README.md`, `package.json`, `Cargo.toml`, `go.mod`, `pyproject.toml`, or equivalent to identify the tech stack, language(s), frameworks, and purpose
2. **Build & test commands** — Identify how to install dependencies, build, lint, and run tests
3. **Code style** — Look for linter configs (`.eslintrc`, `.prettierrc`, `rustfmt.toml`, etc.), formatting rules, and naming conventions
4. **Project structure** — Map key directories and their roles
5. **Git conventions** — Check for `.commitlintrc`, commit history patterns, branch naming conventions
6. **Existing AI instructions** — Read `CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`, or any existing agent config files
7. **CI/CD** — Check `.github/workflows/`, `Makefile`, `Dockerfile`, or deployment configs
8. **Boundaries** — Identify sensitive files, directories, or patterns that agents should not modify (e.g., `.env`, generated files, vendor directories)

### Phase 2: Draft the AGENTS.md

Generate an `AGENTS.md` file following these guidelines:

**Target length**: Under 150 lines. If the project needs more coverage, use progressive disclosure — link to separate `.md` files for detailed topics.

**Required sections** (adapt headings to fit the project):

```markdown
# AGENTS.md

## Project Overview
[1-3 sentences: what this project is, tech stack with versions, primary language]

## Setup
[Exact commands to install, build, and run]

## Testing
[How to run tests, test conventions, CI expectations]

## Code Style
[Formatting rules, naming conventions, key patterns — use examples over descriptions]

## Project Structure
[Key directories and their roles — keep it scannable]

## Git Conventions
[Commit format, branch naming, PR expectations]

## Boundaries
[What agents should NOT modify, sensitive areas, generated files]
```

**Best practices to follow**:
- Put executable commands early with exact flags
- Use real code examples over abstract descriptions
- Specify exact tech stack versions, not generic tool names
- Include a boundaries section with three tiers: always do, ask first, never do
- Point to real files that demonstrate good patterns
- Call out legacy code or anti-patterns to avoid

**Progressive disclosure**: If the content needed to holistically cover the project exceeds 150 lines, create additional `.md` files (e.g., `AGENTS-testing.md`, `AGENTS-architecture.md`) and link to them from the main `AGENTS.md`. Keep the main file as a scannable overview with pointers to details.

### Phase 3: Handle Existing CLAUDE.md

If a `CLAUDE.md` file exists in the project root:

1. **Source it** — Use the content in `CLAUDE.md` as input when generating `AGENTS.md`. Extract project-specific instructions, conventions, and context from it.
2. **Present the plan** — Show the user the generated `AGENTS.md` content and explain what was sourced from `CLAUDE.md`
3. **Ask about CLAUDE.md** — After the user approves the `AGENTS.md`, ask: "Would you like to replace the contents of `CLAUDE.md` with just `@AGENTS.md` so Claude Code reads from the new file?"
4. **If approved** — Clear `CLAUDE.md` and replace its entire contents with:
   ```
   @AGENTS.md
   ```
5. **If declined** — Leave `CLAUDE.md` as-is

If no `CLAUDE.md` exists, skip this phase entirely.

### Phase 4: Save and Confirm

1. Write the `AGENTS.md` file to the project root
2. Write any additional linked `.md` files if progressive disclosure was used
3. Report what was created and where

## Rules

- **Research first.** Do not generate a generic template. The AGENTS.md must reflect the actual project — its real tech stack, real commands, real structure.
- **Be specific.** Use exact commands, exact file paths, exact versions. Agents need precision, not generalities.
- **Keep it concise.** Under 150 lines for the main file. Use progressive disclosure for depth.
- **Source from existing config.** If `CLAUDE.md` or other agent config files exist, incorporate their content rather than ignoring it.
- **Ask before modifying.** Never overwrite `CLAUDE.md` without explicit user approval.
- **Examples over explanations.** Show real code snippets from the project to illustrate conventions rather than describing them abstractly.

## Notes

- This command creates a project-specific `AGENTS.md` by analyzing the actual codebase — it is not a static template
- The generated file follows the [AGENTS.md standard](https://agents.md/) and is automatically discovered by AI agents that support it (Copilot, Codex, Aider, and others)
- For Claude Code users, the `CLAUDE.md` → `@AGENTS.md` migration ensures Claude reads from the universal file while maintaining the Claude-specific entry point
