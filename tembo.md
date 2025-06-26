# AI Contribution Guidelines for pgxn-bridge

## General Contribution Rules
1. **Comments**: ALL COMMENTS IN CODE MUST BE WRITTEN IN UPPERCASE ONLY.

## What is pgxn-bridge?

pgxn-bridge is a Rust application that creates an automated bridge between the PostgreSQL Extension Network (PGXN) and Trunk repositories. It monitors for new PostgreSQL extension releases on PGXN and automatically creates pull requests to add them to the Trunk repository, facilitating extension discovery and distribution.

## Architecture Overview

pgxn-bridge functions as an automated pipeline with the following key components:

1. **PGXN Integration**: Fetches recent extension releases from the PGXN API
2. **Trunk Repository Management**: Clones and manages the Trunk repository
3. **Git Operations**: Handles branch creation and commit generation
4. **GitHub Interaction**: Opens pull requests for new extensions
5. **Metadata Processing**: Transforms PGXN metadata into Trunk.toml format

## Core Functionality

### PGXN API Integration

pgxn-bridge connects to the PGXN API to retrieve information about recently published PostgreSQL extensions. It uses the following endpoints:

- `https://master.pgxn.org/stats/dist.json` - Lists recent distributions
- `https://master.pgxn.org/dist/{dist}/{version}/META.json` - Gets metadata for a specific extension version
- `https://master.pgxn.org/dist/{dist}/{version}/{dist}-{version}.zip` - Downloads the extension archive

The application processes extension metadata, including:
- Extension name and version
- Description and abstract
- Maintainer information
- License details
- Repository and homepage URLs

### Trunk Repository Integration

pgxn-bridge:

1. Clones the Trunk repository to a temporary directory
2. Checks if an extension already exists in Trunk
3. Compares version numbers using semantic versioning to determine if an update is needed
4. Creates a Trunk.toml file with the appropriate extension configuration
5. Commits changes to a new branch with a standardized naming convention (`pgxn-bridge/{extension-name}-{version}`)

### GitHub Automation

The application automatically:

1. Pushes new branches to GitHub
2. Creates pull requests with descriptive titles and bodies
3. Includes relevant metadata from PGXN in the PR description
4. Handles authentication using GitHub personal access tokens

### Workflow Pipeline

The complete workflow follows these steps:

1. Fetch recent PGXN distributions
2. Fetch current Trunk repository contents
3. Compare each PGXN release with Trunk entries
4. For each new or updated extension:
   - Download and parse extension metadata
   - Generate appropriate Trunk.toml configuration
   - Create a new branch in the Trunk repository
   - Commit the Trunk.toml file
   - Push the branch to GitHub
   - Open a pull request with descriptive information

## Technical Implementation

### Key Components

1. **main.rs**: Entry point and workflow orchestration
   - Coordinates the overall process
   - Handles parallel fetching of PGXN and Trunk data
   - Processes extensions and manages error handling

2. **dist.rs**: PGXN distribution handling
   - Fetches and parses PGXN API responses
   - Processes extension metadata
   - Downloads extension archives

3. **git.rs**: Git repository management
   - Clones the Trunk repository
   - Creates branches and commits
   - Pushes changes to GitHub

4. **github.rs**: GitHub API integration
   - Formats pull request descriptions
   - Creates pull requests via the GitHub API

5. **trunk.rs and trunk/trunk_toml.rs**: Trunk repository handling
   - Fetches current Trunk repository contents
   - Generates Trunk.toml configuration files

### Dependencies

pgxn-bridge relies on several key Rust crates:

- **reqwest**: HTTP client for API communication
- **serde/serde_json/toml**: Serialization/deserialization of JSON and TOML data
- **tokio**: Asynchronous runtime for concurrent operations
- **git2**: Git operations via libgit2
- **zip/tar/flate2**: Archive processing
- **tempfile**: Temporary directory management
- **tracing**: Logging and diagnostic information

## Environment Configuration

pgxn-bridge requires several environment variables to function correctly:

- `GH_PAT`: GitHub Personal Access Token for authentication
- `GH_EMAIL`: Email address for Git commit authorship
- `GH_USERNAME`: GitHub username for authentication
- `GH_AUTHOR`: Author name for Git commit authorship

## How to Contribute

### Development Setup

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/tembo-io/pgxn-bridge.git
   cd pgxn-bridge
   ```

2. **Install Dependencies**:
   - Ensure Rust and Cargo are installed
   - Install development dependencies: `cargo build`

3. **Configure Environment Variables**:
   ```bash
   export GH_PAT=your_github_personal_access_token
   export GH_EMAIL=your_email@example.com
   export GH_USERNAME=your_github_username
   export GH_AUTHOR="Your Name"
   ```

4. **Run Tests**:
   ```bash
   cargo test
   ```

### Contribution Guidelines

1. **Code Style**:
   - Follow Rust's standard formatting guidelines (use `rustfmt`)
   - ALL COMMENTS MUST BE WRITTEN IN UPPERCASE ONLY
   - Use meaningful variable names and add appropriate documentation

2. **Pull Request Process**:
   - Create a feature branch from `main`
   - Make your changes with appropriate tests
   - Update documentation if necessary
   - Submit a pull request with a clear description

3. **Testing**:
   - Write unit tests for new functionality
   - Ensure all tests pass before submitting PRs
   - Consider integration tests for API interactions

4. **Error Handling**:
   - Use the `anyhow` crate for error handling
   - Provide meaningful error messages
   - Ensure proper logging with the `tracing` crate

### Extension Enhancement Opportunities

1. **Additional Metadata Processing**:
   - Support for more complex extension configurations
   - Enhanced version comparison logic

2. **Improved Error Recovery**:
   - Better handling of transient API failures
   - Retry mechanisms for network operations

3. **Performance Optimizations**:
   - More efficient parallel processing
   - Smarter caching of repository data

4. **Monitoring and Reporting**:
   - Generate reports on successful/failed PR creations
   - Integration with notification systems

## Deployment

pgxn-bridge can be deployed as:

1. **Scheduled Job**: Run periodically to check for new extensions
2. **Container**: Deploy as a Docker container with appropriate environment variables
3. **CI/CD Pipeline**: Integrate into existing CI/CD workflows

The Dockerfile in the repository provides a containerized deployment option:

```dockerfile
FROM rust:latest as builder
WORKDIR /app
COPY . .
RUN cargo build --release

FROM debian:bullseye-slim
COPY --from=builder /app/target/release/pgxn-bridge /usr/local/bin/
ENTRYPOINT ["pgxn-bridge"]
```

## Troubleshooting

### Common Issues

1. **Authentication Failures**:
   - Ensure GH_PAT has appropriate permissions
   - Verify GH_USERNAME matches the token owner

2. **Network Connectivity**:
   - Check connectivity to PGXN and GitHub APIs
   - Consider using proxies if necessary

3. **Repository Access**:
   - Verify permissions to push to the target repository
   - Ensure branch protection rules don't block automation
