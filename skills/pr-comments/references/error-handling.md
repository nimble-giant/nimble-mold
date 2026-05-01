## Error Handling

### {{scm.provider}} CLI Issues

- If `{{scm.cli}}` command fails, check authentication: `{{auth.check}}`
- If PR not found, verify the number/URL and repository access
- If API rate limits hit, wait and retry with exponential backoff

### Git Issues

- If push fails due to conflicts, help user resolve merge conflicts
- If tests fail, halt execution and help user fix issues
- If commit fails, check for unstaged changes and git status

### Comment Posting Issues

- If reply posting fails, save reply text and provide manual instructions
- If permissions insufficient, guide user through repository access setup

## Security Considerations

- Never commit sensitive information revealed in comments
- Validate all file paths to prevent directory traversal
- Sanitize comment replies to prevent injection attacks
- Respect repository access permissions

## Integration Notes

This template integrates with:

- {{scm.provider}} CLI for PR and comment management
- Git for code changes and commits
- Project test suites for validation
- Plan mode for user interaction

## Usage Tips

1. **Run from PR branch**: Execute this command while on the branch associated with the PR
2. **Check permissions**: Ensure you have write access to the repository
3. **Review before approval**: Carefully review the consolidated plan before approving
4. **Test locally**: Consider running tests locally before pushing changes
5. **Follow up**: Monitor the PR for additional comments after responses

## Recap

Use this command to efficiently address all PR review comments in one session. It fetches all comments, helps you plan responses strategically, makes necessary code changes, and posts professional replies — all while keeping you in control of the process through plan approval.
