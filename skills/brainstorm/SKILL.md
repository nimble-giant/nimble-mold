---
name: brainstorm
description: Seven-phase structured brainstorming that analyzes an idea for feasibility, scope, and value and saves a report — never generates code. Use when the user says "brainstorm X", "is this worth building?", "analyze this idea", "pressure-test this", or "help me think through this".
---

# Skill: Brainstorm

## What This Skill Does

This skill applies structured brainstorming techniques to systematically analyze an idea for feasibility, scope, and value. It draws from the [UNC Writing Center's brainstorming methodology](https://writingcenter.unc.edu/tips-and-tools/brainstorming/), adapted for evaluating software and product ideas.

The skill produces a saved analysis report — not implementation. The user decides independently whether and when to act on the findings.

## When to Use This Skill

Use this skill whenever the user wants to brainstorm, evaluate, or pressure-test an idea. This includes requests like:
- "Brainstorm X"
- "Is this idea worth building?"
- "Analyze whether we should do X"
- "Help me think through this idea"
- "What do you think about building X?"

## Workflow

Follow these steps in order:

### Step 1: Begin Analysis

{{- if .agent.plan_mode.enabled }}
{{agent.plan_mode.enter}} immediately. The brainstorm output IS the plan.
{{- else }}
Begin the structured analysis. The brainstorm output will be presented for user review.
{{- end }}

### Step 2: Research

Before producing the brainstorm, gather relevant context:

- **Codebase context**: If the idea relates to the current project, explore the relevant code, architecture, and existing patterns
- **Web research**: Search for prior art, existing tools, competing solutions, and relevant technical concepts
- **Feasibility signals**: Look for libraries, APIs, or frameworks that would support or complicate the idea

Do not fabricate comparisons or claim prior art exists without verifying. If you cannot find relevant prior art, say so.

### Step 3: Execute All Seven Brainstorming Phases

Work through every phase in order. Each phase builds on the previous one. Do not skip phases.

@references/freewriting.md

@references/cubing.md

@references/journalistic.md

@references/feasibility.md

@references/hypothesis.md

@references/proposals.md

@references/verdict.md

### Step 4: Present the Plan

{{- if .agent.plan_mode.enabled }}
Present the complete brainstorm analysis via `{{agent.plan_mode.exit}}` using the output template:
{{- else }}
Present the complete brainstorm analysis for user review using the output template:
{{- end }}

@references/output-template.md

### Step 5: Save the Report and Stop

After user approval, save the brainstorm report to `{{agent.plans_dir}}`. Create the directory if it does not exist.

Name the file `brainstorm-{slug}.md` where `{slug}` is a kebab-case summary of the idea (3-5 words max).

**Stop after saving.** Do not begin implementation or prompt toward it.

## Rules

{{- if .agent.plan_mode.enabled }}
- Always enter plan mode. The brainstorm output IS the plan.
{{- else }}
- Always present the brainstorm analysis for review before saving.
{{- end }}
- Do not skip phases. Every phase must appear in the output.
- Be direct. Avoid hedge words like "potentially," "might," "could possibly."
- Be honest. If the idea is bad, say so in the verdict.
- Stay grounded. Every claim should trace back to something concrete from the analysis.
- Keep it concise. Thorough but not verbose. The entire brainstorm should be scannable.
- **No implementation.** This skill produces documentation and analysis only. It must never write application code, create features, or modify project source files.
- **Stop after the report.** Do not ask "should I start building this?" or otherwise prompt toward implementation.
