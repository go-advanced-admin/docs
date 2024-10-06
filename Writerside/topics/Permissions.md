# Permissions

The admin panel uses a permission function to control access to various actions.

## Building a Permission Checker Function
Implement a function matching the `PermissionFunc` type:
```go
type PermissionFunc func(PermissionRequest, interface{}) (bool, error)
```

## Example with role based access:
```go
permissionFunc := func(req admin.PermissionRequest, ctx interface{}) (bool, error) {
    user, ok := ctx.(*User)
    if !ok {
        return false, nil
    }

    if user.Role == "admin" {
        return true, nil
    }

    // Additional permission checks
    return false, nil
}
```

## Examples of Different Permission Schemes
- **Role-Based Access Control (RBAC)**: Permissions are assigned based on user roles.
- **Attribute-Based Access Control (ABAC)**: Permissions are granted based on user attributes and resource properties.

## Integration with Authentication Systems
You can integrate the permission function with your authentication system by passing the user context from your web framework.