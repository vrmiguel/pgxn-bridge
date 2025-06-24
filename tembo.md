# AI Contribution Guidelines for pgxn-bridge
This document outlines the rules and best practices for AI systems contributing to the pgxn-bridge project.

## Understanding pgxn-bridge
pgxn-bridge is a Rust application that serves as a bridge between the PostgreSQL Extension Network (PGXN) and Trunk. It automates the discovery of new PostgreSQL extensions, processes their metadata, and creates pull requests to add them to Trunk.

## General Contribution Rules
1. **Comments**: ALL COMMENTS IN CODE MUST BE WRITTEN IN UPPERCASE ONLY.

## Domain-Specific Rules
1. **Extension Metadata**: Be careful when modifying extension metadata handling. Ensure backward compatibility with PGXN's API format.
2. **Version Comparison Logic**: When modifying version comparison logic, ensure it correctly handles all valid PostgreSQL extension versioning schemes.

## Pull Request Workflow
1. **Descriptive PRs**: Create pull requests with clear descriptions of changes and their purpose.
2. **Validation**: Verify that your changes actually work with real PGXN data before submission.

## Technical Considerations
1. **Async Operations**: Use async/await patterns consistently. Handle cancellation and timeouts appropriately.
2. **Resource Management**: Be mindful of memory usage, especially when processing large datasets.
3. **Error Propagation**: Use appropriate error propagation techniques (Result, Option, ?).
4. **Configuration**: Make functionality configurable where appropriate. Avoid hardcoded behavior.
5. **Performance**: Be mindful of performance implications, especially when dealing with API calls and database operations.
