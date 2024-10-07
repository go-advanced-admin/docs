# ORM

Welcome to the **ORM Integrations** section of our Advanced Admin Panel documentation. Here, you'll find detailed 
guides on how to integrate the admin panel with various Object-Relational Mapping (ORM) libraries. Each guide includes 
instructions on configuration, model registration, and querying/filtering data specific to each ORM.

If you're new and want to get started quickly, please refer to our [Quick Start Guides](Quick-Start.md). For any ORMs 
not listed here, or if you'd like to contribute, please visit our [Contributions](Contributing.md) page.

---

## Supported ORM Integrations

We currently support the following ORMs:

- [GORM](#gorm)
- [XORM](#xorm) (coming soon)
- [SQLX](#sqlx) (coming soon)
- [Bun](#bun) (coming soon)
- [Other Popular Go ORMs](#other-popular-go-orms)

### GORM

[GORM](https://gorm.io/) is a widely used ORM library for Go, offering a comprehensive set of features and excellent 
documentation.

- **Guide:** [GORM Integration](GORM.md)
    - **Sections Covered:**
        - Configuration
        - Model Registration
        - Querying and Filtering Data

### XORM

[XORM](https://xorm.io/) is a simple and powerful ORM for Go, emphasizing ease of use and performance.

- **Guide:** [XORM Integration](XORM.md) (coming soon)
    - **Sections Covered:**
        - Configuration
        - Model Registration
        - Querying and Filtering Data

### SQLX

[SQLX](https://github.com/jmoiron/sqlx) provides extensions on Go's standard `database/sql` library, offering a robust 
set of tools for database interactions without an ORM layer.

- **Guide:** [SQLX Integration](SQLX.md) (coming soon)
    - **Sections Covered:**
        - Configuration
        - Model Registration
        - Querying and Filtering Data

### Bun

[Bun](https://bun.uptrace.dev/) is a fast and simple SQL query builder and ORM for Go, designed for performance and 
ease of use.

- **Guide:** [Bun Integration](Bun.md) (coming soon)
    - **Sections Covered:**
        - Configuration
        - Model Registration
        - Querying and Filtering Data

### Other Popular Go ORMs

We are continually expanding support for more ORMs. If your preferred ORM is not listed above, please check the following page:

- **Guide:** [Other ORM Integrations](Contributing.md)

---

## Building a Custom ORM Integration

If the ORM you're using is not currently supported, you can build a custom integration by implementing the required 
interfaces provided by the admin panel. Our comprehensive guide will walk you through the process.

- **Guide:** [](Building-a-Custom-ORM-Integration.md)
    - **Sections Covered:**
        - Implementing the Required Interface
        - Handling Database Interactions
        - Registering the Custom ORM

### Steps Overview

1. **Implementing the Required Interface:**
    - Understand the methods your ORM needs to implement.
    - Learn how to map your ORM's functionalities to the admin panel's expectations.

2. **Handling Database Interactions:**
    - Configure your ORM to interact seamlessly with the admin panel.
    - Manage connections, transactions, and error handling.

3. **Registering the Custom ORM:**
    - Integrate your custom ORM with the admin panel.
    - Ensure your models and data are correctly recognized and managed.

---

## Contribute and Request Features

We value community contributions and are always looking to support more ORMs. If you need an integration that's not 
currently available or have built a custom integration you'd like to share, please visit our 
[Contributions](Contributing.md) page.

### How You Can Help:

- [**Submit a Feature Request:**](https://github.com/go-advanced-admin/admin/issues/new?assignees=&labels=&projects=&template=feature_request.md&title=)
    - Let us know which ORM you'd like to see supported next.
    - Provide any specifics that could help us prioritize and implement the integration.

- [**Contribute Your Integration:**](Contributing.md)
    - Share your custom ORM integration with the community.
    - Collaborate with maintainers to ensure it meets project standards.

- [**Enhance Existing Integrations:**](Contributing.md)
    - Help improve documentation, fix bugs, or add features to current ORM integrations.
    - Engage with other developers to make our tools better for everyone.

---

## Getting Help

If you encounter any issues or have questions while integrating your ORM, please don't hesitate to reach out:

- **Support:** [Contact Us](https://github.com/go-advanced-admin/admin/issues/new?assignees=&labels=&projects=&template=bug_report.md&title=)
- **Community Forum:** [Join the Discussion](https://github.com/go-advanced-admin/admin/discussions)

We are here to assist you and appreciate any feedback that can help improve our documentation and tools.
