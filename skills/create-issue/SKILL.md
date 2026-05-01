---
name: create-issue
description: Create well-formatted GitHub issues with conventional-commit-style titles, labels, and project-board assignment. Use when the user says "create an issue", "file a ticket", or "open a GitHub issue".
---
# Create Github Issue

## Purpose

This command generates {{scm.provider}} issues with a clean, actionable, well-structured format. It is optimized for creating consistent {{scm.provider}} issues for engineering planning, with consistent formatting across `feat`, `fix`, `chore`, `docs`, and `epic` types.

## Command Name

`create-issue`

## Invocation Syntax

You can invoke this command with optional flags:

```bash
/create-github-issue [flags] <description>
```

### Flags

- `-b, --board <name>`: Specify project board (default: "{{project.board}}")
- `-l, --label <label>`: Add labels to the issue (can be used multiple times)
- `--prompt`: Enable interactive mode (prompts for board and labels if supported)
- `--local`: Save the issue as a local markdown file in the `plans/` directory instead of creating a {{scm.provider}} issue

### Examples

```bash
# Basic issue creation (no board or labels)
/create-github-issue feat(web): add control family status card

# With project board
/create-github-issue --board "Tech Debt" fix(api): memory leak in cache

# With labels
/create-github-issue -l bug -l priority:high fix(security): patch vulnerability

# Multiple labels and board
/create-github-issue -b "Backend" -l enhancement -l good-first-issue feat(api): add new endpoint

# Interactive mode for teams with complex configurations
/create-github-issue --prompt feat(web): new feature requiring custom setup

# Save locally as a markdown file in plans/ (private, no GitHub issue created)
/create-github-issue --local feat(web): new feature to plan privately
```

{{- if .agent.plan_mode.enabled }}
1. Immediately {{agent.plan_mode.enter}} when this command is invoked
2. Parse the user input to extract flags and issue description
3. Format the {{scm.provider}} issue using the exact markdown structure below
4. Use {{agent.plan_mode.exit}} to present the formatted issue as the plan
5. Wait for user approval before proceeding
6. After approval:
   - If `--prompt` flag is present: Use interactive mode (ask for board and labels if repository supports them)
   - Otherwise: Use parsed flags only (no prompting, no automatic assignments)
7. Execute the {{scm.provider}} CLI commands to create the issue with configured settings
{{- else }}
1. Parse the user input to extract flags and issue description
2. Format the {{scm.provider}} issue using the exact markdown structure below
3. Present the formatted issue for user review
4. Wait for user approval before proceeding
5. After approval:
   - If `--prompt` flag is present: Use interactive mode (ask for board and labels if repository supports them)
   - Otherwise: Use parsed flags only (no prompting, no automatic assignments)
6. Execute the {{scm.provider}} CLI commands to create the issue with configured settings
{{- end }}

## Plan Mode Output Format

Present the issue using this exact structure:

```markdown
# {type(scope): short description in lowercase}

{Expanded context from user, rewritten as a clear summary. Explain what is being built, changed, or fixed. Be concise but complete. Include motivations and stakeholder context if relevant. Use bullet points where useful.}

## Acceptance Criteria

- [ ] {Itemized criteria, actions, or functional changes that define "done"}
- [ ] {Use checkboxes for each}
- [ ] {Group logically if needed with subheadings}

## Notes (optional)

- {Additional clarifications, decisions, references, or design choices}
```

**Critical**: The title format must be `{type(scope): description}` in all lowercase for {{scm.provider}} CLI compatibility.

---

@references/issue-format.md
@references/execution-workflow.md
@references/cli-commands.md
