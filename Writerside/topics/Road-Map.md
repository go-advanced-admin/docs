# RoadMap

Welcome to the roadmap for the **Go Advanced Admin Panel** project! This document outlines the planned features and 
improvements that we aim to implement in future releases. Our goal is to provide a highly configurable and extensible 
admin panel for Go developers, supporting a wide range of web frameworks and ORMs.

We encourage the community to contribute to the development of this project. If you're interested in helping us build
out this roadmap, please refer to our [Contribution Guide](Contributing.md) for guidelines on how to get involved.

## Table of Contents

- [Integrations](#integrations)
    - [ORM Integrations](#orm-integrations)
    - [Web Framework Integrations](#web-framework-integrations)
- [Testing and Compatibility](#testing-and-compatibility)
- [Form Framework Enhancements](#form-framework-enhancements)
    - [File Field Support](#file-field-support)
    - [Many-to-Many Field Support](#many-to-many-field-support)
    - [Foreign Key Field Enhancements](#foreign-key-field-enhancements)
- [Front-End UI Improvements](#front-end-ui-improvements)
- [Documentation Enhancements](#documentation-enhancements)
- [Get Involved](#get-involved)

## Integrations

To make the admin panel as versatile as possible, we plan to expand our integration suite to support more ORMs and web 
frameworks.

### ORM Integrations

We aim to provide seamless integration with the following ORMs:

- **XORM**: A simple and powerful ORM for Go.
- **SQLX**: Extensions to Go's `database/sql` package.
- **Bun**: An ORM for PostgreSQL, MySQL, and SQLite.
- **GORM**: Enhance existing integration with advanced features.
- **[Other Popular ORMs]**: We are open to supporting additional ORMs based on community interest.

### Web Framework Integrations

We plan to support the following web frameworks:

- **Chi**: A lightweight, idiomatic, and composable router.
- **Gin**: A high-performance HTTP web framework.
- **Fiber**: An Express-inspired web framework written in Go.
- **Standard Library**: Support for Go's `net/http` package without additional frameworks.
- **[Other Popular Frameworks]**: We welcome suggestions for other frameworks to support.

## Testing and Compatibility

Ensuring the reliability and compatibility of our admin panel across different environments is crucial.

- **Comprehensive Testing**: Develop a full suite of tests for the main admin repository and all maintained 
integrations.
- **Compatibility Checks**: Tests will verify compatibility with various versions of Go and dependency libraries.
- **Continuous Integration**: Implement CI pipelines to automate testing across multiple configurations.

## Form Framework Enhancements

Improving the forms framework is a high priority to provide a better developer and user experience.

### File Field Support

- **File Uploads**: Introduce form fields that allow users to upload files.
- **Validation**: Include server-side validation for file types and sizes.
- **Storage Options**: Support for storing files locally or in cloud storage services.

### Many-to-Many Field Support

- **User Interface Improvements**: Provide an intuitive UI for managing many-to-many relationships.
    - Autoloading counters indicating the number of related records.
    - Filtering options to search and select related records.
- **Backend Support**: Simplify the process of defining and handling many-to-many relationships in models.

### Foreign Key Field Enhancements

- **Relationship Visualization**: Clearly display foreign key relationships within the admin panel.
- **Navigation**: Allow easy navigation between related records.
- **Filtering and Search**: Enable filtering of records based on foreign key relationships.

## Front-End UI Improvements

We aim to make the admin panel more customizable and interactive without requiring developers to modify templates 
directly.

- **Theming Support**: Allow developers to change colors, fonts, and styles through configuration.
- **Logo and Branding**: Enable the inclusion of custom logos and branding elements.
- **Responsive Design**: Improve the UI to be fully responsive across devices.
- **Widget System**: Introduce a widget system for adding custom dashboard components.

## Documentation Enhancements

High-quality documentation is essential for developer adoption and ease of use.

- **In-Line Code Documentation**: Improve code comments and documentation for all maintained code.
- **API References**: Generate comprehensive API reference guides.
- **User Guides**: Create detailed guides and tutorials for common tasks.
- **Examples and Snippets**: Provide code examples to illustrate usage patterns.

## Get Involved

We welcome contributions from the community! If you're interested in contributing to any of the items on this roadmap 
or have suggestions for new features, please check out our [Contribution Guide](Contributing.md) to get started.

Your feedback and contributions are invaluable in helping us improve and expand the Go Advanced Admin Panel.

---

*This roadmap is subject to change based on community feedback and project needs.*

> This section of the documentation was written with the help of [Infuzu AI](https://infuzu.com)'s tools.
{style="note"}
