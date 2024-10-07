# Echo

## Introduction

Welcome to the comprehensive guide on integrating the **Go Advanced Admin Panel** with the **Echo** web framework. This 
guide aims to provide you with detailed instructions on how to use and customize the Echo integration to seamlessly 
incorporate the admin panel into your Echo applications.

[Echo](https://echo.labstack.com/) is a high-performance, extensible, minimalist Go web framework that is ideal for 
building robust and scalable web applications. By integrating the admin panel with Echo, you can leverage Echo's 
capabilities while providing a powerful administrative interface for managing your application's data and 
configurations.

> **Note:** This guide assumes that you have already completed the [Echo Quick Start Guide](Echo-Quick-Start.md) and 
> are familiar with setting up a basic admin panel with Echo. If you haven't, please refer to the Quick Start Guide 
> before proceeding.

## Prerequisites

Before diving into the integration, ensure you have the following:

- **Go** installed on your system.
- A working **Echo** application.
- Familiarity with **GORM** (Go ORM) for database interactions.
- The following packages installed:

  ```bash
  go get github.com/go-advanced-admin/admin
  go get github.com/go-advanced-admin/web-echo
  go get github.com/go-advanced-admin/orm-gorm
  ```

## Setting Up the Echo Integration

### Import Necessary Packages

In your main application file, import the required packages:

```go
import (
    "log"

    "github.com/go-advanced-admin/admin"
    "github.com/go-advanced-admin/web-echo"
    "github.com/go-advanced-admin/orm-gorm"
    "github.com/labstack/echo/v4"
    "gorm.io/driver/sqlite"
    "gorm.io/gorm"
)
```

### Initialize Echo and the Admin Panel

Set up your Echo instance and initialize the admin panel with GORM and Echo integrations:

```go
func main() {
    // Initialize Echo
    e := echo.New()

    // Initialize GORM with a SQLite database (for example)
    db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
    if err != nil {
        log.Fatal(err)
    }

    // Create a new GORM integrator
    gormIntegrator := admingorm.NewIntegrator(db)

    // Create a new Echo integrator
    echoIntegrator := adminecho.NewIntegrator(e.Group(""))

    // Define your permission checker function
    permissionChecker := func(
        request admin.PermissionRequest, ctx interface{},
    ) (bool, error) {
        // Implement your permission logic here
        return true, nil
    }

    // Create a new admin panel
    adminPanel, err := admin.NewPanel(
        gormIntegrator, echoIntegrator, permissionChecker, nil, 
    )
    if err != nil {
        log.Fatal(err)
    }

    // Register applications and models (explained later)

    // Start the Echo server
    e.Logger.Fatal(e.Start(":8080"))
}
```

## Using the Echo Integration

### Handling Routes

The Echo integrator handles route registration by mapping admin panel routes to Echo handlers. When you use the 
`HandleRoute` method, routes are added to the Echo group:

```go
func (i *Integrator) HandleRoute(
    method, path string, handler admin.HandlerFunc, 
) {
    i.group.Add(method, path, func(c echo.Context) error {
        code, body := handler(c)
        if code == http.StatusFound || code == http.StatusMovedPermanently || code == http.StatusSeeOther {
            return c.Redirect(int(code), body)
        }
        return c.HTML(int(code), body)
    })
}
```

This method ensures that HTTP responses from the admin panel are correctly handled within the Echo framework.

### Serving Static Assets

To serve static assets (like CSS and JavaScript files) required by the admin panel, use the `ServeAssets` method:

```go
func (i *Integrator) ServeAssets(
    prefix string, renderer admin.TemplateRenderer,
) {
    i.group.GET(
        fmt.Sprintf("/%s/*", prefix), func(c echo.Context) error {
        fileName := c.Param("*")
        fileData, err := renderer.GetAsset(fileName)
        if err != nil {
            return c.NoContent(http.StatusNotFound)
        }

        contentType := mime.TypeByExtension(filepath.Ext(fileName))
        if contentType == "" {
            contentType = "application/octet-stream"
        }
        return c.Blob(http.StatusOK, contentType, fileData)
    })
}
```

This method uses the `admin.TemplateRenderer` to retrieve asset files and serves them through Echo's routing system.

### Extracting Request Data

The Echo integrator provides methods to extract query parameters, path parameters, request methods, and form data from 
the Echo context:

```go
func (i *Integrator) GetQueryParam(
    ctx interface{}, name string, 
) string {
    ec := ctx.(echo.Context)
    return ec.QueryParam(name)
}

func (i *Integrator) GetPathParam(
    ctx interface{}, name string,
) string {
    ec := ctx.(echo.Context)
    return ec.Param(name)
}

func (i *Integrator) GetRequestMethod(ctx interface{}) string {
    ec := ctx.(echo.Context)
    return ec.Request().Method
}

func (i *Integrator) GetFormData(ctx interface{}) map[string][]string {
    ec := ctx.(echo.Context)
    if err := ec.Request().ParseForm(); err != nil {
        return nil
    }
    return ec.Request().Form
}
```

These methods are used by the admin panel to handle HTTP requests and extract necessary data for processing.

## Customizing the Echo Integration

### Custom Route Handling

You can customize route handling by adding middleware or modifying the Echo group before integrating it with the admin 
panel:

```go
// Create a new Echo group with middleware
adminGroup := e.Group("/admin", yourMiddleware)

// Create the Echo integrator with the customized group
echoIntegrator := adminecho.NewIntegrator(adminGroup)

// Initialize the admin panel with the custom integrator
adminPanel, err := admin.NewPanel(
    gormIntegrator, echoIntegrator, permissionChecker, nil, 
)
if err != nil {
    log.Fatal(err)
}
```

### Error Handling

Echo allows you to define custom error handlers. You can set a global error handler to manage errors occurring within 
the admin panel routes:

```go
e.HTTPErrorHandler = func(err error, c echo.Context) {
    // Implement your custom error handling logic
    c.JSON(
        http.StatusInternalServerError, 
        map[string]string{"error": err.Error()},
    )
}
```

### Advanced Customization

For advanced customization, you can
[implement your own version of the `admin.WebIntegrator`](Building-a-Custom-Web-Framework-Integration.md) interface to 
have full control over request handling:

```go
type CustomIntegrator struct {
    group *echo.Group
}

// Implement the required methods
func (i *CustomIntegrator) HandleRoute(
    method, path string, handler admin.HandlerFunc, 
) {
    // Custom route handling
}

func (i *CustomIntegrator) ServeAssets(
    prefix string, renderer admin.TemplateRenderer, 
    ) {
    // Custom asset serving
}

// ... Implement other methods
```

By creating a custom integrator, you can integrate the admin panel into your Echo application in a way that best suits 
your requirements.

## Understanding the GORM Integration

The admin panel uses an ORM integrator to interact with the database. The GORM integration (`admingorm`) allows you to 
manage your database models seamlessly.

### Registering Models with GORM

Define your models using GORM and register them with the admin panel:

```go
type User struct {
    ID       uint   `gorm:"primaryKey"`
    Name     string
    Email    string
    Password string
}

func main() {
    // ... Initialization code

    // Auto-migrate your models
    db.AutoMigrate(&User{})

    // Register an application
    userApp, err := adminPanel.RegisterApp(
        "user_app", "User Management", nil, 
    )
    if err != nil {
        log.Fatal(err)
    }

    // Register the User model
    _, err = userApp.RegisterModel(&User{}, nil)
    if err != nil {
        log.Fatal(err)
    }

    // Start the Echo server
    e.Logger.Fatal(e.Start(":8080"))
}
```

## Contributing to the Echo Integration

We welcome contributions to enhance the Echo integration. If you encounter issues or have ideas for improvements, 
please:

- **Report Issues:** Use the [GitHub Issues](https://github.com/go-advanced-admin/adminecho/issues) page to report bugs 
or request features.
- **Submit Pull Requests:** If you have code changes, submit a pull request following our
[Contribution Guidelines](Contributing.md).
- **Join Discussions:** Participate in discussions to share your insights and collaborate with the community.

Your contributions help make the project better for everyone!

## Conclusion

Integrating the Go Advanced Admin Panel with Echo allows you to build a powerful and customizable administrative 
interface for your web applications. By understanding and utilizing the Echo and GORM integrations, you can manage your 
application's data effectively and tailor the admin panel to meet your specific needs.

Explore other sections of the documentation to learn more about advanced features, customization options, and best 
practices.

---

> For further assistance or questions, please refer to the [official documentation](Welcome.md) or
> [reach out to the community](https://github.com/go-advanced-admin/web-echo/discussions).*

> This section of the documentation was written with the help of [Infuzu AI](https://infuzu.com)'s tools.
{style="note"}
