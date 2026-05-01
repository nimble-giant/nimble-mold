If `--silent-mode=false`, enter plan mode:

### Step 5.1: Enter Plan Mode

Present review findings in plan mode for user approval:

```markdown
# PR Review Plan for #<number>: <title>

## Review Summary
- **Total Issues Found**: X
- **Critical**: Y (require immediate action)
- **Major**: Z (should be addressed)
- **Minor**: A (suggestions for improvement)

## Proposed Comments

### Critical Issue 1
**File**: `src/api/users.js:42`
**Type**: Security Vulnerability
**Comment**: "🚨 **Security Issue**: This code is vulnerable to SQL injection. User input should be sanitized before database queries."

**Post this comment?** [User approval needed]

### Major Issue 2
**File**: `src/components/Form.tsx:15`
**Type**: Performance
**Comment**: "⚠️ **Performance**: This component re-renders unnecessarily. Consider using React.memo to optimize rendering."

**Post this comment?** [User approval needed]

[Continue for all findings...]

## Overall PR Assessment

**Proposed Review Decision**: Request Changes / Approve / Comment

**Summary Comment**:
"Thanks for this PR! Overall the implementation looks good, but there are a few security and performance issues that should be addressed before merging. I've left detailed comments on specific lines."

**Post summary comment and submit review?** [User approval needed]
```

### Step 5.2: Get User Approval

For each proposed comment, ask:
- "Should I post this comment? (yes/no/modify)"
- If "modify": "How would you like to change this comment?"

### Step 5.3: Execute Approved Comments

If user approves, post comments to PR:

```bash
# Post line-specific review comments
{{api.post_review}}

# Post general PR comment
{{pr.comment}}
```
