# Building a Custom ORM Integration

In this guide, we'll walk you through the process of building a custom ORM integrator for the Go Advanced Admin panel. 
If your project uses an ORM that is not currently supported, you can create an integrator to make it compatible with 
the admin panel.

By the end of this guide, you'll learn how to:

- Implement the required interface for ORM integration.
- Handle database interactions specific to your ORM.
- Register your custom ORM with the admin panel.

**Note:** If you build a custom integrator, we encourage you to [contribute](#contributing-your-integrator) it to the community!

---

## Table of Contents

- [Understanding the ORM Integrator Interface](#understanding-the-orm-integrator-interface)
- [Implementing the Interface](#implementing-the-interface)
    - [1. Create the Integrator Struct](#1-create-the-integrator-struct)
    - [2. Implement Required Methods](#2-implement-required-methods)
- [Handling Database Interactions](#handling-database-interactions)
- [Registering the Custom ORM](#registering-the-custom-orm)
- [Contributing Your Integrator](#contributing-your-integrator)

---

## Understanding the ORM Integrator Interface

The admin panel communicates with your ORM through the `ORMIntegrator` interface. This interface defines the methods 
necessary for CRUD operations and data retrieval.

Here's the `ORMIntegrator` interface:

```go
type ORMIntegrator interface {
    GetPrimaryKeyValue(model interface{}) (interface{}, error)
    GetPrimaryKeyType(model interface{}) (reflect.Type, error)
    FetchInstances(model interface{}) (interface{}, error)
    FetchInstancesOnlyFields(
        model interface{}, fields []string, 
    ) (interface{}, error)
    FetchInstancesOnlyFieldWithSearch(
        model interface{}, 
        fields []string, 
        query string, 
        searchFields []string, 
    ) (interface{}, error)
    DeleteInstance(model interface{}, id interface{}) error
    FetchInstanceOnlyFields(
        model interface{}, id interface{}, fields []string, 
    ) (interface{}, error)
    FetchInstance(
        model interface{}, id interface{}, 
    ) (interface{}, error)
    CreateInstance(instance interface{}) error
    UpdateInstance(instance interface{}, primaryKey interface{}) error
    CreateInstanceOnlyFields(
        instance interface{}, fields []string, 
    ) error
    UpdateInstanceOnlyFields(
        instance interface{}, fields []string, primaryKey interface{}, 
    ) error
}
```

### Method Overview

- **GetPrimaryKeyValue**: Retrieve the primary key value from a model instance.
- **GetPrimaryKeyType**: Get the type of the primary key field.
- **FetchInstances**: Retrieve all instances of a model.
- **FetchInstancesOnlyFields**: Retrieve instances with selected fields.
- **FetchInstancesOnlyFieldWithSearch**: Retrieve instances based on a search query.
- **DeleteInstance**: Delete a model instance by ID.
- **FetchInstanceOnlyFields**: Retrieve a single instance with selected fields.
- **FetchInstance**: Retrieve a single instance by ID.
- **CreateInstance**: Create a new instance in the database.
- **UpdateInstance**: Update an existing instance.
- **CreateInstanceOnlyFields**: Create a new instance with selected fields.
- **UpdateInstanceOnlyFields**: Update an instance with selected fields.

---

## Implementing the Interface

To build your custom integrator, you'll need to implement all the methods defined in the `ORMIntegrator` interface.

### 1. Create the Integrator Struct

Start by creating a new package for your ORM integrator. Define a struct that holds your ORM's database connection.

```go
package admincustomorm

import (
    "reflect"
    // Import your ORM's package
    "github.com/yourusername/customorm"
)

// Integrator implements the ORMIntegrator interface using CustomORM.
type Integrator struct {
    // DB is the CustomORM database connection used by the integrator.
    DB *customorm.DB
}

// NewIntegrator creates a new Integrator with the provided CustomORM database connection.
func NewIntegrator(db *customorm.DB) *Integrator {
    return &Integrator{DB: db}
}
```

### 2. Implement Required Methods

Implement each method of the `ORMIntegrator` interface. Below are examples using a hypothetical `CustomORM`. Replace 
`CustomORM` methods with those of your ORM.

#### GetPrimaryKeyValue

Retrieve the primary key value from a model instance.

```go
func (i *Integrator) GetPrimaryKeyValue(model interface{}) (interface{}, error) {
    // Use reflection to access the primary key field
    modelValue := reflect.ValueOf(model)
    if modelValue.Kind() == reflect.Ptr {
        modelValue = modelValue.Elem()
    }

    primaryKeyField := modelValue.FieldByName("ID") // Replace "ID" with your primary key field name
    if !primaryKeyField.IsValid() {
        return nil, fmt.Errorf("primary key field not found")
    }

    return primaryKeyField.Interface(), nil
}
```

#### GetPrimaryKeyType

Get the type of the primary key field.

```go
func (i *Integrator) GetPrimaryKeyType(model interface{}) (reflect.Type, error) {
    modelType := reflect.TypeOf(model)
    if modelType.Kind() == reflect.Ptr {
        modelType = modelType.Elem()
    }

    primaryKeyField, found := modelType.FieldByName("ID") // Replace "ID" with your primary key field name
    if !found {
        return nil, fmt.Errorf("primary key field not found")
    }

    return primaryKeyField.Type, nil
}
```

#### FetchInstances

Retrieve all instances of a model.

```go
func (i *Integrator) FetchInstances(model interface{}) (interface{}, error) {
    modelSlice := reflect.New(
        reflect.SliceOf(reflect.TypeOf(model).Elem()), 
    ).Interface()
    err := i.DB.FindAll(modelSlice)
    if err != nil {
        return nil, err
    }
    return modelSlice, nil
}
```

#### FetchInstancesOnlyFields

Retrieve instances with selected fields.

```go
func (i *Integrator) FetchInstancesOnlyFields(
    model interface{}, fields []string, 
) (interface{}, error) {
    modelSlice := reflect.New(
        reflect.SliceOf(reflect.TypeOf(model).Elem()), 
    ).Interface()
    err := i.DB.Select(fields).FindAll(modelSlice)
    if err != nil {
        return nil, err
    }
    return modelSlice, nil
}
```

#### FetchInstancesOnlyFieldWithSearch

Retrieve instances based on a search query.

```go
func (i *Integrator) FetchInstancesOnlyFieldWithSearch(
    model interface{}, 
    fields []string, 
    query string, 
    searchFields []string, 
) (interface{}, error) {
    modelSlice := reflect.New(
        reflect.SliceOf(reflect.TypeOf(model).Elem()), 
    ).Interface()
    // Build search conditions based on your ORM's syntax
    searchCondition := i.buildSearchCondition(query, searchFields)
    err := i.DB.Select(fields).Where(searchCondition).FindAll(modelSlice)
    if err != nil {
        return nil, err
    }
    return modelSlice, nil
}
```

**Helper Function for Search Conditions:**

```go
func (i *Integrator) buildSearchCondition(
    query string, fields []string, 
) string {
    var conditions []string
    for _, field := range fields {
        conditions = append(
            conditions, fmt.Sprintf("%s LIKE '%s'", field, query), 
        )
    }
    return strings.Join(conditions, " OR ")
}
```

#### DeleteInstance

Delete a model instance by ID.

```go
func (i *Integrator) DeleteInstance(
    model interface{}, id interface{}, 
) error {
    err := i.DB.Delete(model, id)
    if err != nil {
        return err
    }
    return nil
}
```

#### FetchInstanceOnlyFields

Retrieve a single instance with selected fields.

```go
func (i *Integrator) FetchInstanceOnlyFields(
    model interface{}, id interface{}, fields []string, 
) (interface{}, error) {
    instance := reflect.New(reflect.TypeOf(model).Elem()).Interface()
    err := i.DB.Select(fields).Find(instance, id)
    if err != nil {
        return nil, err
    }
    return instance, nil
}
```

#### FetchInstance

Retrieve a single instance by ID.

```go
func (i *Integrator) FetchInstance(
    model interface{}, id interface{}, 
) (interface{}, error) {
    instance := reflect.New(reflect.TypeOf(model).Elem()).Interface()
    err := i.DB.Find(instance, id)
    if err != nil {
        return nil, err
    }
    return instance, nil
}
```

#### CreateInstance

Create a new instance in the database.

```go
func (i *Integrator) CreateInstance(instance interface{}) error {
    err := i.DB.Create(instance)
    if err != nil {
        return err
    }
    return nil
}
```

#### UpdateInstance

Update an existing instance.

```go
func (i *Integrator) UpdateInstance(
    instance interface{}, primaryKey interface{}, 
) error {
    err := i.DB.Update(instance, primaryKey)
    if err != nil {
        return err
    }
    return nil
}
```

#### CreateInstanceOnlyFields

Create a new instance with selected fields.

```go
func (i *Integrator) CreateInstanceOnlyFields(
    instance interface{}, fields []string,
) error {
    err := i.DB.Select(fields).Create(instance)
    if err != nil {
        return err
    }
    return nil
}
```

#### UpdateInstanceOnlyFields

Update an instance with selected fields.

```go
func (i *Integrator) UpdateInstanceOnlyFields(
    instance interface{}, fields []string, primaryKey interface{}, 
) error {
    err := i.DB.Select(fields).Update(instance, primaryKey)
    if err != nil {
        return err
    }
    return nil
}
```

---

## Handling Database Interactions

When implementing the methods, ensure that you:

- **Use Reflection Carefully**: Since models can be pointers or structs, handle both cases.
- **Manage Errors**: Return clear and informative errors for better debugging.
- **Optimize Queries**: Use your ORM's features to optimize data retrieval, especially when selecting specific fields or searching.
- **Test Thoroughly**: Write tests to verify that each method works as expected with your ORM.

---

## Registering the Custom ORM

After implementing your integrator, you need to register it with the admin panel.

```go
package main

import (
    "log"

    "github.com/go-advanced-admin/admin"
    "github.com/go-advanced-admin/web-echo"
    "github.com/labstack/echo/v4"
    "github.com/yourusername/admincustomorm"
    // Import your ORM package
    "github.com/yourusername/customorm"
)

func main() {
    // Initialize your ORM's database connection
    db, err := customorm.Open("your_connection_string")
    if err != nil {
        log.Fatalf("Failed to connect to the database: %v", err)
    }

    // Create the ORM integrator
    ormIntegrator := admincustomorm.NewIntegrator(db)

    // Initialize the web framework integrator (e.g., Echo)
    e := echo.New()
    webIntegrator := adminecho.NewIntegrator(e.Group(""))

    // Define the permission function
    permissionFunc := func(
        req admin.PermissionRequest, ctx interface{}, 
    ) (bool, error) {
        // Implement your permission logic here
        return true, nil
    }

    // Create the admin panel
    panel, err := admin.NewPanel(
        ormIntegrator, webIntegrator, permissionFunc, nil, 
    )
    if err != nil {
        log.Fatal(err)
    }

    // Register an app and models
    app, err := panel.RegisterApp("MyApp", "My Application", nil)
    if err != nil {
        log.Fatal(err)
    }

    // Register models with the app
    _, err = app.RegisterModel(&MyModel{}, nil)
    if err != nil {
        log.Fatal(err)
    }

    // Start the web server
    e.Logger.Fatal(e.Start(":8080"))
}
```

---

## Contributing Your Integrator

We encourage you to contribute your custom ORM integrator to the community! By sharing your integrator, you help others 
who might be using the same ORM.

For more details, please refer to our [Contribution Guide](Contributing.md).

---

**Thank you for contributing to the Go Advanced Admin community!**

If you have any questions or need assistance, feel free to 
[open an issue](https://github.com/go-advanced-admin/admin/issues) or join our community discussions.

---

> This guide was generated to help developers integrate their custom ORMs with the Go Advanced Admin panel. 
> For any discrepancies or issues, 
> [please reach out to the maintainers](https://github.com/go-advanced-admin/admin/issues).