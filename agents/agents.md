# Project AI Instructions

## Development Methodology

**Plan first, build second.** All implementation work begins with analysis and planning. Review requirements, evaluate feasibility, and present a plan for approval before writing any code. Implementation is a separate, deliberate act.

When evaluating ideas, use structured brainstorming: freewriting, cubing (six angles), journalistic questions (5W1H), feasibility/scope/value assessment, hypothesis extraction, and a direct verdict.

When planning tasks, evaluate against **INVEST** criteria (Independent, Negotiable, Valuable, Estimable, Small, Testable) and express acceptance criteria as **BDD specs** (Given/When/Then).

## Git Conventions

- **Commits**: conventional format — `type(scope): description` (types: feat, fix, chore, docs, refactor, epic)
- **PR descriptions**: Summary (1-3 sentences), Changes (bullet list), UAT (test steps)
- **Issue titles**: `type(scope): description` in lowercase

## Code Review Standards

Review against these categories with severity levels (Critical, Major, Minor, Nit):

- **Security**: input validation, injection prevention, auth, secrets, dependencies
- **Performance**: algorithm efficiency, queries, memory, caching
- **Testing**: coverage, edge cases, quality, maintainability
- **Architecture**: SOLID principles, patterns, separation of concerns, API design

## SCM Integration

- Provider: {{scm.provider}}
- CLI: `{{scm.cli}}`
- Base URL: {{scm.base_url}}

## Scope Discipline

When working on a specific issue, stay focused on that issue only. Do not expand scope to related issues, follow cross-references to other work, or search for adjacent issues. All work should be scoped strictly to what the specified issue describes.
