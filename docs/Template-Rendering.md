# Template Rendering

The **go-advanced-admin** panel is highly customizable, and one of its core strengths lies in its flexible template 
rendering system. This allows you to control how your admin panel looks and feels, ensuring it aligns perfectly with 
your project's branding and user experience requirements.

This page provides a comprehensive guide on how template rendering works in the admin panel, how to customize it, and 
how to build your own template renderer if needed.

---

- [Overview](#overview)
- [Default Template Renderer](#default-template-renderer)
    - [Features of the Default Renderer](#features-of-the-default-renderer)
- [Customizing Templates](#customizing-templates)
    - [Overriding Default Templates](#overriding-default-templates)
    - [Creating Custom Templates](#creating-custom-templates)
- [Configuring the Template Renderer](#configuring-the-template-renderer)
    - [Setting Up the Renderer](#setting-up-the-renderer)
    - [Registering Templates and Assets](#registering-templates-and-assets)
- [Building a Custom Template Renderer](#building-a-custom-template-renderer)
    - [Implementing the Required Interface](#implementing-the-required-interface)
    - [Integrating the Custom Renderer](#integrating-the-custom-renderer)
- [Contributing Your Renderer](#contributing-your-renderer)
- [Further Reading](#further-reading)

---

## Overview

Template rendering in **go-advanced-admin** is responsible for generating the HTML that is sent to the user's browser. 
By default, the admin panel uses Go's standard `html/template` package for rendering templates. This ensures 
compatibility and ease of use for Go developers.

However, the system is designed to be extensible. If you prefer to use a different templating engine or require 
advanced customization, you can build your own template renderer and integrate it with the admin panel.

---

## Default Template Renderer

### Features of the Default Renderer

The default template renderer provided by **go-advanced-admin** offers the following features:

- **Uses Go's `html/template` Package**: Familiar syntax for Go developers.
- **Supports Template Inheritance**: Allows for base templates and extending templates.
- **Custom Functions**: Includes built-in template functions and allows registration of custom functions.
- **Asset Management**: Manages static assets like CSS and JavaScript files.
- **Default Templates and Assets**: Comes with a set of default templates and assets to get you started quickly.

The default renderer is suitable for most projects, but if you have specific needs, you can customize it or replace it 
entirely.

---

## Customizing Templates

To change the look and feel of your admin panel, you can customize the templates used by the default renderer.

### Overriding Default Templates

You can override the default templates without modifying the core code. This is done by registering your custom 
templates with the renderer.

**Example:**

```go
renderer := admin.NewDefaultTemplateRenderer()
err := renderer.AddCustomTemplate("base.html", customBaseTemplate)
if err != nil {
    log.Fatal(err)
}
```

### Creating Custom Templates

If the default templates don't meet your needs, you can create entirely new templates from scratch.

- **Define Your Template Files**: Create `.html` files using Go's template syntax.
- **Register Templates**: Use the renderer's methods to register your templates.

For more details on template customization, refer to the [](Templates.md) page.

---

## Configuring the Template Renderer

The template renderer can be configured to suit your project's requirements.

### Setting Up the Renderer

When initializing the admin panel, you can provide your own renderer:

```go
renderer := admin.NewDefaultTemplateRenderer()
// Customize the renderer as needed

config := &admin.Config{
    Renderer: renderer,
    // Other configuration options
}

panel, err := admin.NewPanel(ormIntegrator, webIntegrator, permissionFunc, config)
if err != nil {
    log.Fatal(err)
}
```

### Registering Templates and Assets

The renderer allows you to register templates and static assets.

**Register Default Templates and Assets:**

```go
renderer.RegisterDefaultTemplates(templatesFS, "templates/")
renderer.RegisterDefaultAssets(assetsFS, "assets/")
```

**Add Custom Template:**

```go
err := renderer.AddCustomTemplate("custom_template.html", customTemplateContent)
if err != nil {
    log.Fatal(err)
}
```

**Add Custom Asset:**

```go
renderer.AddCustomAsset("custom_style.css", customStyleContent)
```

---

## Building a Custom Template Renderer

If you need more control over how templates are rendered, you can build your own template renderer.

### Implementing the Required Interface

Your custom renderer must implement the `TemplateRenderer` interface defined by the admin panel.

**TemplateRenderer Interface:**

```go
type TemplateRenderer interface {
    RenderTemplate(name string, data map[string]interface{}) (string, error)
    RegisterDefaultTemplates(templates embed.FS, prefix string)
    RegisterCompositeDefaultTemplate(name string, baseNames ...string) error
    RegisterDefaultData(data map[string]interface{}) error
    AddCustomTemplate(name string, tmplText string) error
    AddCustomCompositeTemplate(name string, baseNames ...string) error
    RegisterDefaultAssets(assets embed.FS, prefix string)
    AddCustomAsset(name string, asset []byte)
    GetAsset(name string) ([]byte, error)
    RegisterLinkFunc(func(string) string)
    RegisterAssetsFunc(func(string) string)
}
```

**Steps to Implement:**

1. **Create a Struct**: Define a struct for your renderer.
2. **Implement Methods**: Implement all methods of the `TemplateRenderer` interface.
3. **Handle Template Parsing and Rendering**: Use your preferred templating engine.

**Example:**

```go
type MyCustomRenderer struct {
    // Your fields here
}

func (r *MyCustomRenderer) RenderTemplate(
    name string, data map[string]interface{}, 
) (string, error) {
    // Your rendering logic here
}
```

### Integrating the Custom Renderer

Once your custom renderer is implemented, integrate it with the admin panel:

```go
renderer := &MyCustomRenderer{
    // Initialize your renderer
}

config := &admin.Config{
    Renderer: renderer,
    // Other configuration options
}

panel, err := admin.NewPanel(ormIntegrator, webIntegrator, permissionFunc, config)
if err != nil {
    log.Fatal(err)
}
```

---

## Contributing Your Renderer

If you've built a custom template renderer that you believe could benefit others, we encourage you to contribute it to 
the community.

- **Submit a Pull Request**: Fork the [admin repository](https://github.com/go-advanced-admin/admin), add your 
renderer, and submit a pull request.
- **Follow Contribution Guidelines**: Ensure your code adheres to the project's 
[code style guidelines](Contributing.md).
- **Provide Documentation**: Include usage instructions and examples.

By contributing, you help make **go-advanced-admin** more versatile and valuable for everyone.

---

## Further Reading

- **[](Templates.md)**: Detailed guide on customizing templates.
- **[](Configuration.md)**: Comprehensive guide on configuration options.
- **[Contribution Guide](Contributing.md)**: How to contribute to the project.
- **[Issue Tracker](https://github.com/go-advanced-admin/admin/issues)**: Report bugs or request features.

---

**Next Steps:**

- Customize your templates by following the [](Templates.md) guide.
- Explore advanced customization in the [](Advanced-Topics.md).
- Learn how to create custom forms in the [](Forms.md) section.

---

**Feedback**

We value your feedback. If you have suggestions or encounter issues, please 
[open an issue](https://github.com/go-advanced-admin/admin/issues/new/choose) or submit a pull request.

---

**Thank you for using go-advanced-admin!**


> This section of the documentation was written with the help of [Infuzu AI](https://infuzu.com)'s tools.
{style="note"}
