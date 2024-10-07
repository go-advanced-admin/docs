# Quick Start

Welcome to the **Go Advanced Admin Panel**! This guide will help you get started with integrating the admin panel into 
your Go application using the Echo web framework and GORM ORM. By the end of this guide, you'll have a basic admin 
panel up and running with a simple `Post` model.

> **Note:** We also provide separate quick start guides for other web frameworks and ORMs. Refer to the 
> [Additional Quick Start Guides](#additional-quick-start-guides) section for more information.

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Step 1: Create a New Go Module](#step-1-create-a-new-go-module)
- [Step 2: Install Dependencies](#step-2-install-dependencies)
- [Step 3: Import Packages](#step-3-import-packages)
- [Step 4: Initialize Echo and GORM](#step-4-initialize-echo-and-gorm)
- [Step 5: Define Your Model](#step-5-define-your-model)
- [Step 6: Migrate the Model](#step-6-migrate-the-model)
- [Step 7: Set Up the Admin Panel](#step-7-set-up-the-admin-panel)
- [Step 8: Register the Model with the Admin Panel](#step-8-register-the-model-with-the-admin-panel)
- [Step 9: Start the Server](#step-9-start-the-server)
- [Step 10: Run the Application](#step-10-run-the-application)
- [Step 11: Access the Admin Panel](#step-11-access-the-admin-panel)
- [Next Steps](#next-steps)
- [Additional Quick Start Guides](#additional-quick-start-guides)
- [Conclusion](#conclusion)

## Introduction

The Go Advanced Admin Panel is a highly configurable and extensible admin interface for Go applications. It allows you 
to manage your application's data through a web interface, with support for various web frameworks and ORMs.

This quick start guide uses **Echo** and **GORM** for demonstration purposes.

## Prerequisites

- Go 1.16 or later installed on your machine.
- Basic knowledge of Go programming.
- Familiarity with Echo and GORM is helpful but not required.

## Step 1: Create a New Go Module

Create a new directory for your project and initialize a Go module.

```bash
mkdir my-admin-panel
cd my-admin-panel
go mod init github.com/yourusername/my-admin-panel
```

## Step 2: Install Dependencies

Install the necessary packages, including the admin panel, Echo, and GORM with the SQLite driver.

```bash
go get github.com/go-advanced-admin/admin
go get github.com/go-advanced-admin/web-echo
go get github.com/go-advanced-admin/orm-gorm
go get github.com/labstack/echo/v4
go get gorm.io/gorm
go get gorm.io/driver/sqlite
```

## Step 3: Import Packages

In your `main.go` file, import the required packages.

```go
package main

import (
    "github.com/go-advanced-admin/admin"
    "github.com/go-advanced-admin/web-echo"
    "github.com/go-advanced-admin/orm-gorm"
    "github.com/labstack/echo/v4"
    "gorm.io/driver/sqlite"
    "gorm.io/gorm"
    "log"
)
```

## Step 4: Initialize Echo and GORM

Set up your Echo web server and GORM database connection.

```go
func main() {
    // Initialize Echo
    e := echo.New()

    // Initialize GORM with SQLite
    db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
    if err != nil {
        log.Fatalf("Failed to connect to database: %v", err)
    }
}
```

## Step 5: Define Your Model

Create a simple `Post` model that represents the data you want to manage.

```go
type Post struct {
    ID      uint   `gorm:"primarykey"`
    Title   string
    Content string
}
```

## Step 6: Migrate the Model

Ensure your database schema is up-to-date by running the migration.

```go
    // Migrate the schema
    err = db.AutoMigrate(&Post{})
    if err != nil {
        log.Fatalf("Failed to migrate database: %v", err)
    }
```

## Step 7: Set Up the Admin Panel

Integrate the admin panel with your Echo server and GORM ORM.

```go
    // Set up web integrator for Echo
    webIntegrator := adminecho.NewIntegrator(e.Group(""))

    // Set up ORM integrator for GORM
    ormIntegrator := admingorm.NewIntegrator(db)

    // Define a simple permission function (allow all actions)
    permissionFunc := func(
        req admin.PermissionRequest, ctx interface{},
    ) (bool, error) {
        return true, nil
    }

    // Create the admin panel
    panel, err := admin.NewPanel(
        ormIntegrator, webIntegrator, permissionFunc, nil,
    )
    if err != nil {
        log.Fatalf("Failed to create admin panel: %v", err)
    }
```

## Step 8: Register the Model with the Admin Panel

Register your `Post` model so it appears in the admin interface.

```go
    // Register an app within the admin panel
    app, err := panel.RegisterApp("BlogApp", "Blog Application", nil)
    if err != nil {
        log.Fatalf("Failed to register app: %v", err)
    }

    // Register the Post model
    _, err = app.RegisterModel(&Post{}, nil)
    if err != nil {
        log.Fatalf("Failed to register model: %v", err)
    }
```

## Step 9: Start the Server

Run your Echo server and access the admin panel.

```go
    // Start the server
    e.Logger.Fatal(e.Start(":8080"))
}
```

## Step 10: Run the Application

Build and run your Go application.

```bash
go run main.go
```

## Step 11: Access the Admin Panel

Open your web browser and navigate to `http://localhost:8080/admin`.

You should see the admin panel interface, where you can manage your `Post` entries.

## Full `main.go` Example

Here's the complete code for your `main.go` file:

```go
package main

import (
    "github.com/go-advanced-admin/admin"
    adminecho "github.com/go-advanced-admin/web-echo"
    admingorm "github.com/go-advanced-admin/orm-gorm"
    "github.com/labstack/echo/v4"
    "gorm.io/driver/sqlite"
    "gorm.io/gorm"
    "log"
)

type Post struct {
    ID      uint   `gorm:"primarykey"`
    Title   string
    Content string
}

func main() {
    // Initialize Echo
    e := echo.New()

    // Initialize GORM with SQLite
    db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
    if err != nil {
        log.Fatalf("Failed to connect to database: %v", err)
    }

    // Migrate the schema
    err = db.AutoMigrate(&Post{})
    if err != nil {
        log.Fatalf("Failed to migrate database: %v", err)
    }

    // Set up web integrator for Echo
    webIntegrator := adminecho.NewIntegrator(e.Group(""))

    // Set up ORM integrator for GORM
    ormIntegrator := admingorm.NewIntegrator(db)

    // Define a simple permission function (allow all actions)
    permissionFunc := func(
        req admin.PermissionRequest, ctx interface{},
    ) (bool, error) {
        return true, nil
    }

    // Create the admin panel
    panel, err := admin.NewPanel(
        ormIntegrator, webIntegrator, permissionFunc, nil,
    )
    if err != nil {
        log.Fatalf("Failed to create admin panel: %v", err)
    }

    // Register an app within the admin panel
    app, err := panel.RegisterApp("BlogApp", "Blog Application", nil)
    if err != nil {
        log.Fatalf("Failed to register app: %v", err)
    }

    // Register the Post model
    _, err = app.RegisterModel(&Post{}, nil)
    if err != nil {
        log.Fatalf("Failed to register model: %v", err)
    }

    // Start the server
    e.Logger.Fatal(e.Start(":8080"))
}
```

## Next Steps

- **Explore the Admin Panel**: Try creating, editing, and deleting `Post` entries.
- **Customize Models**: Learn how to customize model fields, validations, and display options.
- **Implement Permissions**: Replace the basic permission function with a real authentication and authorization system.

## Additional Quick Start Guides

This guide used Echo and GORM for demonstration purposes. We provide separate quick start guides for other web 
frameworks and ORMs:

### Web Frameworks

- [Echo Quick Start Guide](Echo-Quick-Start.md)
- [Gin Quick Start Guide](Gin-Quick-Start.md) (coming soon)
- [Chi Quick Start Guide](Chi-Quick-Start.md) (coming soon)
- [Fiber Quick Start Guide](Fiber-Quick-Start.md) (coming soon)
- [Other Frameworks](Web-Framework-Specific-Quick-Starts.md)

### ORMs

- [GORM Quick Start Guide](GORM-Quick-Start.md)
- [XORM Quick Start Guide](XORM-Quick-Start.md) (coming soon)
- [SQLX Quick Start Guide](SQLX-Quick-Start.md) (coming soon)
- [Bun Quick Start Guide](Bun-Quick-Start.md) (coming soon)
- [Other ORMs](ORM-Specific-Quick-Starts.md)

## Conclusion

You've successfully set up the Go Advanced Admin Panel with Echo and GORM! From here, you can explore more features, 
integrate with other frameworks or ORMs, and customize the admin panel to suit your application's needs.
