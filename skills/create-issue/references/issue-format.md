## Rules & Style Guide

### Title Format

- **Required pattern**: `{type(scope): description}` in all lowercase
- **Supported types**: `feat`, `fix`, `chore`, `docs`, `refactor`, `epic` (see .commitlintrc.yaml for complete list)
- **Scope format**: Parentheses with component name, e.g., `(web)`, `(api)`, `(ssp)`
- **Description**: Brief, actionable summary

### Content Guidelines

- Use standard markdown heading syntax (`#`, `##`)
- Default to "Acceptance Criteria" for features/fixes, "Requirements" for epics
- Use consistent voice, past tense only for bugs
- Always rewrite user input for clarity, grammar, and consistency
- Include context and motivation, not just what to build

### {{scm.provider}} CLI Requirements

- Title must be passed to `--title` parameter
- Body content (everything after title) goes to `--body` parameter
- Project assignment uses `{{issue.add_to_project}}`

---

## Issue Format Examples

### Example 1 (feature)

```markdown
# feat(web): add ssp section to product grid cards

Add a new **System Security Plan (SSP)** section to each product card in the product grid. This section should:

- Display whether an SSP exists for the product
- Include a button labeled "Analyze" to trigger SSP analysis

Also update the glossary to define:
- What "SSP" is
- What "Analyze" means in this context

## Acceptance Criteria

- [ ] Each product card displays SSP status
- [ ] "Analyze" button is present and functional
- [ ] Glossary includes definitions for "SSP" and "Analyze"
```

### Example 2 (chore)

```markdown
# chore(aws): configure ecr scan policy for sandbox vs prod environments

We hit our ECR scan limits in sandbox, which blocked scan visibility before a release. To avoid this:

- Disable **automatic scanning** in sandbox
- Keep it **enabled** in production to meet AWS Marketplace requirements
- Add a manual scan trigger via CLI or CD pipeline stage in sandbox

## Acceptance Criteria

- [ ] Automatic ECR scanning disabled in sandbox
- [ ] ECR scanning enabled in prod remains untouched
- [ ] CD pipeline optionally supports JIT scan step in sandbox
```
