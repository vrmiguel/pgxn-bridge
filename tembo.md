# Tembo AI Contribution Guidelines for pgxn-bridge

This document outlines the rules and best practices for AI systems contributing to the pgxn-bridge project. pgxn-bridge automates the process of bridging PostgreSQL extensions from PGXN to the Trunk repository.

## Understanding the Project

Before making any contributions, AI systems should:

1. Understand that pgxn-bridge is a Rust application that monitors PGXN for new PostgreSQL extension releases and automatically creates pull requests to add them to the Trunk repository.
2. Be familiar with the project's core components:
   - PGXN API interaction
   - Git operations
   - GitHub API for PR creation
   - Trunk.toml file generation

## Code Contribution Rules

### General Guidelines

1. Always follow Rust best practices and idiomatic patterns.
2. Maintain the existing code structure and naming conventions.
3. Ensure all functions have appropriate error handling.
4. Add appropriate comments for complex logic, but keep them concise.
5. Do not introduce external dependencies without clear justification.

### Specific Requirements

1. **Error Handling**:
   - Use appropriate error types and propagation.
   - Ensure errors are meaningful and actionable.
   - Log errors with appropriate context.

2. **Configuration**:
   - Do not hardcode sensitive values (tokens, credentials).
   - Use environment variables or configuration files for variable parameters.

3. **API Interactions**:
   - Implement proper rate limiting for external API calls.
   - Add appropriate retries with exponential backoff for network operations.
   - Validate all external data before processing.

4. **Git Operations**:
   - Always create new branches for each extension update.
   - Follow the established commit message format.
   - Ensure the repository is reset to a clean state after operations.

5. **Trunk.toml Generation**:
   - Strictly follow the required schema for Trunk.toml files.
   - Validate generated TOML against the expected format.
   - Include all required fields from the PGXN metadata.

## Testing Requirements

1. Write unit tests for all new functions.
2. Create integration tests for new features.
3. Mock external dependencies (PGXN API, GitHub) in tests.
4. Include both success and failure scenarios in tests.

## Pull Request Process

AI systems should:

1. Create a descriptive PR title that clearly indicates the change.
2. Include a detailed description of the changes and their purpose.
3. Reference any related issues or PRs.
4. Ensure CI checks pass before requesting review.

## Security Considerations

1. Never commit API tokens, private keys, or credentials.
2. Validate and sanitize all external input.
3. Be cautious with file system operations.
4. Use secure methods for all GitHub interactions.

## Performance Considerations

1. Be mindful of memory usage, especially when processing large extension metadata.
2. Optimize network requests by batching when possible.
3. Consider the impact of concurrent operations.
4. Implement appropriate timeouts for external API calls.

## Documentation Requirements

1. Update documentation to reflect any API or behavior changes.
2. Document new functions with appropriate doc comments.
3. Ensure examples in documentation are accurate and tested.

## Maintenance Guidelines

1. Keep dependencies updated to secure versions.
2. Address technical debt proactively.
3. Refactor code when it improves readability or maintainability.
4. Remove deprecated or unused code.

By following these guidelines, AI systems can contribute effectively to the pgxn-bridge project while maintaining code quality, security, and project integrity.