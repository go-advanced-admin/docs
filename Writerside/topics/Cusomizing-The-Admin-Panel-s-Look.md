# Customizing the Admin Panel's Look

The Go Advanced Admin Panel is designed to be highly customizable, allowing you to tailor its appearance to match your 
application's branding and user experience requirements.

## Table of Contents

- [Introduction](#introduction)
- [Overriding Default Templates](#overriding-default-templates)
    - [Steps to Override Templates](#steps-to-override-templates)
    - [Template Hierarchy](#template-hierarchy)
- [Using Custom CSS and JavaScript](#using-custom-css-and-javascript)
    - [Steps to Include Custom Assets](#steps-to-include-custom-assets)
- [Creating Custom Themes](#creating-custom-themes)
    - [Steps to Create a Custom Theme](#steps-to-create-a-custom-theme)
- [Example: Applying a Custom Theme](#example-applying-a-custom-theme)
- [Additional Resources](#additional-resources)

---

## Introduction

Customizing the look and feel of your admin panel can enhance usability and provide a consistent experience for your 
users. This guide will walk you through the steps to customize the admin panel's appearance.

## Overriding Default Templates

The admin panel uses Go's `html/template` package for rendering HTML templates. You can override the default templates 
provided by the admin panel to customize the UI components.

### Steps to Override Templates

1. **Create Custom Templates**: Define your custom templates by creating `.html` files with your desired HTML structure 
and styling.

2. **Register Custom Templates**: Use the `TemplateRenderer` interface to register your custom templates.

   ```go
   renderer := admin.NewDefaultTemplateRenderer()

   // Add your custom template
   err := renderer.AddCustomTemplate("page.html", yourCustomTemplateContent)
   if err != nil {
       log.Fatal(err)
   }

   // Set the renderer in the admin config
   config := &admin.Config{
       Renderer: renderer,
   }
   ```

3. **Specify the Custom Renderer**: When initializing the admin panel, provide your custom renderer in the 
configuration.

   ```go
   panel, err := admin.NewPanel(ormIntegrator, webIntegrator, permissionFunc, config)
   if err != nil {
       log.Fatal(err)
   }
   ```

### Template Hierarchy

Understanding the template hierarchy is crucial for effective customization:

- **Base Templates**: Templates that contain common elements like headers, footers, and navigation menus.
- **Page Templates**: Templates that extend base templates for specific pages (e.g., dashboard, models).

You can create composite templates by combining multiple templates to build complex layouts.

## Using Custom CSS and JavaScript

Incorporate custom styles and scripts to further modify the appearance and behavior of your admin panel.

### Steps to Include Custom Assets

1. **Register Custom Assets**: Use the `TemplateRenderer` to register your custom CSS and JavaScript files.

   ```go
   // Add your custom CSS
   renderer.AddCustomAsset("custom.css", customCSSContent)

   // Add your custom JavaScript
   renderer.AddCustomAsset("custom.js", customJSContent)
   ```

2. **Reference Assets in Templates**: Modify your templates to include the custom assets using the `assetPath` function.

   ```html
   <link rel="stylesheet" href="{{ assetPath "custom.css" }}">
   <script src="{{ assetPath "custom.js" }}"></script>
   ```

## Creating Custom Themes

For a more comprehensive customization, you can create custom themes that encompass a consistent design language 
throughout the admin panel.

### Steps to Create a Custom Theme

1. **Design the Theme**: Define the color schemes, typography, and layouts that align with your application's branding.

2. **Implement Theme Styles**: Write CSS files to implement your design choices.

3. **Override Templates**: Modify or create new templates to include any additional HTML structures required by your 
theme.

4. **Bundle the Theme**: Organize your templates and assets into a cohesive package for easy integration and 
maintenance.

## Example: Applying a Custom Theme

Suppose you want to apply a dark theme to the admin panel.

1. **Create a CSS Stylesheet**: Write a `dark-theme.css` file with your dark mode styles.

2. **Register the Asset**:

   ```go
   renderer.AddCustomAsset("dark-theme.css", darkThemeCSSContent)
   ```

3. **Modify Templates**: Ensure your templates include the stylesheet.

   ```html
   <link rel="stylesheet" href="{{ assetPath "dark-theme.css" }}">
   ```

4. **Update Admin Config**: Use the custom renderer with the registered assets when initializing the admin panel.

   ```go
   config := &admin.Config{
       Renderer: renderer,
   }
   ```

## Additional Resources

- [TemplateRenderer Interface Documentation](Template-Rendering.md)
- [Go `html/template` Package Documentation](https://golang.org/pkg/html/template/)


> This section of the documentation was written with the help of [Infuzu AI](https://infuzu.com)'s tools.
{style="note"}
