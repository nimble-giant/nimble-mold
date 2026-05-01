## {{scm.provider}} CLI Commands

### Basic Issue Creation

```bash
# Create a basic issue
{{issue.create}}

# Create issue with labels
{{issue.create_with_labels}}

# Add to project board after creation (if your team uses boards)
{{issue.add_to_project}}
```

### Available {{scm.provider}} CLI Options

- `--title`: Issue title (required)
- `--body`: Issue description (required)
- `--label`: Comma-separated list of labels (optional)
- `--assignee`: Assign to specific users (optional)
- `--milestone`: Add to milestone (optional)

**Note**: Project board integration varies by repository configuration. Some teams use {{scm.provider}} Projects, others use different systems, and some don't use boards at all.

```bash
# List available project boards
{{project.list}}

# Step 1: Create issue WITHOUT project assignment
{{issue.create}}

# Step 2: Add to project board using project NAME (not number)
{{issue.add_to_project}}

# Common project names:
# - "{{project.board}}" (default)
# - "Tech Debt"
# - "Compliance"
# - "Webhooks"
# - "Reporting"
```

{{- if or .ore.status.enabled .ore.priority.enabled .ore.iteration.enabled}}

## Project Field Management

### {{.project.board}} Board Field IDs

- Project ID: `{{.project.id}}`
{{- if .ore.status.enabled}}
- Status Field ID: `{{.ore.status.field_id}}`
{{- end}}
{{- if .ore.priority.enabled}}
- Priority Field ID: `{{.ore.priority.field_id}}`
{{- end}}
{{- if .ore.iteration.enabled}}
- Iteration Field ID: `{{.ore.iteration.field_id}}`
{{- end}}
{{- if .ore.status.enabled}}

**Status Options:**

{{range $key, $opt := .ore.status.options -}}
- {{$opt.label}}{{if $opt.id}}: `{{$opt.id}}`{{end}}
{{end}}

```bash
# Set Status
{{api.base}} graphql --field query='
mutation {
  updateProjectV2ItemFieldValue(
    input: {
      projectId: "{{.project.id}}"
      itemId: "<ITEM_ID>"
      fieldId: "{{.ore.status.field_id}}"
      value: {
        singleSelectOptionId: "<OPTION_ID>"
      }
    }
  ) {
    projectV2Item { id }
  }
}'
```

{{- end}}
{{- if .ore.priority.enabled}}

**Priority Options:**

{{range $key, $opt := .ore.priority.options -}}
- {{$opt.label}}{{if $opt.id}}: `{{$opt.id}}`{{end}}
{{end}}

```bash
# Set Priority
{{api.base}} graphql --field query='
mutation {
  updateProjectV2ItemFieldValue(
    input: {
      projectId: "{{.project.id}}"
      itemId: "<ITEM_ID>"
      fieldId: "{{.ore.priority.field_id}}"
      value: {
        singleSelectOptionId: "<OPTION_ID>"
      }
    }
  ) {
    projectV2Item { id }
  }
}'
```

{{- end}}
{{- end}}

### Flag Parsing Rules

- Short flags can be combined: `-bl bug` equals `-b board -l bug`
- Long flags require equals or space: `--board="My Board"` or `--board "My Board"`
- Flags must come before the issue description
- Unknown flags are treated as part of the description
- `--local` is mutually exclusive with `--board`, `--label`, and `--prompt` (local mode does not interact with {{scm.provider}})

## Recap

Use this command for creating {{scm.provider}} issues efficiently:

- **Default mode**: Issues are created immediately after plan approval with sensible defaults
- **Flag overrides**: Use flags when you need specific values (e.g., `-b "Tech Debt" -p P0`)
- **Interactive mode**: Use `--prompt` flag when you need full control or are unsure of settings
- **Local mode**: Use `--local` flag to save the plan as a private markdown file in `plans/` without creating a {{scm.provider}} issue

The command balances automation with flexibility, defaulting to streamlined execution while allowing interactive configuration when needed.
