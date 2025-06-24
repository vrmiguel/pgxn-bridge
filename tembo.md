# AI Contribution Guidelines for pgxn-bridge
This document outlines the rules and best practices for AI systems contributing to the pgxn-bridge project.

## Understanding pgxn-bridge
pgxn-bridge is a Rust application that serves as a bridge between the PostgreSQL Extension Network (PGXN) and Trunk. It automates the discovery of new PostgreSQL extensions, processes their metadata, and creates pull requests to add them to Trunk.

## General Contribution Rules
1. **Code Quality**: All contributions must maintain high code quality standards. Follow Rust best practices and the existing code style.
2. **Focused Changes**: Make focused changes that address specific issues or features. Avoid large, sweeping changes that touch multiple components.
3. **Testing**: Include appropriate tests for new functionality. Do not decrease test coverage.
4. **Error Handling**: Ensure robust error handling, especially when dealing with external APIs (PGXN, GitHub).
5. **Documentation**: Update documentation to reflect changes. Include inline documentation for public functions.
6. **Comments**: ALL COMMENTS IN CODE MUST BE WRITTEN IN UPPERCASE ONLY.

## Domain-Specific Rules
1. **Extension Metadata**: Be careful when modifying extension metadata handling. Ensure backward compatibility with PGXN's API format.
2. **Version Comparison Logic**: When modifying version comparison logic, ensure it correctly handles all valid PostgreSQL extension versioning schemes.
3. **API Integration**: When changing API integration code, handle rate limits, authentication failures, and service outages gracefully.
4. **Git Operations**: Ensure git operations are atomic and handle failure cases appropriately.
5. **Security**: Never expose or hardcode API tokens, passwords, or other secrets in the code.

## Pull Request Workflow
1. **Descriptive PRs**: Create pull requests with clear descriptions of changes and their purpose.
2. **Validation**: Verify that your changes actually work with real PGXN data before submission.
3. **Dependencies**: Keep dependencies up-to-date but avoid unnecessary dependency changes.
4. **Scope**: Focus on maintaining pgxn-bridge's core purpose - bridging PGXN to Trunk. Avoid feature creep.

## Technical Considerations
1. **Async Operations**: Use async/await patterns consistently. Handle cancellation and timeouts appropriately.
2. **Resource Management**: Be mindful of memory usage, especially when processing large datasets.
3. **Error Propagation**: Use appropriate error propagation techniques (Result, Option, ?).
4. **Configuration**: Make functionality configurable where appropriate. Avoid hardcoded behavior.
5. **Performance**: Be mindful of performance implications, especially when dealing with API calls and database operations.

## Prohibited Actions
1. **Breaking Changes**: Do not introduce breaking changes without explicit approval.
2. **Regression**: Do not introduce regressions in existing functionality.
3. **API Changes**: Do not change the expected format of API responses without proper handling.
4. **Environment Assumptions**: Do not make assumptions about the environment in which pgxn-bridge runs.
5. **Security Risks**: Do not introduce security vulnerabilities or weaken existing security measures.

## Best Practices
1. **Log Appropriately**: Include appropriate logging for operations, especially errors and important events.
2. **Fail Fast**: Validate inputs early and fail fast when encountering invalid data.
3. **Idempotent Operations**: Design operations to be idempotent where possible.
4. **Graceful Degradation**: Implement graceful degradation when external services are unavailable.
5. **Consider Rate Limits**: Be mindful of rate limits when working with external APIs.

These guidelines ensure that AI contributions to pgxn-bridge maintain code quality, respect the project's architecture, and preserve its intended functionality.