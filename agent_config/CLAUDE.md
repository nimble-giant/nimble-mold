{{- if has "claude" .agent.targets -}}
@agents.md

# Claude-Specific Configuration

## Plan Mode

- Use plan mode before implementation to analyze requirements and present a plan
- Present analysis via `{{agent.plan_mode.exit}}` for user approval before proceeding

## Commands

Available slash commands are installed in `.claude/commands/`. Use `/help` to list them.

## Skills

Auto-triggered skills are installed in `.claude/skills/`. These activate based on natural language context — no slash prefix needed.

## Reports

Save brainstorm and analysis reports to `{{agent.plans_dir}}`. Create the directory if it does not exist.
{{- end -}}
