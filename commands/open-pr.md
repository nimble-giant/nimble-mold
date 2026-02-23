# open-pr

Open a pull request for the current branch with automatically generated description, or update existing PR.

## Usage

```
/open-pr [draft]
```

## Options

- `draft` - Creates the PR in draft mode (optional, defaults to ready for review)

## Workflow

When this command is used:

{{- if .agent.plan_mode.enabled }}
1. **{{agent.plan_mode.enter}}** to outline the PR creation/update process
{{- else }}
1. **Outline the PR creation/update process** for user review
{{- end }}

2. **Check for existing PR** on the current branch using `{{pr.check}}`

3. **If PR already exists**:
   - Use `/update-pr` to update the existing PR description
   - Skip to step 6

4. **If no PR exists**:
   - Generate PR description using `/pr-description` command
   - Create new PR using `{{pr.create}}`

5. **Set PR status**:
   - Default: Ready for review
   - With `draft` flag: Draft mode

6. **Display PR URL** for user to review

## Examples

- `/open-pr` - Create PR ready for review
- `/open-pr draft` - Create PR in draft mode

## Integration

This command integrates with:

- `/pr-description` - For generating PR descriptions
- `/update-pr` - For updating existing PRs
- `/gh-issue` - Includes issue references if branch was created for an issue

## Notes

- Automatically detects existing PRs to avoid duplicates
- Uses {{scm.provider}} CLI (`{{scm.cli}}`) for all PR operations
- Enters plan mode for transparency
- Leverages existing `/pr-description` functionality
- Does not include AI attribution in PR descriptions
