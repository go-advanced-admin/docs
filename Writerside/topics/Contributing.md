# Contributing

Thank you for your interest in contributing to the Go Advanced Admin project! We welcome contributions from the 
community and are excited to collaborate with you to make this project even better.

This guide outlines the ways you can contribute, the guidelines to follow, and resources to help you get started.

## Table of Contents

- [Ways to Contribute](#ways-to-contribute)
    - [Reporting Issues](#reporting-issues)
    - [Submitting Feature Requests](#submitting-feature-requests)
    - [Contributing Code](#contributing-code)
    - [Sharing Examples](#sharing-examples)
    - [Sharing Custom Integrations](#sharing-custom-integrations)
    - [Contributing to Documentation](#contributing-to-documentation)
- [Guidelines for Contributing Code](#guidelines-for-contributing-code)
    - [Code Style Guidelines](#code-style-guidelines)
    - [Testing](#testing)
    - [Documentation Updates](#documentation-updates)
- [Getting Started](#getting-started)
    - [Setting Up Your Development Environment](#setting-up-your-development-environment)
    - [Working with the Repositories](#working-with-the-repositories)
- [Community and Support](#community-and-support)
- [Code of Conduct](#code-of-conduct)

---

## Ways to Contribute

### Reporting Issues

If you encounter any bugs or issues while using Go Advanced Admin, please let us know by 
[creating an issue](https://github.com/go-advanced-admin/admin/issues/new) in the appropriate repository. When 
reporting an issue, please include:

- A clear and descriptive title.
- Steps to reproduce the issue.
- Expected and actual behavior.
- Any relevant logs or error messages.
- Environment details (e.g., Go version, OS).

### Submitting Feature Requests

Have an idea to improve Go Advanced Admin? We welcome feature requests! Please 
[open a new issue](https://github.com/go-advanced-admin/admin/issues/new) and select the "Feature Request" template. 
Provide as much detail as possible about the feature and how it would benefit users.

### Contributing Code

We appreciate code contributions to both the core project and maintained integrations. Here's how you can contribute code:

1. **Fork the Repository**: Create a personal fork of the repository you want to contribute to (e.g., `admin`, 
`admingorm`, `admingin`, etc.).

2. **Create a Branch**: Create a new branch for your changes:
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Make Your Changes**: Implement your changes, ensuring you follow the project's code style guidelines.

4. **Write Tests**: Ensure all existing tests pass and write new tests to cover your changes. If you're fixing a bug, 
include tests that would fail without your fix.

5. **Update Documentation**: All pull requests must be accompanied by a corresponding pull request to the 
[documentation repository](https://github.com/go-advanced-admin/docs). Update the documentation to reflect your changes 
or new features.

6. **Submit a Pull Request**: Open a pull request to the appropriate repository. Provide a clear description of your 
changes and reference any related issues.

### Sharing Examples

If you've built something interesting with Go Advanced Admin, we'd love to see it! You can:

- **Share on GitHub**: Post your example on your own GitHub repository.
- **Contribute to Documentation**: Submit a pull request to the 
[documentation repository](https://github.com/go-advanced-admin/docs) to include a reference to your example in the 
relevant section.

### Sharing Custom Integrations

Have you created a custom integration with a web framework or ORM not yet supported? Here's how you can share it:

- **Open Source Your Integration**: Publish your integration on GitHub or another platform.
- **Contribute to Documentation**: Update the [documentation](https://github.com/go-advanced-admin/docs) to include a 
reference to your integration and its documentation.

By sharing your custom integrations, you help others in the community and contribute to the growth of the project.

### Contributing to Documentation

Documentation is crucial for the success of any project. You can contribute by:

- **Improving Existing Content**: Fix typos, clarify explanations, or enhance the existing documentation.
- **Adding New Sections**: Write guides, tutorials, or FAQs to help users better understand and use the project.
- **Translating Documentation**: Help make the documentation accessible to a wider audience by providing translations.

To contribute to the documentation, submit a pull request to the 
[documentation repository](https://github.com/go-advanced-admin/docs).

---

## Guidelines for Contributing Code

To ensure a smooth collaboration, please follow these guidelines when contributing code.

### Code Style Guidelines

- **Follow Go Conventions**: Write idiomatic Go code following the 
[Effective Go](https://golang.org/doc/effective_go.html) guidelines.
- **Keep It Simple**: Strive for simplicity and readability. Write clear and maintainable code.
- **Comments and Documentation**: Document exported functions, types, and packages using GoDoc comments.

### Testing

- **Run All Tests**: Before submitting your pull request, ensure that all existing tests pass.
  ```bash
  go test ./...
  ```
- **Write New Tests**: For new features or bug fixes, include tests to verify the behavior.
- **Test Coverage**: Aim to maintain or improve the test coverage of the project.

### Documentation Updates

- **Update Documentation**: All code changes must be accompanied by updates to the documentation.
- **Consistent Versioning**: Ensure that your documentation updates correspond to the correct version of the project.
- **Pull Request to Docs Repo**: Submit a pull request to the 
[documentation repository](https://github.com/go-advanced-admin/docs) alongside your code changes.

---

## Getting Started

### Setting Up Your Development Environment

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/go-advanced-admin/admin.git
   cd admin
   ```

2. **Install Dependencies**:
   ```bash
   go mod tidy
   ```

3. **Set Up Database (if applicable)**: Depending on the integration or feature you're working on, you may need to set 
up a database.

### Working with the Repositories

- **Core Repository**: Contains the main codebase of the Go Advanced Admin project.
- **Integrations**: Separate repositories exist for different ORM and web framework integrations (e.g., `admingorm`, 
`admingin`).
- **Documentation**: The documentation is maintained in the `docs` repository.

---

## Community and Support

- **Issue Tracker**: Use the [GitHub Issues](https://github.com/go-advanced-admin/admin/issues) to report bugs or 
request features.
- **Roadmap**: Check out the [Roadmap](Road-Map.md) to see what we're working on and where contributions are needed.
- **Discussions**: Join the conversation in the 
[GitHub Discussions](https://github.com/go-advanced-admin/admin/discussions) forum.

---

## Code of Conduct

We are committed to fostering an open and welcoming environment. Please read our 
[Code of Conduct](https://github.com/go-advanced-admin/admin/blob/main/CODE_OF_CONDUCT.md) to understand the 
expectations for all contributors.

---

Thank you for taking the time to contribute to Go Advanced Admin. Your efforts help improve the project for everyone. 
We look forward to your contributions!

> This section of the documentation was written with the help of [Infuzu AI](https://infuzu.com)'s tools.
{style="note"}
