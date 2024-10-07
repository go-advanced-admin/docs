# Models

Models are the core building blocks of your admin panel. They define the structure of your data and how it is displayed 
and managed within the admin interface. This guide will walk you through the process of registering a model with 
**go-advanced-admin**, configuring its options, and customizing its behavior.

---

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Registering a Model](#registering-a-model)
    - [Step 1: Define Your Model](#step-1-define-your-model)
    - [Step 2: Register the Model with the Admin Panel](#step-2-register-the-model-with-the-admin-panel)
- [Model Configuration Options](#model-configuration-options)
    - [Fields](#fields)
    - [Display Options](#display-options)
    - [Actions](#actions)
- [Advanced Model Customization](#advanced-model-customization)
- [References](#references)
- [Contributing](#contributing)

---

## Introduction

In **go-advanced-admin**, a model represents a data structure that you want to manage through the admin interface. By 
registering your application's models, you enable CRUD (Create, Read, Update, Delete) operations, list views, and 
detail views for your data.

## Prerequisites

Before proceeding, ensure you have:

- A Go project set up with **go-advanced-admin** installed.
- A web framework (e.g., Gin, Echo) and an ORM (e.g., GORM, XORM) integrated.
- Basic understanding of Go structs and interfaces.

If you're new to **go-advanced-admin**, we recommend starting with the [Quick Start Guide](Quick-Start.md) for your 
specific web framework and ORM.

## Registering a Model

### Step 1: Define Your Model

First, define the Go struct that represents your data model. For example, let's create a simple `Post` model:

```go
package models

type Post struct {
    ID      uint   `gorm:"primaryKey"` // Assuming you're using GORM
    Title   string
    Content string
    Author  string
}
```

Ensure your model includes all necessary fields and tags required by your ORM.

### Step 2: Register the Model with the Admin Panel

To register the `Post` model with the admin panel, you need to:

1. Register an application (if not already done).
2. Register the model within the application.

Here's how you can do it:

```go
package main

import (
    "github.com/go-advanced-admin/admin"
    "github.com/go-advanced-admin/admin/orms/gormadapter"
    "github.com/go-advanced-admin/admin/webs/ginadapter"
    "github.com/gin-gonic/gin"
    "gorm.io/driver/sqlite"
    "gorm.io/gorm"
    "myproject/models" // Replace with your actual import path
)

func main() {
    // Initialize Gin
    r := gin.Default()

    // Initialize GORM
    db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
    if err != nil {
        panic("failed to connect database")
    }

    // Migrate the schema
    db.AutoMigrate(&models.Post{})

    // Create ORM and Web adapters
    ormAdapter := gormadapter.New(db)
    webAdapter := ginadapter.New(r)

    // Define a simple permission function
    permissionFunc := func(
        req admin.PermissionRequest, ctx interface{}, 
    ) (bool, error) {
        return true, nil // Allow all actions for simplicity
    }

    // Initialize the admin panel
    panel, err := admin.NewPanel(
        ormAdapter, webAdapter, permissionFunc, nil, 
    )
    if err != nil {
        panic(err)
    }

    // Register an application
    app, err := panel.RegisterApp("blog", "Blog Management", nil)
    if err != nil {
        panic(err)
    }

    // Register the Post model
    _, err = app.RegisterModel(&models.Post{}, nil)
    if err != nil {
        panic(err)
    }

    // Start the server
    r.Run(":8080")
}
```

**Explanation:**

- **Initialize Gin and GORM:** Set up your web framework and ORM.
- **Create Adapters:** Use the provided adapters (`gormadapter`, `ginadapter`) to integrate with **go-advanced-admin**.
- **Permission Function:** Define how permissions are handled. In this example, we allow all actions.
- **Initialize the Admin Panel:** Create a new admin panel instance.
- **Register an Application:** Organize your models under an application (e.g., "Blog Management").
- **Register the Model:** Add your `Post` model to the admin panel.

## Model Configuration Options

When registering a model, you can customize its behavior and appearance in the admin panel.

### Fields

You can control how each field in your model is handled by the admin panel using struct tags.

**Example:**

```go
type Post struct {
    ID      uint   `gorm:"primaryKey" admin:"listDisplay:include;search:exclude"`
    Title   string `admin:"listDisplay:include;search:include;displayName:Post Title"`
    Content string `admin:"listDisplay:exclude;search:include;addForm:include"`
    Author  string `admin:"listDisplay:include;search:include"`
}
```

**Supported Struct Tags:**

- `listDisplay`: Control whether the field is displayed in the list view (`include` or `exclude`).
- `search`: Control whether the field is searchable (`include` or `exclude`).
- `addForm`: Control whether the field appears in the "Add" form (`include` or `exclude`).
- `editForm`: Control whether the field appears in the "Edit" form (`include` or `exclude`).
- `displayName`: Provide a custom display name for the field.

### Display Options

Customize how the model and its fields are displayed.

- **Model Display Name:** Implement the `AdminModelDisplayNameInterface` interface to set a custom display name for the 
model.

  ```go
  func (p *Post) AdminDisplayName() string {
      return "Blog Post"
  }
  ```

- **Field Display Name:** Use the `displayName` struct tag as shown above.

### Actions

You can define custom actions or override default behaviors by implementing certain interfaces.

- **Custom Form Fields:** Implement the `AdminFormFieldInterface` to customize form fields.

  ```go
  func (p *Post) AdminFormField(name string, isEdit bool) form.Field {
      if name == "Content" {
          // Return a custom form field for the Content field
      }
      return nil
  }
  ```

## Advanced Model Customization

For more advanced use cases, you can:

- **Implement Custom ORM Logic:** Provide a custom ORM integrator if you have special database interactions.
- **Override Handlers:** Customize how requests are handled by providing your own handlers.

Refer to the [](Advanced-Topics.md) section for more details.

## References

- **Quick Start Guide:** For a step-by-step setup, check out the [Quick Start Guide](Quick-Start.md).
- **Forms Framework:** Learn more about form fields and validation in the [](Forms.md) section.
- **Permissions:** Understand how to secure your models in the [](Permissions.md) guide.
- **Applications:** Organize your models using [](Applications.md).

## Contributing

We welcome contributions to improve the **go-advanced-admin** project. If you have suggestions or enhancements related 
to models:

- **Contribution Guide:** Review our [Contribution Guide](Contributing.md) for guidelines.
- **Issue Tracker:** Report issues or feature requests on our 
- [GitHub Issues](https://github.com/go-advanced-admin/admin/issues).

---

**Next Steps:**

- Explore how to [Customize Templates](Templates.md) for your models.
- Learn about [](Logging.md) to track changes to your models.
- Dive into [](Integrations.md) for ORM and web framework specifics.

---

**Feedback**

If you have questions or need further assistance, feel free to 
[open an issue](https://github.com/go-advanced-admin/admin/issues/new/choose) or join our community discussions.

---

**Thank you for using go-advanced-admin!**

> This section of the documentation was written with the help of [Infuzu AI](https://infuzu.com)'s tools.
{style="note"}
