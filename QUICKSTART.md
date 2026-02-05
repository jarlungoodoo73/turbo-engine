# Quick Start Guide for GitHub CLI

Welcome to the GitHub CLI (`gh`) project! This guide will help you get started with contributing to or understanding the codebase.

## What is GitHub CLI?

GitHub CLI (`gh`) is GitHub's official command-line tool that brings pull requests, issues, and other GitHub concepts to your terminal, right where you're already working with `git` and your code.

## Prerequisites

- **Go 1.24 or later**: Required to build the project
- **Git**: For version control
- **GitHub account**: To test GitHub-specific features

## Quick Setup

### 1. Clone the Repository

```bash
git clone https://github.com/cli/cli.git
cd cli
```

### 2. Build the Project

#### On Unix-like systems (Linux, macOS):
```bash
make
```

#### On Windows:
```bash
go run script/build.go
```

The compiled binary will be available in the `bin/` directory.

### 3. Run Your Build

#### On Unix-like systems:
```bash
./bin/gh --version
```

#### On Windows:
```bash
.\bin\gh.exe --version
```

### 4. Run Tests

```bash
go test ./...
```

For acceptance tests:
```bash
go test -tags acceptance ./acceptance
```

## Project Structure

- **`cmd/`**: Main command entry points
- **`pkg/cmd/`**: Command implementations (issue, pr, repo, etc.)
- **`api/`**: GitHub API client code
- **`internal/`**: Internal packages
- **`acceptance/`**: Acceptance tests
- **`docs/`**: Documentation

## Common Development Tasks

### Building
```bash
make                    # Build the gh binary
```

### Testing
```bash
make test               # Run unit tests
make acceptance         # Run acceptance tests
```

### Generating Documentation
```bash
make manpages           # Generate manual pages
make completions        # Generate shell completions
```

### Installing Locally
```bash
make install            # Install to /usr/local (Unix-like systems only)
```

## Working with Commands

Commands in GitHub CLI follow a hierarchical structure. For example:

- `gh issue create` - Create a new issue
- `gh pr view` - View a pull request
- `gh repo clone` - Clone a repository

### Adding a New Feature

1. Check for existing issues or create a new one
2. Discuss your approach with the team
3. Create a new branch: `git checkout -b feature/my-feature`
4. Implement your changes
5. Add tests for your changes
6. Run tests: `go test ./...`
7. Submit a pull request: `gh pr create --web`

## Special Features

### Assigning Copilot

GitHub CLI supports assigning issues and pull requests to GitHub Copilot:

```bash
gh issue create --assignee @copilot
gh pr edit 123 --add-assignee @copilot
```

This feature allows you to assign work to GitHub's AI-powered coding assistant.

## Testing Your Changes

1. **Unit Tests**: Test individual functions and components
   ```bash
   go test ./pkg/cmd/issue/...
   ```

2. **Integration Tests**: Test command execution
   ```bash
   go test ./...
   ```

3. **Manual Testing**: Run your built binary
   ```bash
   ./bin/gh issue list
   ./bin/gh pr view 123
   ```

## Getting Help

- **Documentation**: See the [docs/](docs/) directory
- **Contributing Guide**: Read [.github/Jabs-CONTRIBUTING.md](.github/Jabs-CONTRIBUTING.md)
- **Discussions**: Visit [GitHub Discussions](https://github.com/cli/cli/discussions)
- **Issues**: Check [existing issues](https://github.com/cli/cli/issues)

## Common Issues

### Build Errors

If you encounter build errors:
1. Ensure you have Go 1.24+ installed: `go version`
2. Clean and rebuild: `make clean && make`
3. Check for dependency issues: `go mod tidy`

### Test Failures

If tests fail:
1. Run tests in verbose mode: `go test -v ./...`
2. Run specific test: `go test -v -run TestName ./pkg/...`
3. Check test setup and requirements

## Next Steps

1. **Read the Contributing Guide**: [.github/Jabs-CONTRIBUTING.md](.github/Jabs-CONTRIBUTING.md)
2. **Explore the Codebase**: Start with `cmd/gh/main.go`
3. **Find a Good First Issue**: Look for [`good first issue`](https://github.com/cli/cli/labels/good%20first%20issue) labels
4. **Join the Community**: Participate in GitHub Discussions

## Useful Links

- [GitHub CLI Manual](https://cli.github.com/manual/)
- [GitHub CLI Documentation](https://docs.github.com/en/github-cli)
- [Project Layout](docs/project-layout.md)
- [Release Process](docs/releasing.md)

---

Happy coding! If you have questions, don't hesitate to ask in [GitHub Discussions](https://github.com/cli/cli/discussions).
