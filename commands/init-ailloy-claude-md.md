# Init Ailloy Claude MD

## Purpose

This command generates a `CLAUDE.md` file in the user's project root that references `agents.md` via the `@agents.md` directive. This allows Claude Code to read the universal AI instructions from `agents.md` while also receiving Claude-specific configuration.

## Command Name

`init-ailloy-claude-md`

## Invocation Syntax

```bash
/init-ailloy-claude-md
```

## Workflow

When this command is invoked:

1. **Check for existing `CLAUDE.md`** in the project root
   - If it exists, warn the user and ask if they want to overwrite it
   - If the user declines, stop

2. **Check for `agents.md`** in the project root
   - If it does not exist, warn the user: "No agents.md found in the project root. The generated CLAUDE.md references @agents.md — you should create or cast an agents.md first."
   - Proceed regardless (the user may add agents.md later)

3. **Generate `CLAUDE.md`** in the project root with the following content:

```markdown
@agents.md

# Claude-Specific Configuration

## Plan Mode

- Use plan mode before implementation to analyze requirements and present a plan
- Present analysis via `ExitPlanMode` for user approval before proceeding

## Commands

Available slash commands are installed in `.claude/commands/`. Use `/help` to list them.

## Skills

Auto-triggered skills are installed in `.claude/skills/`. These activate based on natural language context — no slash prefix needed.

## Reports

Save brainstorm and analysis reports to `.claude/plans/`. Create the directory if it does not exist.
```

4. **Confirm success**: Report that `CLAUDE.md` has been created with `@agents.md` at the top

## Notes

- The `@agents.md` directive at the top of `CLAUDE.md` tells Claude Code to include the contents of `agents.md` as context
- This keeps Claude-specific instructions separate from the universal agent instructions in `agents.md`
- The generated file is a starting point — users should customize it for their project's specific needs
- This command does not modify `agents.md` — it only creates `CLAUDE.md`
