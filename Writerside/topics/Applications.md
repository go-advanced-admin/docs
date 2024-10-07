# Applications

Applications in **go-advanced-admin** allow you to organize your admin panel into modular components. Each application 
can contain multiple models and has its own configuration options, such as routing and middleware. This modular 
approach helps manage large projects by grouping related models and configurations together.

This guide covers:

- [Registering an Application](#registering-an-application)
- [App-Level Configuration Options](#app-level-configuration-options)
- [Next Steps](#next-steps)

---

## Registering an Application

To register an application in your admin panel, you'll use the `RegisterApp` method provided by the `AdminPanel`. 
Here's how you can do it:

### Step 1: Initialize the Admin Panel

First, ensure you have an instance of `AdminPanel`. If you haven't set this up yet, refer to the 
[Quick Start Guide](Quick-Start.md).

```go
import (
    "log"
    "github.com/go-advanced-admin/admin"
    // Import your ORM and web framework integrations
)

func main() {
    ormIntegrator := /* Initialize your ORM integrator */
    webIntegrator := /* Initialize your web integrator */
    permissionsCheck := /* Define your permission checker function */
    config := /* Define your admin panel configuration */

    adminPanel, err := admin.NewPanel(
        ormIntegrator, webIntegrator, permissionsCheck, &config, 
    )
    if err != nil {
        log.Fatalf("Failed to initialize admin panel: %v", err)
    }
}
```

### Step 2: Register an Application

Use the `RegisterApp` method to create a new application.

```go
appName := "blog"
displayName := "Blog Administration"

app, err := adminPanel.RegisterApp(appName, displayName, nil) // Use `nil` to use the default ORM
if err != nil {
    log.Fatalf("Failed to register app: %v", err)
}
```

**Parameters:**

- `appName` (`string`): The unique identifier for your application. It should be URL-safe (only letters, numbers, 
underscores, and hyphens).
- `displayName` (`string`): The human-readable name displayed in the admin panel.
- `orm` (`ORMIntegrator`): Optional. Use a custom ORM integrator for this app. Pass `nil` to use the default ORM.

### Step 3: Register Models within the Application

After registering the application, you can add models to it. Refer to the [Models Guide](Models.md) for detailed 
instructions.

```go
type Post struct {
    ID      uint   `gorm:"primaryKey"`
    Title   string
    Content string
}

_, err = app.RegisterModel(&Post{}, nil)
if err != nil {
    log.Fatalf("Failed to register model: %v", err)
}
```

---

## App-Level Configuration Options

Applications offer several configuration options to customize their behavior.

### Routing

Each application has its own routing prefix, which is derived from the app name. You can access the app via:

```
/admin/a/<appName>
```

For example, if your `appName` is `"blog"`, the URL would be `/admin/a/blog`.

### Custom ORM Integrator

If you need a different ORM for a specific application, you can provide a custom ORM integrator when registering the 
app.

```go
customORM := /* Initialize your custom ORM integrator */

app, err := adminPanel.RegisterApp(appName, displayName, customORM)
if err != nil {
    log.Fatalf("Failed to register app with custom ORM: %v", err)
}
```

### Middleware

Currently, middleware is managed at the web framework integration level. To apply middleware to a specific application, 
you should configure it within your web framework.

**Example with Gin:**

```go
router := gin.Default()

// Apply middleware to the admin routes
adminGroup := router.Group("/admin", yourMiddleware)
adminPanel.Web.SetRouter(adminGroup)
```

### Custom Template Renderer

If you need custom templates for a specific application, you can set a custom template renderer.

```go
customRenderer := /* Initialize your custom template renderer */

app.SetTemplateRenderer(customRenderer)
```

**Note:** Ensure your custom renderer implements the `TemplateRenderer` interface.

---

## Next Steps

- **Register Models:** Now that you've registered an application, learn how to [register models](Models.md) within it.
- **Configure Permissions:** Set up permissions for your application by defining a 
[permission checker function](Permissions.md).
- **Contribute:** If you'd like to contribute to the project, please see our [Contribution Guide](Contributing.md).

---

## Troubleshooting

If you encounter issues while registering an application, consider the following:

- **Unique App Name:** Ensure the `appName` is unique and URL-safe.
- **ORM Integrator:** If you're using a custom ORM integrator, verify that it implements the required methods.
- **Error Messages:** Check the error messages returned when calling `RegisterApp` for hints.

---

## References

- **[Quick Start Guide](Quick-Start.md)**
- **[Models Guide](Models.md)**
- **[Permissions Guide](Permissions.md)**
- **[Contribution Guide](Contributing.md)**
- **[Issue Tracker](https://github.com/go-advanced-admin/admin/issues)**

---

### Feedback

We welcome your feedback! If you have suggestions or find issues, please 
[open an issue](https://github.com/go-advanced-admin/admin/issues/new/choose) or submit a pull request.

---

Thank you for using **go-advanced-admin**!