# Building a Custom Web Framework Integration

## Introduction

The **Go Advanced Admin Panel** is designed to be flexible and extensible, allowing seamless integration with various 
web frameworks. While we provide integrations for popular frameworks like Gin, Echo, Chi, and Fiber, you might be using 
a different web framework or have specific requirements that necessitate a custom integration.

This guide will walk you through the process of building a custom web framework integration for the Go Advanced Admin 
Panel. We'll cover:

- Understanding the required interface.
- Implementing each method.
- Providing a full example.
- Encouraging contributions back to the community.

By the end of this guide, you'll have a fully functional integrator for your preferred web framework.

## Requirements

To build a custom web framework integration, you'll need:

- Familiarity with Go and your chosen web framework.
- Understanding of how your framework handles routing, middleware, and context.
- Knowledge of the `admin.WebIntegrator` interface.

## The `WebIntegrator` Interface

The `WebIntegrator` interface defines the methods your custom integrator must implement to interact with the admin 
panel. Here's the interface:

```go
type HandlerFunc = func(interface{}) (uint, string)

type WebIntegrator interface {
    HandleRoute(method, path string, handler HandlerFunc)
    ServeAssets(prefix string, renderer TemplateRenderer)
    GetQueryParam(ctx interface{}, name string) string
    GetPathParam(ctx interface{}, name string) string
    GetRequestMethod(ctx interface{}) string
    GetFormData(ctx interface{}) map[string][]string
}
```

Your custom integrator will bridge the admin panel's needs with your web framework's specifics by implementing these 
methods.

## Step-by-Step Guide

### 1. Create the Integrator Struct

Start by creating a new package and defining an `Integrator` struct that will hold any necessary references to your web 
framework's components (e.g., router, app instance).

```go
package myframeworkadmin

import (
    "github.com/go-advanced-admin/admin"
    "myframework"
)

type Integrator struct {
    app *myframework.App
}

func NewIntegrator(app *myframework.App) *Integrator {
    return &Integrator{app: app}
}
```

### 2. Implement `HandleRoute`

This method registers routes with your web framework. It maps HTTP methods and paths to handler functions.

```go
func (i *Integrator) HandleRoute(
    method, path string, handler admin.HandlerFunc, 
) {
    i.app.Handle(method, path, func(ctx *myframework.Context) {
        code, body := handler(ctx)
        if code >= 300 && code < 400 {
            ctx.Redirect(int(code), body)
        } else {
            ctx.Response.WriteHeader(int(code))
            ctx.Response.Write([]byte(body))
        }
    })
}
```

**Note:** Replace `myframework.App`, `myframework.Context`, and method calls with equivalents from your framework.

### 3. Implement `ServeAssets`

This method serves static assets (like CSS and JavaScript files) required by the admin panel.

```go
func (i *Integrator) ServeAssets(
    prefix string, renderer admin.TemplateRenderer, 
) {
    i.app.Handle(
        "GET", 
        fmt.Sprintf("/%s/*filepath", prefix), 
        func(ctx *myframework.Context) {
        fileName := ctx.Param("filepath")
        fileData, err := renderer.GetAsset(fileName)
        if err != nil {
            ctx.Response.WriteHeader(http.StatusNotFound)
            return
        }

        contentType := mime.TypeByExtension(filepath.Ext(fileName))
        if contentType == "" {
            contentType = "application/octet-stream"
        }
        ctx.Response.Header().Set("Content-Type", contentType)
        ctx.Response.Write(fileData)
    })
}
```

### 4. Implement Request Helpers

#### `GetQueryParam`

Extracts query parameters from the request context.

```go
func (i *Integrator) GetQueryParam(
    ctx interface{}, name string, 
) string {
    c := ctx.(*myframework.Context)
    return c.QueryParam(name)
}
```

#### `GetPathParam`

Extracts path parameters from the request context.

```go
func (i *Integrator) GetPathParam(
    ctx interface{}, name string, 
) string {
    c := ctx.(*myframework.Context)
    return c.Param(name)
}
```

#### `GetRequestMethod`

Retrieves the HTTP method of the request.

```go
func (i *Integrator) GetRequestMethod(ctx interface{}) string {
    c := ctx.(*myframework.Context)
    return c.Request.Method
}
```

#### `GetFormData`

Retrieves form data from the request.

```go
func (i *Integrator) GetFormData(ctx interface{}) map[string][]string {
    c := ctx.(*myframework.Context)
    if err := c.Request.ParseForm(); err != nil {
        return nil
    }
    return c.Request.Form
}
```

### 5. Full Example: Echo Framework Integration

To illustrate, here's the full code for an Echo framework integrator:

```go
package adminecho

import (
    "fmt"
    "github.com/go-advanced-admin/admin"
    "github.com/labstack/echo/v4"
    "mime"
    "net/http"
    "path/filepath"
)

type Integrator struct {
    group *echo.Group
}

func NewIntegrator(g *echo.Group) *Integrator {
    return &Integrator{group: g}
}

func (i *Integrator) HandleRoute(
    method, path string, handler admin.HandlerFunc, 
) {
    i.group.Add(method, path, func(c echo.Context) error {
        code, body := handler(c)
        if code >= 300 && code < 400 {
            return c.Redirect(int(code), body)
        }
        return c.HTML(int(code), body)
    })
}

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

### 6. Register Your Integrator with the Admin Panel

Once your integrator is ready, use it to initialize the admin panel:

```go
import (
    "github.com/go-advanced-admin/admin"
    "myframeworkadmin"
    "myorm"
    "mypermissions"
)

func main() {
    app := myframework.NewApp()
    integrator := myframeworkadmin.NewIntegrator(app)

    panel, err := admin.NewPanel(
        myorm.NewORM(), integrator, mypermissions.Checker, nil, 
    )
    if err != nil {
        panic(err)
    }

    // Register apps and models...

    app.Start(":8080")
}
```

## Encouragement to Contribute

### Share with the Community

If you've built a custom integrator, we encourage you to share it with the community! Your contribution can help others 
who use the same web framework.

### Contribution Guide

For detailed instructions, please refer to our [Contribution Guide](Contributing.md).

## Conclusion

Building a custom web framework integration involves implementing the `WebIntegrator` interface, which bridges the 
admin panel with your framework's routing and context systems. With this guide and the provided examples, you should be 
able to create an integrator tailored to your needs.

If you have questions or need assistance, feel free to open an issue on GitHub or reach out to the community.

**Happy coding!**

> This section of the documentation was written with the help of [Infuzu AI](https://infuzu.com)'s tools.
{style="note"}
