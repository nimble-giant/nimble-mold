### Step 2.2: Present Comments for Planning

Format in plan mode:

```markdown
# PR Comment Response Plan for #<number>: <title>

## Summary
- Total comments requiring response: X
- Review comments (code-specific): Y
- General discussion comments: Z

## Comments to Address

### Review Comment 1
**File**: `path/to/file.js:42`
**Author**: @reviewer
**Comment**: "This function could be simplified by extracting the validation logic."

**Suggested Actions**:
- [ ] Refactor function to extract validation
- [ ] Add unit tests for new validation function
- [ ] Reply acknowledging the change

**Your Response Strategy**: [User input needed]

---

### General Comment 2
**Author**: @reviewer
**Comment**: "Could you add documentation for the new API endpoints?"

**Suggested Actions**:
- [ ] Add API documentation
- [ ] Update README with usage examples
- [ ] Reply with documentation link

**Your Response Strategy**: [User input needed]

---

[Continue for all comments...]

## Response Plan Summary
[To be filled based on user input]
```

### Step 2.3: Interactive Comment Planning

For each comment, ask the user:

1. **"How would you like to address this comment?"**
   - Provide specific suggestions based on comment content
   - Options: "Make code changes", "Reply with explanation", "Ask for clarification", "Mark as won't fix"

2. **"What specific changes should I make?"** (if code changes selected)
   - Get detailed instructions from user
   - Confirm file locations and approach

3. **"What should the reply message say?"** (if replying)
   - Suggest professional response templates
   - Get user's preferred tone and content
