---
name: pr-review
description: Conduct a full code review with silent (markdown report) or interactive (posts inline comments) mode. Supports focused reviews by category. Use when the user says "review this PR", "security-review PR 42", or "do a code review".
---
# PR Review

## Purpose

This command helps reviewers conduct comprehensive code reviews efficiently. It analyzes PR changes, generates review feedback, and can either output a markdown review file (silent mode) or post comments directly to the PR (interactive mode with plan approval).

## Command Name

`pr-review`

## Invocation Syntax

```bash
/pr-review <pr-number> [--silent-mode|-s] [--focus=<area>]
/pr-review <pr-url> [--silent-mode|-s] [--focus=<area>]
/pr-review [--silent-mode|-s] [--focus=<area>]
```

**Examples:**

```bash
# Silent mode (default) - generates markdown review file
/pr-review 1234
/pr-review 1234 --silent-mode
/pr-review 1234 -s

# Interactive mode - posts comments to PR after plan approval
/pr-review 1234 --silent-mode=false

# Focus on specific areas
/pr-review 1234 --focus=security
/pr-review 1234 --focus=performance
/pr-review 1234 --focus=testing

# Auto-detect current PR
/pr-review --silent-mode=false
```

**Flags:**

- `--silent-mode` / `-s`: Generate markdown review file only (default: true)
- `--silent-mode=false`: Interactive mode with plan approval and PR commenting
- `--focus=<area>`: Focus review on specific areas (security, performance, testing, architecture, etc.)

## Workflow Overview

Execute this workflow when the command is invoked:

1. **Parse Flags**: Determine silent mode and focus areas
2. **Fetch PR Information**: Get PR details, diff, and context
3. **Analyze Code Changes**: Comprehensive code review analysis
4. **Generate Review**: Create structured review feedback
5. **Output Mode**: Either save markdown file (silent) or enter plan mode (interactive)

## Phase 1: Flag Parsing and Setup

### Step 1.1: Parse Command Flags

Default behavior: `--silent-mode=true`

```javascript
// Parse user input for flags
const flags = {
  silentMode: true,  // Default to silent mode
  focus: null,       // No specific focus by default
  prNumber: null,
  prUrl: null
};

// Check for explicit flags in user input
if (input.includes('--silent-mode=false')) flags.silentMode = false;
if (input.includes('--focus=')) flags.focus = extractFocusArea(input);
```

### Step 1.2: Determine PR Number

If no PR number provided:

```bash
# Get current branch
CURRENT_BRANCH=$(git branch --show-current)

# Find PR for current branch
{{pr.find_for_branch}}
```

## Phase 2: PR Information Gathering

### Step 2.1: Fetch PR Details

```bash
# Get comprehensive PR information
{{pr.view_full}}

# Get PR diff with context
{{pr.diff}}

# Get file list and stats
{{pr.view_files}}

# Get existing reviews and comments
{{api.pr_reviews}}
{{api.pr_review_comments}}
```

### Step 2.2: Analyze Changed Files

For each changed file:

```bash
# Get file content before and after (replace <ref> with base or head branch)
{{api.file_content}}

# Get file history and blame info if needed
git log --oneline -n 10 <file-path>
```

## Phase 3: Code Review Analysis

@references/categories.md

## Phase 4: Review Generation

@references/severity.md

## Phase 5: Output Generation

### Mode A: Silent Mode (Default)

@references/output-template.md

### Mode B: Interactive Mode

@references/posting-workflow.md

## Focus Area Specializations

@references/focus-areas.md

## Review Quality Guidelines, Error Handling, and Integration

@references/guidelines.md
