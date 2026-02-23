{{- if .agent.targets.copilot -}}
# Copilot Instructions

Refer to `agents.md` in the project root for complete development methodology and conventions.

## Key Conventions

- Follow conventional commit format: `type(scope): description`
- Use BDD acceptance criteria (Given/When/Then) for test specifications
- Review code against security, performance, testing, and architecture categories
- Plan before implementing — analyze requirements and present approach before writing code

## SCM

- Provider: {{scm.provider}}
- CLI: `{{scm.cli}}`
{{- end -}}
