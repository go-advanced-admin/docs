# Web

Welcome to the **Web Framework Integrations** section. This page provides detailed guides on integrating the Advanced 
Admin Panel with various Go web frameworks. Whether you're using a popular framework like Gin or Echo, or a custom 
framework, this section will help you seamlessly integrate the admin panel into your project.

If you're new to the admin panel and want a quick setup, please refer to our [Quick Start Guides](Quick-Start.md) for 
step-by-step instructions.

---

## Supported Web Frameworks

We currently support the following Go web frameworks:

- [Gin](#gin) (coming soon)
- [Echo](#echo)
- [Chi](#chi) (coming soon)
- [Fiber](#fiber) (coming soon)

Each integration guide includes:

- Configuration steps
- Routing setup
- Middleware integration
- Template rendering
- Handling HTTP requests and responses

---

## Gin
(coming soon)

[**Gin**](https://github.com/gin-gonic/gin) is a high-performance HTTP web framework. Our Gin integration guide covers:

- Setting up the admin panel with Gin.
- Configuring routes and middleware.
- Integrating the admin panel's template renderer.
- Serving static files.

**Guide:** [Gin Integration Guide](Gin.md)

---

## Echo

[**Echo**](https://github.com/labstack/echo) is a fast and minimalist web framework. The Echo integration guide 
includes:

- Initializing the admin panel with Echo.
- Setting up routing and middleware.
- Managing contexts and request handling.
- Integrating templates and static assets.

**Guide:** [Echo Integration Guide](Echo.md)

---

## Chi
(coming soon)

[**Chi**](https://github.com/go-chi/chi) is a lightweight and idiomatic router for building HTTP services. In the Chi 
integration guide, you'll learn:

- How to set up the admin panel with Chi.
- Defining routes and subrouters.
- Middleware configuration.
- Template rendering and static file serving.

**Guide:** [Chi Integration Guide](Chi.md)

---

## Fiber
(coming soon)

[**Fiber**](https://github.com/gofiber/fiber) is an Express-inspired web framework written in Go. The Fiber integration 
guide covers:

- Integrating the admin panel with Fiber.
- Configuring routes and middleware.
- Handling requests and responses.
- Serving templates and static files.

**Guide:** [Fiber Integration Guide](Fiber.md)

## Building a Custom Web Framework Integration

If your preferred web framework is not listed above, you can build a custom integration. Our comprehensive guide walks 
you through:

- Implementing the required interface.
- Handling HTTP requests and responses.
- Managing routing and middleware.
- Serving templates and static files.
- Registering your custom framework with the admin panel.

**Guide:** [](Building-a-Custom-Web-Framework-Integration.md)

### Topics Covered:

- **Implementing the `WebIntegrator` Interface:** Learn how to conform to the admin panel's expectations for web 
frameworks.
- **Handling Requests and Responses:** Properly manage HTTP methods, URL parameters, and form data.
- **Routing and Middleware:** Set up routes and incorporate middleware for authentication, logging, etc.
- **Template Rendering:** Integrate your framework's template engine with the admin panel's templates.
- **Static File Serving:** Serve CSS, JavaScript, and image files required by the admin panel.

---

## Contribute and Request Features

We are continuously working to expand our support for various web frameworks. If your preferred framework is not 
currently supported, we encourage you to:

- **Submit a Feature Request:** Visit our [Contributions](Contributing.md) page to request support for a new framework 
or [submit an issue on GitHub](https://github.com/go-advanced-admin/admin/issues/new?assignees=&labels=&projects=&template=feature_request.md&title=).
- **Contribute an Integration:** If you're interested in contributing, you can develop a new integration and share it 
with the community.
- **Collaborate:** Join our community discussions to collaborate on enhancing existing integrations or creating new 
ones.

Your contributions are invaluable and help us improve the admin panel for everyone in the Go community.

---

## Need a Quick Start?

If you're eager to get started and want a step-by-step guide for a specific web framework, check out our 
[Quick Start Guides](Quick-Start.md):

- [Gin Quick Start](Gin-Quick-Start.md) (coming soon)
- [Echo Quick Start](Echo-Quick-Start.md)
- [Chi Quick Start](Chi-Quick-Start.md) (coming soon)
- [Fiber Quick Start](Fiber-Quick-Start.md) (coming soon)

These guides will help you set up the admin panel in your project quickly and efficiently.

---

## Support and Feedback

If you have any questions, need assistance, or want to provide feedback, please don't hesitate to reach out through our 
[support channels](https://github.com/go-advanced-admin/admin/issues/new?assignees=&labels=&projects=&template=bug_report.md&title=). 
We're here to help you make the most of the Advanced Admin Panel.

---

Feel free to explore the guides linked above to integrate the admin panel with your web framework of choice. We look forward to seeing how you utilize the admin panel in your projects!
