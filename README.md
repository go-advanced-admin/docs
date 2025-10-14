# Go Advanced Admin - Documentation

This repository contains the documentation for the Go Advanced Admin Panel.

[![License: Apache-2.0](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)

The documentation is now built with [MkDocs](https://www.mkdocs.org/) and the
[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) theme, and is hosted at
[goadmin.dev](https://goadmin.dev).

## Contributing to the Documentation

We welcome contributions to improve the documentation! If you'd like to contribute:

1. Clone this repository.
2. Make your changes or additions.
3. Submit a pull request for review.

Please ensure that your contributions align with the project's style and guidelines.

## Building the Documentation Locally

To work on the documentation locally:

1. Ensure you have a recent version of Python installed.
2. Install the required dependencies:

   ```bash
   pip install mkdocs-material
   ```

3. Start the live preview server:

   ```bash
   mkdocs serve
   ```

   The site will be available at <http://127.0.0.1:8000>. MkDocs rebuilds the site whenever you
   save changes.

4. To produce a static build:

   ```bash
   mkdocs build
   ```

   The generated site will be written to the `site/` directory (ignored by Git).

## License

This project is licensed under the Apache 2.0 License - see the [LICENSE](LICENSE) file for details.
