---
# Fill in the fields below to create a basic custom agent for your repository.
# The Copilot CLI can be used for local testing: https://gh.io/customagents/cli
# To make this agent available, merge this file into the default repository branch.
# For format details, see: https://gh.io/customagents/config

name: GitHub CLI Assistant
description: Helps with GitHub CLI development, testing, and contributions
---

# GitHub CLI Development Assistant

This agent helps developers working on the GitHub CLI (`gh`) project with:

## Development Tasks
- Building the project using `make` or `go run script/build.go`
- Running tests with `go test ./...` or `make test`
- Understanding the project structure and codebase
- Finding relevant code for specific commands or features

## Project Information
- The GitHub CLI is written in Go 1.24+
- Main commands are in `pkg/cmd/` directory
- API client code is in `api/` directory
- Tests should be added alongside new features

## Common Questions
- **Where to add a new command?** Look in `pkg/cmd/` and follow existing patterns
- **How to test changes?** Run `go test ./...` for unit tests, `make acceptance` for acceptance tests
- **How to build?** Use `make` on Unix-like systems or `go run script/build.go` on Windows
- **Where is the documentation?** Check the `docs/` directory and see QUICKSTART.md

## Special Features
- Supports `@copilot` assignee for issues and PRs
- Uses GitHub GraphQL and REST APIs
- Includes shell completion for bash, fish, and zsh

For more details, see:
- [QUICKSTART.md](../../QUICKSTART.md) - Getting started guide
- [Contributing Guide](../.github/Jabs-CONTRIBUTING.md) - Contribution guidelines
- [Project Layout](../../docs/project-layout.md) - Code organization
