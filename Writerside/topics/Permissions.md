# Permissions

The permissions system in **go-advanced-admin** is a crucial component that controls access to various parts of the 
admin panel. It allows you to define who can view, create, update, or delete resources within your application.

**Important:** If you do not set up the permissions correctly, users may find the admin panel empty or lacking content. 
It's essential to implement the permission checker function to ensure that the appropriate content is displayed to the 
right users.

This guide covers:

- [Understanding the Permission Checker Function](#understanding-the-permission-checker-function)
- [Building a Custom Permission Checker](#building-a-custom-permission-checker)
- [Permission Schemes](#permission-schemes)
    - [Role-Based Permissions](#role-based-permissions)
    - [Attribute-Based Permissions](#attribute-based-permissions)
- [Integration with Authentication Systems](#integration-with-authentication-systems)
- [Common Pitfalls](#common-pitfalls)
- [Troubleshooting Empty Panels](#troubleshooting-empty-panels)
- [Next Steps](#next-steps)

---

## Understanding the Permission Checker Function

At the core of the permissions system is the **Permission Checker Function**. This function determines whether a user 
has the necessary permissions to perform specific actions within the admin panel.

### Function Signature

```go
type PermissionFunc func(PermissionRequest, interface{}) (bool, error)
```

- **PermissionRequest**: Contains details about the requested action.
- **interface{}**: The context, typically representing the current request.
- **Returns**: A boolean indicating whether the action is allowed, and an error if applicable.

### PermissionRequest Structure

```go
type PermissionRequest struct {
    AppName    *string
    ModelName  *string
    InstanceID interface{}
    Action     *Action
}
```

- **AppName**: The name of the application.
- **ModelName**: The name of the model.
- **InstanceID**: The ID of the specific instance (if applicable).
- **Action**: The action being requested (e.g., read, create, update, delete).

### Actions

The actions are defined as constants:

```go
const (
    ReadAction    Action = "read"
    CreateAction  Action = "create"
    UpdateAction  Action = "update"
    DeleteAction  Action = "delete"
    LogViewAction Action = "log_view"
)
```

---

## Building a Custom Permission Checker

To effectively use the admin panel, you need to implement the `PermissionFunc`. Here's how to build a custom permission 
checker that aligns with your application's requirements.

### Step-by-Step Guide

1. **Define the Permission Checker Function**

    Create a function that matches the `PermissionFunc` signature.

    ```go
    func MyPermissionChecker(
        req admin.PermissionRequest, ctx interface{}, 
    ) (bool, error) {
        // Your permission logic goes here
    }
    ```

2. **Access User Information**

    Extract the current user or request context from the `ctx` parameter. This often involves type asserting `ctx` to 
    your specific context type.

    ```go
    user, ok := ctx.(*User)
    if !ok {
        return false, errors.New("invalid context")
    }
    ```

3. **Implement Permission Logic**

    Based on the `PermissionRequest`, determine if the user has the necessary permissions.

    ```go
    switch *req.Action {
    case admin.ReadAction:
        // Logic for read permissions
    case admin.CreateAction:
        // Logic for create permissions
    case admin.UpdateAction:
        // Logic for update permissions
    case admin.DeleteAction:
        // Logic for delete permissions
    default:
        return false, nil
    }
    ```

4. **Return the Permission Decision**

    Return `true` if the action is allowed, `false` otherwise. Include an error if applicable.

    ```go
    if user.HasPermission(*req.Action, req.AppName, req.ModelName) {
        return true, nil
    }
    return false, nil
    ```

5. **Handle Errors Appropriately**

    If an error occurs during permission checking, return `false` and the error.

    ```go
    if err != nil {
        return false, err
    }
    ```

6. **Register the Permission Checker**

   When initializing the admin panel, provide your custom permission checker function.

   ```go
   panel, err := admin.NewPanel(ormIntegrator, webIntegrator, MyPermissionChecker, &admin.Config{
       // Your configuration options
   })
   if err != nil {
       log.Fatalf("Failed to create admin panel: %v", err)
   }
   ```

**Note:** Without a proper permission checker function, the admin panel will not display any content to users, as all 
actions will be denied by default.

---

## Permission Schemes

Implementing an effective permission scheme is crucial for both security and usability. Here are two common approaches:

### Role-Based Permissions

Role-based permissions assign access rights to users based on their roles (e.g., admin, editor, viewer).

**Example:**

```go
func RoleBasedPermissionChecker(
    req admin.PermissionRequest, ctx interface{}, 
) (bool, error) {
    user, ok := ctx.(*User)
    if !ok {
        return false, errors.New("invalid context")
    }

    switch user.Role {
    case "admin":
        // Admins have all permissions
        return true, nil
    case "editor":
        // Editors can read and update
        if *req.Action == admin.ReadAction || *req.Action == admin.UpdateAction {
            return true, nil
        }
    case "viewer":
        // Viewers can only read
        if *req.Action == admin.ReadAction {
            return true, nil
        }
    }

    // Deny all other actions
    return false, nil
}
```

### Attribute-Based Permissions

Attribute-based permissions consider specific attributes of the user or the resource to determine access rights.

**Example:**

```go
func AttributeBasedPermissionChecker(
    req admin.PermissionRequest, ctx interface{}, 
) (bool, error) {
    user, ok := ctx.(*User)
    if !ok {
        return false, errors.New("invalid context")
    }

    // Allow users to read their own data
    if *req.Action == admin.ReadAction && req.InstanceID == user.ID {
        return true, nil
    }

    // Additional attribute-based logic
    // ...

    // Deny by default
    return false, nil
}
```

---

## Integration with Authentication Systems

To make effective permission decisions, your permission checker needs to access user information from your 
authentication system.

### Steps to Integrate

1. **Pass User Information via Context**

   Ensure that the `ctx` parameter provided to the permission checker includes the authenticated user.

2. **Modify Your Web Integrator**

   Adjust your web integrator to include user information in the context passed to the admin panel handlers.

   **Example with Gin:**

   ```go
   func MyGinWebIntegrator() admin.WebIntegrator {
       return &GinWebIntegrator{
           // Implementation details
       }
   }

   // Within your Gin handlers, set the user in the context
   func (gi *GinWebIntegrator) HandleRoute(
       method, path string, handler admin.HandlerFunc, 
   ) {
       gi.router.Handle(method, path, func(c *gin.Context) {
           user, err := AuthenticateUser(c)
           if err != nil {
               c.AbortWithStatus(http.StatusUnauthorized)
               return
           }
           // Pass the user to the handler
           statusCode, body := handler(user)
           c.String(int(statusCode), body)
       })
   }
   ```

---

## Common Pitfalls

### Empty Admin Panel

- **Symptom**: When accessing the admin panel, all pages appear empty.
- **Cause**: The permission checker function is denying all actions, or not properly implemented.
- **Solution**: Ensure your permission checker returns `true` for the actions and resources the user should have access 
to.

### Incorrect Context Type

- **Symptom**: Errors indicating invalid context or type assertion failures.
- **Cause**: The `ctx` parameter does not contain the expected user or request information.
- **Solution**: Verify that you're correctly passing the context with the necessary user information to the permission 
checker.

### Unhandled Actions

- **Symptom**: Users cannot perform certain actions despite having the correct permissions.
- **Cause**: The permission checker does not handle all possible actions.
- **Solution**: Ensure your permission checker includes cases for all relevant actions (`ReadAction`, `CreateAction`, 
`UpdateAction`, `DeleteAction`, etc.).

---

## Troubleshooting Empty Panels

If the admin panel appears empty or certain sections are not visible, follow these steps:

1. **Review Your Permission Checker**

   Confirm that your permission checker function correctly grants permissions for the intended actions and resources.

2. **Check User Roles and Permissions**

   Ensure that the user has the appropriate roles or attributes assigned.

3. **Inspect Logs**

   Look for any error messages or warnings in your application's logs that might indicate permission issues.

4. **Test with Different Users**

   Try accessing the admin panel with users of different roles to determine if the issue is user-specific.

5. **Validate Context Passing**

   Make sure that the user information is correctly passed through the context to the permission checker.

---

## Next Steps

- **Implement and Test Your Permission Checker**

  Ensure that your permission logic aligns with your application's security requirements.

- **Explore Advanced Permission Schemes**

  Consider combining role-based and attribute-based permissions for more granular control.

- **Proceed to Configuration**

  Continue to the [](Configuration.md) guide to further customize your admin panel.

---

## Feedback and Support

If you encounter any issues or have questions regarding permissions, please:

- **Open an Issue**: [GitHub Issues](https://github.com/go-advanced-admin/admin/issues)
- **Contribute**: Refer to our [Contribution Guide](Contributing.md) if you'd like to contribute improvements.

---

**Note:** Properly setting up the permissions system is essential for the functionality of the admin panel. Without it, 
users may not see any content when accessing the main page or specific apps and models.

---

*Return to [](Core-Features.md) or explore other topics in the documentation.*

---

Thank you for using **go-advanced-admin**!