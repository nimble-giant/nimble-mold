## Review Quality Guidelines

### Comment Tone

**Constructive and Helpful:**
- "Consider using X approach here because..."
- "This could be improved by..."
- "Great implementation! One suggestion..."

**Specific and Actionable:**
- Provide exact line numbers
- Include code examples
- Suggest specific alternatives

**Educational:**
- Explain the "why" behind suggestions
- Link to relevant documentation
- Share best practices

### Comment Templates

**Security Issues:**
```
🚨 **Security**: [Brief description]

This could lead to [specific risk]. Consider [specific solution].

References: [link to security documentation]
```

**Performance Issues:**
```
⚠️ **Performance**: [Brief description]

This approach has O(n²) complexity. For better performance, consider [alternative].

Example: [code snippet]
```

**Best Practice Suggestions:**
```
💡 **Suggestion**: [Brief description]

This follows the pattern well! For even better maintainability, you might consider [enhancement].
```

## Error Handling

### {{scm.provider}} API Issues
- Rate limiting: Implement exponential backoff
- Authentication: Guide user through `{{auth.login}}`
- Permissions: Verify repository access

### File Analysis Issues
- Large diffs: Focus on most critical files first
- Binary files: Skip or note as non-reviewable
- Deleted files: Focus on impact analysis

### Review Quality Issues
- Complex changes: Ask user for guidance on focus areas
- Unclear context: Request additional information
- Conflicting best practices: Present options with rationale

## Integration Notes

This template integrates with:
- {{scm.provider}} CLI for PR management
- Git for code analysis
- Language-specific linters and analyzers
- Security scanning tools
- Performance profiling tools

## Usage Tips

1. **Preparation**: Ensure you have repository access and context
2. **Focus**: Use focus flags for targeted reviews
3. **Batch Reviews**: Review multiple files systematically
4. **Follow-up**: Monitor PR for author responses and updates
5. **Learning**: Use reviews as opportunities to share knowledge

## Security Considerations

- Respect repository access permissions
- Don't expose sensitive information in comments
- Validate all file paths and content
- Use secure methods for code analysis

## Recap

Use this command to conduct thorough, consistent code reviews. In silent mode (default), it generates comprehensive markdown review files for your records. In interactive mode, it helps you post structured, helpful comments directly to the PR after plan approval, ensuring high-quality feedback while maintaining reviewer control.
