# Generic Quick Start
This guide will walk you through setting up the Advanced Admin Panel with a common web framework and ORM in Go. We'll use Echo and GORM for this example.

## Prerequisites
- Go installed on your machine. 
- Basic knowledge of Go programming. 
- Familiarity with web frameworks and ORMs in Go.

## Installation
Install the necessary packages:
```bash
go get github.com/go-advanced-admin/admin
go get github.com/go-advanced-admin/orm-gorm
go get github.com/go-advanced-admin/web-echo
go get github.com/labstack/echo/v4
go get gorm.io/gorm
go get gorm.io/driver/sqlite
```

## Basic Setup
Create a new Go project and initialize your `main.go` file:
```go
package main

import (
    "log"
    "github.com/go-advanced-admin/admin"
    "github.com/go-advanced-admin/orm-gorm"
    "github.com/go-advanced-admin/web-echo"
    "github.com/labstack/echo/v4"
    "gorm.io/driver/sqlite"
    "gorm.io/gorm"
)

func main() {
    // Initialize Echo web framework
    e := echo.New()

    // Initialize GORM with SQLite
    db, err := gorm.Open(sqlite.Open("admin.db"), &gorm.Config{})
    if err != nil {
        log.Fatalf("Failed to connect to database: %v", err)
    }

    // Create the ORM integrator
    ormIntegrator := admingorm.NewIntegrator(db)

    // Create the web integrator
    webIntegrator := adminecho.NewIntegrator(e.Group(""))

    // Define a permission function
    permissionFunc := func(req admin.PermissionRequest, ctx interface{}) (bool, error) {
        // Allow all actions for simplicity
        return true, nil
    }

    // Initialize the admin panel
    panel, err := admin.NewPanel(ormIntegrator, webIntegrator, permissionFunc, nil)
    if err != nil {
        log.Fatalf("Failed to initialize admin panel: %v", err)
    }

    // Start the Echo server
    e.Logger.Fatal(e.Start(":8080"))
}
```

## Registering a Simple Model
Create a simple `Post` model and register it with the admin panel:
```go 
type Post struct {
    ID      uint   `gorm:"primarykey"`
    Title   string
    Content string
}

func main() {
    // ... previous setup code ...

    // Auto-migrate the Post model
    err = db.AutoMigrate(&Post{})
    if err != nil {
        log.Fatalf("Failed to migrate database: %v", err)
    }

    // Register the Post model with the admin panel
    app, err := panel.RegisterApp("Blog", "Blog Management", nil)
    if err != nil {
        log.Fatalf("Failed to register app: %v", err)
    }

    _, err = app.RegisterModel(&Post{}, nil)
    if err != nil {
        log.Fatalf("Failed to register model: %v", err)
    }

    // Start the Echo server
    e.Logger.Fatal(e.Start(":8080"))
}
```

## Running the Admin Panel
Run your application:
```bash
go run main.go
```
Navigate to `http://localhost:8080/admin` in your browser to access the admin panel.