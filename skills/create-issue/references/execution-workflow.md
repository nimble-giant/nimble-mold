## Execution Workflow

### Phase 1: Plan Mode

1. **Parse Input**:
   - Extract flags from command (e.g., `-b`, `--board`, `-l`, `--label`)
   - Extract issue description from remaining input
   - Note: No defaults are applied - only use explicitly provided flags
2. **Format Issue**: Create {{scm.provider}} issue using the exact markdown format above
{{- if .agent.plan_mode.enabled }}
3. **Present Plan**: Use `{{agent.plan_mode.exit}}` with the formatted issue as the plan, including parsed settings
{{- else }}
3. **Present Plan**: Present the formatted issue for user review, including parsed settings
{{- end }}
4. **Wait for Approval**: Do not proceed until user explicitly approves

### Phase 2: Issue Creation

#### Mode A: Default Mode (No Prompting)

1. **Extract Components**:
   - Title: First line of the plan (the `# title` line)
   - Body: Everything after the title
2. **Create Issue**:
   - Basic: `{{issue.create}}`
   - With labels: `{{issue.create_with_labels}}`
3. **Add to Project Board** (if specified):
   - Only if `-b` or `--board` flag was provided
   - Execute: `{{issue.add_to_project}}`
4. **Confirm Success**: Report the created issue URL to user

#### Mode B: Interactive Mode (--prompt flag)

If `--prompt` flag is detected:

1. **Ask for Board**: "Would you like to add this issue to a project board? If so, which one?"
2. **Ask for Labels**: "Would you like to add any labels to this issue? (comma-separated)"
3. **Create Issue**: Same as Mode A but with user-provided values
4. **Add Board/Labels**: Apply user-specified board and labels

#### Mode C: Local Mode (--local flag)

If `--local` flag is detected:

1. **Derive filename** from the issue title:
   - Take the title line (e.g. `feat(web): add control family status card`)
   - Convert to lowercase kebab-case, stripping punctuation: `feat-web-add-control-family-status-card`
   - Append `.md` extension
2. **Ensure `plans/` directory exists**: Create it if not present (`mkdir -p plans`)
3. **Write the file**: Save the full formatted plan (title + body) to `plans/<filename>.md`
4. **Confirm**: Report the saved file path to the user (e.g. `plans/feat-web-add-control-family-status-card.md`)
5. **Do NOT** run any `{{scm.cli}}` commands — no {{scm.provider}} issue is created

## Default Behaviors

When no flags are specified:

- Create a basic {{scm.provider}} issue with title and description only
- Not add to any project board
- Not apply any labels
- Not prompt for any configuration

This ensures a streamlined experience where issues are created immediately after plan approval.

## Interactive Mode (--prompt)

When the `--prompt` flag is used:

- Ask if you want to add the issue to a project board
- Ask if you want to add any labels to the issue
- List available options when relevant

This provides flexibility for users who prefer to configure settings interactively or are unsure about their repository's configuration.

## Local Mode (--local)

When the `--local` flag is used, Claude will:

- Save the formatted issue as a markdown file in the `plans/` directory
- Derive the filename from the issue title in kebab-case (e.g. `plans/feat-web-add-new-feature.md`)
- Create the `plans/` directory if it does not exist
- Skip all {{scm.provider}} CLI commands — nothing is published or shared

This is useful for keeping early-stage planning private or for drafting issues before deciding to publish them. The saved files are compatible with `/start-issue --local` for beginning implementation directly from a local plan.

## Error Handling

- If `{{scm.cli}}` command fails, report the error and suggest checking {{scm.provider}} CLI authentication
- If project board doesn't exist, ask user to verify the board name or skip board assignment
- If title format is invalid, reformat according to rules automatically
- If labels don't exist, ask user to verify label names or skip label assignment
