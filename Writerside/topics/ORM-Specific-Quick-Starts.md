# ORM-Specific Quick Starts

Welcome to the ORM-Specific Quick Start Guides for the Go Advanced Admin Panel. These guides will help you integrate 
the admin panel with your preferred Object-Relational Mapping (ORM) library in Go.

Before proceeding, we recommend reviewing the [Generic Quick Start Guide](Quick-Start.md) to familiarize yourself with 
the basic setup and configuration of the admin panel.

## Supported ORM Integrations

We currently support the following ORM integrations:

### GORM

[GORM](https://gorm.io/) is a powerful ORM library for Go, offering developer-friendly APIs for CRUD operations, 
associations, transactions, and more.

- **[GORM Quick Start Guide](GORM-Quick-Start.md)**

### Bun

[Bun](https://bun.uptrace.dev/) is an SQL-first ORM for Go, designed for performance and ease of use, with support for 
PostgreSQL, MySQL, and SQLite.

- **[Bun Quick Start Guide](Bun-Quick-Start.md)** (coming soon)

### SQLX

[SQLX](https://jmoiron.github.io/sqlx/) extends Go's `database/sql` package, providing a set of extensions that make 
working with databases easier without introducing heavy ORM features.

- **[SQLX Quick Start Guide](SQLX-Quick-Start.md)** (coming soon)

### XORM

[XORM](https://xorm.io/) is a simple and powerful ORM for Go. It makes it easy to interact with databases using Go 
structs.

- **[XORM Quick Start Guide](XORM-Quick-Start.md)** (coming soon)

## Getting Started

Choose your ORM from the list above to access the specific quick start guide tailored to it. Each guide covers:

- Installation and setup.
- Configuration specific to the ORM.
- Registering models with the admin panel.
- Running the admin panel with your ORM integration.

## Building a Custom ORM Integration

If your preferred ORM is not listed, you can create a custom integration by implementing the required interfaces. Refer
to the [](Building-a-Custom-ORM-Integration.md) guide for detailed instructions on:

- Implementing the ORM integrator interface.
- Handling database interactions.
- Registering your custom ORM with the admin panel.

## Contributing

We welcome contributions from the community! If you've developed a custom ORM integration and want to share it with 
others, please see our [Contributing Guide](Contributing.md) for information on how to contribute to the project. Your 
contributions help make the Go Advanced Admin Panel more versatile and useful for everyone.

---

**Next Steps:**

- Return to the [Generic Quick Start Guide](Quick-Start.md).
- Proceed to the [Web Framework Quick Start Guides](Web-Framework-Specific-Quick-Starts.md) to integrate with your preferred web framework.

> This section of the documentation was written with the help of [Infuzu AI](https://infuzu.com)'s tools.
{style="note"}
