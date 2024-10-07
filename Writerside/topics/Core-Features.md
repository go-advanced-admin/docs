# Core Features

Welcome to the **go-advanced-admin** Core Features documentation. This section provides a comprehensive guide to the 
main functionalities of the admin panel. Each feature is designed to be highly configurable and extensible, allowing 
you to tailor the admin panel to your specific needs.

Below is an overview of each core feature, along with links to detailed guides for further information.

---

- [Permissions](#permissions)
- [Configuration](#configuration)
- [Templates](#templates)
- [Applications](#applications)
- [Models](#models)
- [Forms](#forms)
- [Logging](#logging)
- [Template Rendering](#template-rendering)

---

## Permissions

Control access to different parts of your admin panel with a flexible permissions' system.

- **Guide on Building a Permission Checker Function**: Learn how to create custom permission logic.
- **Examples of Different Permission Schemes**: Implement role-based or attribute-based permissions.
- **Integration with Authentication Systems**: Connect your permissions with existing authentication mechanisms.

[**Read more about Permissions »**](Permissions.md)

---

## Configuration

Customize the admin panel to suit your project's requirements.

- **Comprehensive Guide on Configuration Options**: Explore all available settings.
    - **Database Connection**: Configure your database.
    - **Authentication**: Set up user authentication.
    - **Authorization**: Define access controls.
    - **Logging**: Configure logging preferences.
    - **Templating**: Customize templates.
    - **Static Files**: Manage static assets.
- **Explanation of the Default Configuration**: Understand the out-of-the-box settings.

[**Read more about Configuration »**](Configuration.md)

---

## Templates

Modify the look and feel of your admin panel.

- **Guide on Overriding Default Templates**: Customize existing templates.
- **Templating Language Syntax and Usage**: Learn the templating language.
- **Creating Custom Templates**: Build templates from scratch.

[**Read more about Templates »**](Templates.md)

---

## Applications

Organize your admin panel into modular applications.

- **Guide on Registering an App**: Learn how to create new applications within the admin panel.
- **App-level Configuration Options**: Customize routing, middleware, and more for each app.

[**Read more about Applications »**](Applications.md)

---

## Models

Define how data is structured and displayed.

- **Guide on Registering a Model**: Add models to your admin panel.
- **Model-level Configuration Options**: Customize fields, display options, and actions.

[**Read more about Models »**](Models.md)

---

## Forms

Handle user input with the built-in forms framework.

- **Guide on the Forms Framework**: Understand how forms work.
    - **Form Field Types**: Explore available field types.
    - **Validation**: Implement data validation.
    - **Customization**: Tailor forms to your needs.
- **Customizing and Adding/Editing Forms**: Learn how to modify forms for create and edit views.

[**Read more about Forms »**](Forms.md)

---

## Logging

Track and audit activities within the admin panel.

- **Guide on Configuring the Log Store Level**: Set the verbosity of logs.
- **Configuring the Log Store**: Use the default in-memory store or configure a custom one.
- **Building a Custom Log Store**:
    - **Implementing the Required Interface**: Learn the necessary methods.
    - **Handling Log Messages**: Manage how logs are processed.
    - **Registering the Custom Log Store**: Integrate your log store with the admin panel.

[**Read more about Logging »**](Logging.md)

---

## Template Rendering

Customize how templates are processed and displayed.

- **Guide on Building a Custom Template Renderer**: Create your own rendering logic.
- **Integrating a Custom Template Renderer**: Hook your renderer into the admin panel.

[**Read more about Template Rendering »**](Template-Rendering.md)

---

## Contributing

We welcome contributions from the community! If you're interested in helping improve the **go-advanced-admin** project, 
please refer to the following resources:

- **[Contribution Guide](Contributing.md)**: Learn how to contribute code, report issues, and suggest enhancements.
- **[Issue Tracker](https://github.com/go-advanced-admin/admin/issues)**: View existing issues or submit new ones.

Your contributions are highly appreciated and help make this project better for everyone.

---

If you encounter any bugs or have suggestions for improvements, please don't hesitate to 
[open an issue](https://github.com/go-advanced-admin/admin/issues). For any questions or discussions, you can join our 
community on [GitHub Discussions](https://github.com/go-advanced-admin/admin/discussions).

---

**Next Steps:**

- Dive into the [Quick Start Guide](Quick-Start.md) to get started with the admin panel.
- Explore the [](Integrations.md) section for ORM and web framework support.
- Check out the [](Advanced-Topics.md) for in-depth customization options.

---

### Feedback

We strive to keep our documentation clear and up-to-date. If you have any feedback or suggestions, please let us know 
by [opening an issue](https://github.com/go-advanced-admin/admin/issues/new/choose) or submitting a pull request.

---

Thank you for using **go-advanced-admin**!

> This section of the documentation was written with the help of [Infuzu AI](https://infuzu.com)'s tools.
{style="note"}
