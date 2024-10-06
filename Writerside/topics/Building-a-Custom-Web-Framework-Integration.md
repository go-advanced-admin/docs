# Building a Custom Web Framework Integration

Implement the `WebIntegrator` interface:
```go
package adminpanel

// HandlerFunc represents a handler function used in the admin panel routes.
type HandlerFunc = func(interface{}) (uint, string)

// WebIntegrator defines the interface for integrating web frameworks with the admin panel.
type WebIntegrator interface {
	// HandleRoute registers a route with the given method, path, and handler function.
	HandleRoute(method, path string, handler HandlerFunc)

	// ServeAssets serves static assets under the specified prefix using the provided renderer.
	ServeAssets(prefix string, renderer TemplateRenderer)

	// GetQueryParam retrieves the value of a query parameter from the context.
	GetQueryParam(ctx interface{}, name string) string

	// GetPathParam retrieves the value of a path parameter from the context.
	GetPathParam(ctx interface{}, name string) string

	// GetRequestMethod retrieves the HTTP method of the request from the context.
	GetRequestMethod(ctx interface{}) string

	// GetFormData retrieves form data from the context.
	GetFormData(ctx interface{}) map[string][]string
}
```

## Implementing the Required Interface
Create a struct that satisfied the `WebIntegrator` interface, implementing all required methods.

## Handling HTTP Requests and Responses
Use your web framework's mechanisms to handle routing, middleware, and request context.

## Registering the Custom Framework
Pass your custom web integrator when initializing the admin panel:
```go
panel, err := admin.NewPanel(ormIntegrator, myCustomWebIntegrator, permissionFunc, nil)
```

