# Start Issue

Fetch a {{scm.provider}} issue and begin implementation.

## Usage

```
/start-issue <issue-number>
/start-issue <issue-url>
/start-issue --local <filename>
/start-issue --local
```

## Flags

- `--local [filename]`: Read from a local markdown file in the `plans/` directory instead of fetching a {{scm.provider}} issue. If `filename` is omitted, list available plans and prompt the user to choose one.

## Examples

- `/start-issue 1234` - Start work on issue #1234
- `/start-issue {{scm.base_url}}/your-org/your-repo/issues/1234` - Start work using full URL
- `/start-issue --local feat-web-add-control-family-status-card` - Start work from a local plan file
- `/start-issue --local` - List available local plans and choose one

## Workflow

When this command is used:

### GitHub Mode (default)

1. **Fetch the issue details** using `{{issue.view}}`

2. **Create a todo list** with tasks derived from the issue requirements

3. **Begin implementation** following the issue requirements and acceptance criteria

4. **Use issue context** to inform commit messages and implementation approach

### Local Mode (--local flag)

1. **Resolve the plan file**:
   - If a filename was provided, look for `plans/<filename>.md` (`.md` extension may be omitted)
   - If no filename was provided, list all `*.md` files in the `plans/` directory and ask the user to choose one
2. **Read the local plan file** and use it as the issue specification
3. **Create a todo list** with tasks derived from the plan requirements
4. **Begin implementation** following the plan's requirements and acceptance criteria
5. **Use the plan filename** to inform commit messages in place of an issue number

## Scope Boundaries

**IMPORTANT: Stay focused on the specified issue ONLY.**

- Do NOT search for, fetch, or work on related issues, sub-issues, parent issues, or adjacent issues
- Do NOT follow `#XXXX` references in the issue body to other issues
- Do NOT use `{{issue.list}}` or `{{issue.search}}` to discover other issues
- If the issue description mentions other issues for context, note them but do NOT expand scope to include their requirements
- All work should be scoped strictly to what the specified issue describes

## Manual {{scm.provider}} Issue Commands

### Fetching Issue Details

```bash
{{issue.view}}
```

## Notes

- Authentication handled through user's {{scm.provider}} CLI login
- Use `#<issue-number>` format in commit messages for GitHub issues
- For local plans, reference the plan filename in commit messages (e.g. `plans/feat-web-add-thing`)
- Focus on rapid implementation of the single issue
- Local plan files are created by `/create-issue --local` and stored in `plans/`
