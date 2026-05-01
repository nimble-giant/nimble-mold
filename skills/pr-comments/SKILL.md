---
name: pr-comments
description: Fetch all PR review comments, plan strategic responses, apply code changes, and post replies in one pass. Use when the user says "respond to PR comments", "address the review", or "reply to the feedback on PR 42".
---
# Respond to PR Comments

## Purpose

This command helps PR authors respond to review comments efficiently. It fetches PR details and comments using {{scm.provider}} CLI, enters plan mode to strategically address each comment, and executes the response plan with code changes and comment replies.

## Command Name

`pr-comments`

## Invocation Syntax

```
/pr-comments <pr-number>
/pr-comments <pr-url>
/pr-comments
```

Examples:

- `/pr-comments 1234` - Address comments on PR #1234
- `/pr-comments {{scm.base_url}}/owner/repo/pull/1234` - Address comments using full URL
- `/pr-comments` - Address comments on current branch's PR (auto-detect)

## Workflow Overview

Execute this workflow when the command is invoked:

1. **Fetch PR Information**: Get PR details and all comments using {{scm.provider}} CLI
{{- if .agent.plan_mode.enabled }}
2. **{{agent.plan_mode.enter}}**: Present all comments and build response strategy
{{- else }}
2. **Present all comments** and build response strategy for user review
{{- end }}
3. **Interactive Planning**: For each comment, ask user how to respond
4. **Consolidate Plan**: Present final response plan for approval
5. **Execute Plan**: Make code changes and post replies as approved

## Phase 1: PR Information Gathering

### Step 1.1: Determine PR Number

If no PR number provided:

```bash
# Get current branch
CURRENT_BRANCH=$(git branch --show-current)

# Find PR for current branch
{{pr.find_for_branch}}
```

If URL provided, extract the PR number from the URL.

### Step 1.2: Fetch PR Details

```bash
# Get PR information
{{pr.view}}

# Get PR comments (review comments on code)
{{pr.view_reviews}}

# Get detailed review comments
{{api.pr_review_comments}}

# Get general PR conversation comments
{{api.pr_conversation_comments}}
```

### Step 1.3: Parse and Organize Comments

Group comments by:

- **Review comments**: Line-specific code feedback
- **General comments**: Overall PR discussion
- **Resolved comments**: Already addressed
- **Pending comments**: Require response

Extract for each comment:

- Author and timestamp
- Comment body/content
- File and line context (for review comments)
- Comment ID for replies
- Resolution status

## Phase 2: Plan Mode - Comment Response Strategy

### Step 2.1: Enter Plan Mode

{{- if .agent.plan_mode.enabled }}
Immediately {{agent.plan_mode.enter}} and present all pending comments organized by type and priority.
{{- else }}
Present all pending comments organized by type and priority for user review.
{{- end }}

### Steps 2.2–2.3: Present Comments and Interactive Planning

@references/response-strategies.md

## Phase 3: Plan Consolidation and Approval

@references/plan-consolidation.md

## Phase 4: Plan Execution

@references/execution.md

## Comment Reply Templates

@references/reply-templates.md

## Error Handling, Security, and Integration

@references/error-handling.md
