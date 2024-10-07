# Templates

The **go-advanced-admin** panel provides a flexible templating system that allows you to customize the look and feel of 
your admin interface. This guide covers how to work with templates, including overriding default templates, creating 
custom templates, and configuring custom assets. If you wish to use a different template renderer than the default, 
refer to the [Template Rendering Guide](Template-Rendering.md).

---

- [Overview](#overview)
- [Default Templates](#default-templates)
- [Overriding Default Templates](#overriding-default-templates)
    - [Locating Default Templates](#locating-default-templates)
    - [Creating Custom Templates](#creating-custom-templates)
    - [Registering Custom Templates](#registering-custom-templates)
- [Templating Language Syntax and Usage](#templating-language-syntax-and-usage)
    - [Variables](#variables)
    - [Control Structures](#control-structures)
    - [Functions](#functions)
    - [Template Inheritance](#template-inheritance)
- [Custom Assets](#custom-assets)
    - [Adding Custom Assets](#adding-custom-assets)
    - [Serving Static Files](#serving-static-files)
- [Using a Custom Template Renderer](#using-a-custom-template-renderer)
- [Next Steps](#next-steps)
- [Feedback](#feedback)

---

## Overview

Templates in **go-advanced-admin** are used to render HTML pages for your admin interface. The default template 
renderer uses Go's `html/template` package, which provides robust functionality for creating dynamic web pages. By 
customizing templates, you can:

- Change the layout and design of the admin panel.
- Add or remove elements from the UI.
- Customize the way data is displayed.

## Default Templates

The admin panel comes with a set of default templates that cover the basic pages and components needed for the 
interface. These templates include:

- **Root Page**: The main dashboard of the admin panel.
- **App Page**: Displays the models registered under an application.
- **Model Page**: Lists instances of a model.
- **Instance Page**: Shows details of a single instance.
- **Form Pages**: Used for adding and editing instances.
- **Log Page**: Displays log entries.

These templates are embedded within the `go-advanced-admin` package and can be overridden or extended as needed.

## Overriding Default Templates

To customize the appearance of your admin panel, you can override the default templates with your own versions.

### Locating Default Templates

While the default templates are embedded within the package, you can refer to the source code to view their structure. 
The templates are written using Go's `html/template` syntax.

### Creating Custom Templates

1. **Create a Custom Template File**: Start by creating a new template file in your project directory, for example, 
`custom_templates/model.html`.

2. **Copy and Modify the Default Template**: You can copy the content of the default template from the source code as a 
starting point and modify it according to your needs.

   ```html
   <!-- custom_templates/model.html -->
   {{ define "model" }}
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>{{ .model.DisplayName }} List</title>
   </head>
   <body>
       <h1>{{ .model.DisplayName }}</h1>
       <!-- Add your custom HTML and templating code here -->
   </body>
   </html>
   {{ end }}
   ```

### Registering Custom Templates

To make the admin panel use your custom templates, you need to register them with the template renderer.

```go
package main

import (
    "github.com/go-advanced-admin/admin"
    "github.com/go-advanced-admin/admin/internal/adminpanel"
)

func main() {
    // Initialize your ORM and web integrator here
    orm := /* your ORM integrator */
    web := /* your Web integrator */
    permissionChecker := /* your permission checker function */
    config := &admin.Config{
        // Other configuration options
        Renderer: adminpanel.NewDefaultTemplateRenderer(),
    }

    // Initialize the admin panel
    panel, err := admin.NewPanel(orm, web, permissionChecker, config)
    if err != nil {
        panic(err)
    }

    // Add your custom template
    templateContent := `{{ define "model" }} ... {{ end }}` // Your custom template content
    err = panel.Config.Renderer.AddCustomTemplate("model.html", templateContent)
    if err != nil {
        panic(err)
    }

    // Register your apps and models here
    // ...
}
```

Alternatively, you can read the template content from your custom template file:

```go
import (
    "io/ioutil"
)

templateContent, err := ioutil.ReadFile("custom_templates/model.html")
if err != nil {
    panic(err)
}

err = panel.Config.Renderer.AddCustomTemplate("model.html", string(templateContent))
if err != nil {
    panic(err)
}
```

**Note**: Ensure that the template name you provide matches the name used in the default templates, so that it 
overrides correctly.

## Templating Language Syntax and Usage

The default template renderer uses Go's `html/template` package. Here's a brief overview of the templating syntax and 
how to use it.

### Variables

Variables are accessed using the `{{ .VariableName }}` syntax.

```html
<p>Welcome, {{ .User.Name }}!</p>
```

### Control Structures

#### If Statements

```html
{{ if .User.IsAdmin }}
  <p>You have administrative privileges.</p>
{{ else }}
  <p>You are a regular user.</p>
{{ end }}
```

#### Range Loops

```html
<ul>
{{ range .Items }}
  <li>{{ . }}</li>
{{ end }}
</ul>
```

### Functions

The template renderer provides built-in functions, and you can define custom functions.

#### Built-in Functions

- `safeHTML`: Marks a string as safe HTML.
- `getFieldValue`: Retrieves a field's value from a struct.
- `assetPath`: Generates the URL for an asset.

**Example**:

```html
<img src="{{ assetPath "logo.png" }}" alt="Logo">
```

#### Custom Functions

You can register custom functions using the `template.FuncMap`.

```go
funcMap := template.FuncMap{
    "upper": strings.ToUpper,
}

renderer := adminpanel.NewDefaultTemplateRenderer()
renderer.RegisterFuncMap(funcMap)
```

Then use it in your templates:

```html
<p>{{ upper .User.Name }}</p>
```

### Template Inheritance

You can define base templates and extend them using the `template` action.

**Base Template (`base.html`)**:

```html
{{ define "base" }}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{{ block "title" . }}Default Title{{ end }}</title>
</head>
<body>
    {{ block "content" . }}{{ end }}
</body>
</html>
{{ end }}
```

**Extended Template (`model.html`)**:

```html
{{ define "model" }}
{{ template "base" . }}

{{ define "title" }}{{ .model.DisplayName }} List{{ end }}

{{ define "content" }}
<h1>{{ .model.DisplayName }}</h1>
<!-- Your content here -->
{{ end }}
{{ end }}
```

## Custom Assets

In addition to templates, you can customize static assets like CSS, JavaScript, and images.

### Adding Custom Assets

1. **Prepare Your Asset Files**: Place your custom assets in a directory within your project, for example, 
`custom_assets/`.

2. **Register Custom Assets**: Use the `AddCustomAsset` method to register each asset.

    ```go
    assetContent, err := ioutil.ReadFile("custom_assets/style.css")
    if err != nil {
        panic(err)
    }

    panel.Config.Renderer.AddCustomAsset("style.css", assetContent)
    ```

3. **Reference Assets in Templates**: Use the `assetPath` function to include your assets in templates.

    ```html
    <link rel="stylesheet" href="{{ assetPath "style.css" }}">
    ```

### Serving Static Files

Your web integrator needs to serve the static files so that they are accessible via HTTP. The admin panel provides a 
method to serve assets using the default renderer.

```go
panel.Web.ServeAssets(panel.Config.AssetsPrefix, panel.Config.Renderer)
```

Ensure that your web integrator's `ServeAssets` method is implemented to serve assets correctly.

## Using a Custom Template Renderer

If you wish to use a different template renderer than the default, you can implement the `TemplateRenderer` interface.

**TemplateRenderer Interface**:

```go
type TemplateRenderer interface {
    RenderTemplate(name string, data map[string]interface{}) (string, error)
    // Other methods...
}
```

### Implementing a Custom Renderer

1. **Create Your Renderer**: Implement all the methods defined in the `TemplateRenderer` interface.

    ```go
    type MyCustomRenderer struct {
        // Your fields here
    }

    func (r *MyCustomRenderer) RenderTemplate(
        name string, data map[string]interface{}, 
    ) (string, error) {
        // Your rendering logic here
        return "", nil
    }

    // Implement other methods...
    ```

2. **Integrate Your Renderer**: Pass an instance of your custom renderer to the admin panel configuration.

```go
config := &admin.Config{
    Renderer: &MyCustomRenderer{},
    // Other configuration options
}
```

3. **Use Your Renderer**: The admin panel will now use your custom renderer for all template rendering tasks.

**Note**: When implementing a custom renderer, ensure that it supports all the necessary functionality required by the 
admin panel templates.

For more details, refer to the [Template Rendering Guide](Template-Rendering.md).

## Next Steps

- **Learn about Forms**: Understand how to customize forms in the [Forms Guide](Forms.md).
- **Explore Models**: Learn how to register and configure models in the [Models Guide](Models.md).
- **Configure Permissions**: Set up access control in the [Permissions Guide](Permissions.md).

## Feedback

We hope this guide helps you customize your admin panel templates effectively. If you have any questions or encounter 
issues, please:

- **Open an Issue**: [GitHub Issue Tracker](https://github.com/go-advanced-admin/admin/issues)
- **Contribute**: See our [Contribution Guide](Contributing.md) for details on how to contribute to the project.

---

**Thank you for using go-advanced-admin!**