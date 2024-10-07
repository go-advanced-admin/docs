# Configuration

The **go-advanced-admin** panel is designed to be highly configurable, allowing you to tailor it to your specific 
needs. This guide provides a comprehensive overview of all the configuration options available in the admin panel. 
You'll learn how to set up database connections, authentication, authorization, logging, templating, and manage static 
files.

---

## Table of Contents

- [Introduction](#introduction)
- [AdminConfig Structure](#adminconfig-structure)
- [Configuration Options](#configuration-options)
    - [Name](#name)
    - [Prefix](#prefix)
    - [Renderer](#renderer)
    - [AssetsPrefix](#assetsprefix)
    - [GroupPrefix](#groupprefix)
    - [DefaultInstancesPerPage](#defaultinstancesperpage)
    - [NavBarGenerators](#navbargenerators)
    - [UserFetcher](#userfetcher)
    - [LogStore](#logstore)
    - [LogStoreLevel](#logstorelevel)
- [Database Connection](#database-connection)
- [Authentication](#authentication)
- [Authorization](#authorization)
- [Logging](#logging)
- [Templating](#templating)
- [Static Files](#static-files)
- [Default Configuration](#default-configuration)
- [Next Steps](#next-steps)
- [Feedback](#feedback)

---

## Introduction

Configuring the admin panel correctly is crucial for ensuring that it works seamlessly with your application's 
requirements. The configuration is managed through the `AdminConfig` struct, which provides various options to 
customize the behavior and appearance of the admin panel.

---

## AdminConfig Structure

The `AdminConfig` struct is the core of the configuration system. It includes several fields that allow you to 
customize different aspects of the admin panel.

```go
type AdminConfig struct {
    Name                    string
    Prefix                  string
    Renderer                TemplateRenderer
    AssetsPrefix            string
    GroupPrefix             string
    DefaultInstancesPerPage uint
    NavBarGenerators        []NavBarGenerator
    UserFetcher             UserFetchFunction
    LogStore                logging.LogStore
    LogStoreLevel           logging.LogStoreLevel
}
```

---

## Configuration Options

### Name

- **Type**: `string`
- **Description**: The name of your admin panel. This will be displayed in the UI, typically in the header or title.
- **Default**: `"Site Administration"`

**Example:**

```go
config := &admin.Config{
    Name: "My Custom Admin Panel",
}
```

### Prefix

- **Type**: `string`
- **Description**: The URL prefix for the admin panel routes. This allows you to mount the admin panel under a specific 
path.
- **Default**: `"admin"`

**Example:**

```go
config := &admin.Config{
    Prefix: "dashboard",
}
```

With this configuration, the admin panel will be accessible at `/dashboard`.

### Renderer

- **Type**: `TemplateRenderer`
- **Description**: Handles template rendering for the admin panel. You can use the default renderer or provide a 
custom one to change how templates are processed.
- **Default**: `NewDefaultTemplateRenderer()`

**Example:**

```go
renderer := NewCustomTemplateRenderer()
config := &admin.Config{
    Renderer: renderer,
}
```

### AssetsPrefix

- **Type**: `string`
- **Description**: The URL prefix for static assets like CSS and JavaScript files.
- **Default**: `"admin-assets"`

**Example:**

```go
config := &admin.Config{
    AssetsPrefix: "static-assets",
}
```

Static files will be served from `/static-assets`.

### GroupPrefix

- **Type**: `string`
- **Description**: An additional prefix that can be used if you're grouping multiple services under a common path.
- **Default**: `""`

**Example:**

```go
config := &admin.Config{
    GroupPrefix: "/api/v1",
}
```

All admin routes will be prefixed with `/api/v1`.

### DefaultInstancesPerPage

- **Type**: `uint`
- **Description**: The default number of instances (records) to display per page in list views.
- **Default**: `10`

**Example:**

```go
config := &admin.Config{
    DefaultInstancesPerPage: 20,
}
```

### NavBarGenerators

- **Type**: `[]NavBarGenerator`
- **Description**: A slice of functions that generate navigation bar items. This allows you to customize the navigation 
bar dynamically based on the context.
- **Default**: A set of default navigation items.

**Example:**

```go
config := &admin.Config{
    NavBarGenerators: []NavBarGenerator{
        func(ctx interface{}) NavBarItem {
            return NavBarItem{Name: "Home", Link: "/"}
        },
        func(ctx interface{}) NavBarItem {
            return NavBarItem{Name: "Profile", Link: "/profile"}
        },
    },
}
```

### UserFetcher

- **Type**: `func(ctx interface{}) (userID interface{}, repr string, err error)`
- **Description**: A function used to fetch the current user's information from the context. This is essential for 
logging and permissions.
- **Default**: `nil`

**Example:**

```go
config := &admin.Config{
    UserFetcher: func(ctx interface{}) (interface{}, string, error) {
        user, ok := ctx.(*User)
        if !ok {
            return nil, "", fmt.Errorf("invalid user context")
        }
        return user.ID, user.Username, nil
    },
}
```

### LogStore

- **Type**: `logging.LogStore`
- **Description**: Manages how logs are stored. You can use the default in-memory log store or implement a custom one.
- **Default**: `logging.NewInMemoryLogStore(100)`

**Example:**

```go
customLogStore := NewCustomLogStore()
config := &admin.Config{
    LogStore: customLogStore,
}
```

### LogStoreLevel

- **Type**: `logging.LogStoreLevel`
- **Description**: Determines the verbosity level of logging. Higher levels include all lower levels.
- **Default**: `logging.LogStoreLevelPanelView`

**Available Levels:**

- `logging.LogStoreLevelDelete`
- `logging.LogStoreLevelCreate`
- `logging.LogStoreLevelUpdate`
- `logging.LogStoreLevelInstanceView`
- `logging.LogStoreLevelListView`
- `logging.LogStoreLevelPanelView`

**Example:**

```go
config := &admin.Config{
    LogStoreLevel: logging.LogStoreLevelUpdate,
}
```

With this configuration, logs for updates, deletes, and creates will be recorded.

---

## Database Connection

While the database connection isn't directly configured within `AdminConfig`, it's essential to set up your ORM 
integrator correctly. The admin panel uses an ORM integrator to interact with your database models.

**Example with GORM:**

```go
import (
    "github.com/go-advanced-admin/admin"
    "github.com/go-advanced-admin/gorm-integrator"
    "gorm.io/gorm"
)

func main() {
    db, err := gorm.Open(...)
    if err != nil {
        panic("failed to connect database")
    }

    ormIntegrator := gormIntegrator.New(db)
    panel, err := admin.NewPanel(
        ormIntegrator, webIntegrator, permissionChecker, nil, 
    )
    if err != nil {
        panic(err)
    }
}
```

---

## Authentication

Authentication is managed through the `UserFetcher` function and the context provided by your web framework.

**Setting Up UserFetcher:**

```go
config := &admin.Config{
    UserFetcher: func(ctx interface{}) (interface{}, string, error) {
        requestContext, ok := ctx.(YourRequestContextType)
        if !ok {
            return nil, "", fmt.Errorf("invalid context type")
        }

        user := requestContext.User
        if user == nil {
            return nil, "", fmt.Errorf("user not authenticated")
        }

        return user.ID, user.Username, nil
    },
}
```

This function extracts the user information from the request context, which is then used for permissions and logging.

---

## Authorization

Authorization is handled by implementing the `PermissionFunc`, which checks if a user has the necessary permissions to 
perform certain actions.

For more information on the permission checker, please check out the [Permissions Guide](Permissions.md).

**Example Permission Checker:**

```go
permissionChecker := func(
    req admin.PermissionRequest, ctx interface{}, 
) (bool, error) {
    userID, _, err := config.UserFetcher(ctx)
    if err != nil {
        return false, err
    }

    // Implement your permission logic here
    if userID == nil {
        return false, nil
    }

    // For simplicity, allow all actions
    return true, nil
}
```

**Registering the Permission Checker:**

```go
panel, err := admin.NewPanel(
    ormIntegrator, webIntegrator, permissionChecker, config, 
)
```

---

## Logging

### Configuring the Log Store Level

The `LogStoreLevel` determines what actions are logged.

**Example:**

```go
config := &admin.Config{
    LogStoreLevel: logging.LogStoreLevelCreate,
}
```

This will log all create actions and any actions with a higher priority.

### Configuring the Log Store

You can use the default in-memory log store or implement a custom one.

For more information on this, please check out the [Logging Guide](Logging.md).

**Using the Default Log Store:**

```go
config := &admin.Config{
    LogStore: logging.NewInMemoryLogStore(100),
}
```

**Implementing a Custom Log Store:**

```go
type CustomLogStore struct {
    // Your implementation
}

func (cls *CustomLogStore) InsertLogEntry(
    logEntry *logging.LogEntry, 
) error {
    // Handle log insertion
}

func (cls *CustomLogStore) GetLogEntry(id interface{}) (*logging.LogEntry, error) {
    // Retrieve a single log entry
}

func (cls *CustomLogStore) GetLogEntries() ([]*logging.LogEntry, error) {
    // Retrieve all log entries
}
```

**Registering the Custom Log Store:**

```go
customLogStore := &CustomLogStore{}
config := &admin.Config{
    LogStore: customLogStore,
}
```

---

## Templating

The admin panel uses a `TemplateRenderer` to render HTML templates. You can customize this to change the look and feel 
of the admin panel.

For more information on this, please check out the [Template Rendering Guide](Template-Rendering.md) and the 
[Templates Guide](Templates.md).

### Using the Default Renderer

The default renderer uses Go's `html/template` package.

```go
config := &admin.Config{
    Renderer: admin.NewDefaultTemplateRenderer(),
}
```

### Implementing a Custom Renderer

```go
type CustomTemplateRenderer struct {
    // Your implementation
}

func (ctr *CustomTemplateRenderer) RenderTemplate(
    name string, data map[string]interface{}, 
) (string, error) {
    // Custom rendering logic
}

config := &admin.Config{
    Renderer: &CustomTemplateRenderer{},
}
```

---

## Static Files

Static files like CSS and JavaScript are served from the path specified by `AssetsPrefix`.

### Configuring AssetsPrefix

```go
config := &admin.Config{
    AssetsPrefix: "assets",
}
```

Static files will now be served from `/assets`.

### Serving Custom Static Files

If you have custom static files, ensure that your web framework is configured to serve them from the `AssetsPrefix` 
path.

---

## Default Configuration

If you don't provide a custom configuration, the admin panel uses the `DefaultAdminConfig`:

```go
var DefaultAdminConfig = &admin.Config{
    Name:                    "Site Administration",
    Prefix:                  "admin",
    Renderer:                admin.NewDefaultTemplateRenderer(),
    AssetsPrefix:            "admin-assets",
    GroupPrefix:             "",
    DefaultInstancesPerPage: 10,
    NavBarGenerators:        defaultNavBarGenerators,
    UserFetcher:             nil,
    LogStore:                logging.NewInMemoryLogStore(100),
    LogStoreLevel:           logging.LogStoreLevelPanelView,
}
```

This default configuration sets up the admin panel with sensible defaults, but you can override any of these settings 
by providing your own configuration.

---

## Next Steps

- **[Permissions Guide](Permissions.md)**: Learn how to implement custom permissions.
- **[Templates Guide](Templates.md)**: Customize the look and feel of the admin panel.
- **[Logging Guide](Logging.md)**: Dive deeper into configuring logging.

---

## Feedback

We strive to keep our documentation clear and up-to-date. If you have any feedback or suggestions, please let us know 
by [opening an issue](https://github.com/go-advanced-admin/admin/issues/new/choose) or submitting a pull request.

---

**Thank you for using go-advanced-admin!**


> This section of the documentation was written with the help of [Infuzu AI](https://infuzu.com)'s tools.
{style="note"}
