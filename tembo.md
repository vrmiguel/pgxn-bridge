# AI Contribution Guidelines for pgxn-bridge

## General Contribution Rules
1. **Comments**: ALL COMMENTS IN CODE MUST BE WRITTEN IN UPPERCASE ONLY.

## PGXN-Bridge: Architecture & Operation

PGXN-Bridge is a Rust application that bridges the PostgreSQL Extension Network (PGXN) with the Trunk repository system. It automates the discovery, evaluation, and integration of PostgreSQL extensions from PGXN into Trunk.

### Core Purpose

PGXN-Bridge serves as an automated bridge between two PostgreSQL extension ecosystems:
- **PGXN (PostgreSQL Extension Network)**: A repository of PostgreSQL extensions with standardized metadata
- **Trunk**: A curated collection of PostgreSQL extensions with specific packaging formats

The primary function is to monitor for new or updated extensions on PGXN and automatically create pull requests to add them to Trunk, streamlining the extension discovery and integration process.

### System Architecture

PGXN-Bridge is built as a modular Rust application with several key components:

#### 1. Module Structure

- **main.rs**: Orchestrates the overall workflow and contains the core execution logic
- **dist.rs**: Handles interactions with the PGXN distribution API
- **git.rs**: Manages Git repository operations
- **github.rs**: Interfaces with GitHub API for pull request creation
- **trunk.rs**: Interacts with the Trunk repository and its specific formats
  - **trunk_toml.rs**: Creates and manipulates Trunk.toml configuration files

#### 2. Key Data Structures

- **DistResponse**: Represents PGXN distribution statistics and recent releases
- **Release**: Encapsulates metadata for a specific extension release
- **MetaJson**: Contains detailed metadata from a PGXN extension's META.json
- **TrunkRepo**: Wraps Git operations for the Trunk repository
- **TrunkToml**: Represents the Trunk.toml configuration format

#### 3. External Dependencies

- **HTTP**: Uses reqwest for API communications
- **Git**: Employs git2 for repository management
- **Serialization**: Leverages serde, serde_json, and toml for data processing
- **Async**: Built on tokio for asynchronous operations
- **Error Handling**: Utilizes anyhow for ergonomic error management
- **Logging**: Implements tracing for structured logging

### Workflow Process

PGXN-Bridge follows a systematic workflow:

1. **Environment Setup**:
   - Requires GitHub credentials (GH_PAT, GH_EMAIL, GH_USERNAME, GH_AUTHOR)
   - Initializes logging with tracing framework
   - Creates temporary directory for operations

2. **Repository Preparation**:
   - Clones the Trunk repository to the temporary directory
   - Prepares for branch-based operations

3. **Data Collection** (executed concurrently):
   - Fetches recent extension releases from PGXN's `/stats/dist.json` endpoint
   - Retrieves existing Trunk entries by downloading and parsing Trunk.toml files

4. **Extension Processing**:
   - For each recent PGXN release (processed in reverse chronological order):
     - Checks if the extension already exists in Trunk
     - If it exists, compares versions using semantic versioning rules
     - For new extensions or newer versions, proceeds with integration

5. **Integration Process**:
   - Fetches detailed metadata from PGXN's META.json
   - Creates a unique branch name using extension name and version
   - Generates a Trunk.toml file from the PGXN metadata
   - Creates the necessary directory structure in the Trunk repository
   - Writes the Trunk.toml file to the appropriate location

6. **Pull Request Creation**:
   - Commits changes to the new branch
   - Pushes the branch to GitHub
   - Creates a pull request with automatically generated description
   - Resets to main branch for next operation

7. **Error Handling**:
   - Implements robust error handling throughout the process
   - Logs errors but continues processing other extensions when failures occur

### Key Features

1. **Version Comparison**:
   - Custom semver comparison to detect newer versions
   - Prevents redundant PRs for already up-to-date extensions

2. **Metadata Transformation**:
   - Converts PGXN's META.json format to Trunk's TOML format
   - Standardizes extension metadata across ecosystems

3. **Concurrent Processing**:
   - Uses tokio for asynchronous operations
   - Parallelizes data collection with try_join

4. **Stateless Operation**:
   - Uses temporary directories for all operations
   - Designed to be run periodically (e.g., via CI/CD or scheduled jobs)

5. **Error Resilience**:
   - Continues processing extensions even when some fail
   - Implements detailed error logging and context

## Contribution Guidelines

### Getting Started

1. **Prerequisites**:
   - Rust toolchain (stable channel)
   - Git client
   - GitHub account with appropriate permissions
   - PostgreSQL knowledge (for understanding extensions)

2. **Environment Setup**:
   - Set up environment variables:
     ```
     export GH_PAT="your-github-personal-access-token"
     export GH_EMAIL="your-email@example.com"
     export GH_USERNAME="your-github-username"
     export GH_AUTHOR="Your Name"
     ```

3. **Building the Project**:
   ```bash
   cargo build
   ```

4. **Running the Project**:
   ```bash
   cargo run
   ```

### Development Workflow

1. **Code Contributions**:
   - Fork the repository
   - Create a feature branch
   - Implement changes with appropriate tests
   - Submit a pull request

2. **Testing**:
   - Write unit tests for new functionality
   - Ensure integration tests pass
   - Validate against real PGXN extensions

3. **Code Style**:
   - Follow Rust idioms and best practices
   - Use the standard Rust formatting (rustfmt)
   - Run clippy to catch common issues
   - ALL COMMENTS MUST BE WRITTEN IN UPPERCASE ONLY

### Extension Areas

Consider contributing in these areas:

1. **Core Functionality**:
   - Improve version comparison logic
   - Enhance error handling and reporting
   - Add support for more PGXN metadata fields

2. **Performance Enhancements**:
   - Optimize network requests
   - Improve concurrency patterns
   - Reduce memory usage for large repositories

3. **Integration Features**:
   - Add support for additional extension repositories
   - Implement webhook receivers for real-time updates
   - Create notification systems for integration status

4. **Documentation**:
   - Improve inline code documentation
   - Update user-facing documentation
   - Create examples and tutorials

### Pull Request Process

1. **Before Submitting**:
   - Ensure all tests pass
   - Update documentation if needed
   - Add your changes to the CHANGELOG.md if applicable

2. **PR Description**:
   - Clearly describe the purpose of your changes
   - Reference any related issues
   - Explain your implementation approach
   - Note any breaking changes

3. **Review Process**:
   - Address review feedback promptly
   - Keep discussions focused on the code
   - Be open to alternative approaches

4. **After Merging**:
   - Delete your branch
   - Verify deployment if applicable
   - Monitor for any issues related to your changes

## Troubleshooting

### Common Issues

1. **Authentication Failures**:
   - Verify your GitHub token has appropriate permissions
   - Ensure environment variables are correctly set

2. **Network Issues**:
   - Check connectivity to PGXN and GitHub APIs
   - Verify proxy settings if applicable
   - Look for rate limiting issues

3. **Parsing Errors**:
   - Validate against unexpected PGXN metadata formats
   - Check for changes in the PGXN API
   - Verify Trunk.toml structure requirements

### Logging and Debugging

- The application uses the tracing framework for structured logging
- Set the RUST_LOG environment variable to control log levels:
  ```
  export RUST_LOG=info  # Options: trace, debug, info, warn, error
  ```
- For detailed debugging, use:
  ```
  export RUST_LOG=pgxn_bridge=trace
  ```

## Future Directions

1. **Automation Enhancements**:
   - Add CI/CD pipeline integration
   - Implement scheduled runs via GitHub Actions
   - Create dashboard for monitoring bridge operations

2. **Extension Validation**:
   - Add preliminary testing of extensions
   - Validate SQL syntax and structure
   - Check for compatibility issues

3. **User Interface**:
   - Develop web dashboard for monitoring
   - Create notification systems
   - Add control panel for manual operations

4. **Extended Repository Support**:
   - Add support for additional extension sources
   - Implement bidirectional synchronization
   - Create abstraction layer for multiple repository types