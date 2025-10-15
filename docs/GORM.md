# GORM

## Introduction

This guide provides a comprehensive overview of how to use and customize the GORM integration with the Go Advanced 
Admin Panel. The GORM integration allows you to seamlessly connect your admin panel to a GORM-managed database, 
enabling you to perform CRUD operations and manage your data models efficiently.

By the end of this guide, you will have a deep understanding of how the GORM integration works, how to register your 
models, and how to customize the integration to suit your application's needs.

## Prerequisites

- **Go Programming Language**: Basic knowledge of Go.
- **GORM ORM Library**: Familiarity with GORM ([Official Documentation](https://gorm.io/)).
- **Admin Panel Setup**: A working admin panel setup (refer to the [Quick Start Guide](Quick-Start.md) if needed).

## Configuration

### Installing GORM and the GORM Integrator

First, ensure you have GORM installed:

```bash
go get -u gorm.io/gorm
go get -u gorm.io/driver/sqlite # or the driver of your choice
```

Then, install the GORM integrator package:

```bash
go get -u github.com/go-advanced-admin/orm-gorm
```

### Setting Up the Database Connection

Set up your GORM database connection as you normally would. For example, using SQLite:

```go
import (
    "gorm.io/driver/sqlite"
    "gorm.io/gorm"
)

func main() {
    db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
    if err != nil {
        panic("failed to connect database")
    }
    // Rest of your code
}
```

### Initializing the GORM Integrator

Create a new instance of the GORM integrator and pass your GORM database connection to it:

```go
import (
    "github.com/go-advanced-admin/admin"
    "github.com/go-advanced-admin/orm-gorm"
)

// ...

ormIntegrator := admingorm.NewIntegrator(db)
```

### Integrating with the Admin Panel

When creating your admin panel, pass the GORM integrator to it:

```go
panel, err := admin.NewPanel(
    ormIntegrator, webIntegrator, permissionFunc, nil,
)
if err != nil {
    log.Fatal(err)
}
```

## Model Registration

### Defining Your Models

Define your GORM models as you normally would:

```go
type Post struct {
    ID      uint   `gorm:"primarykey"`
    Title   string
    Content string
}

type User struct {
    ID       uint   `gorm:"primarykey"`
    Username string
    Email    string
}
```

### Migrating Your Models

Ensure your models are migrated to the database:

```go
err = db.AutoMigrate(&Post{}, &User{})
if err != nil {
    log.Fatal(err)
}
```

### Registering Models with the Admin Panel

Register your models with the admin panel using the `RegisterModel` method:

```go
app, err := panel.RegisterApp("Blog", "Blog Management", nil)
if err != nil {
    log.Fatal(err)
}

_, err = app.RegisterModel(&Post{}, nil)
if err != nil {
    log.Fatal(err)
}

_, err = app.RegisterModel(&User{}, nil)
if err != nil {
    log.Fatal(err)
}
```

## Querying and Filtering Data

The GORM integrator handles data fetching and querying for you. When you use the admin panel, it utilizes the 
integrator's methods to perform CRUD operations.

### Searching and Filtering

The integrator supports searching and filtering through its `FetchInstancesOnlyFieldWithSearch` method. This method 
constructs SQL queries using GORM's API to search across specified fields.

### Pagination

Pagination is handled by the admin panel, which uses the integrator to fetch a subset of data based on the current page 
and items per page.

## Understanding the GORM Integrator

The GORM integrator implements the `ORMIntegrator` interface required by the admin panel. Here's an in-depth look at 
how it works.

### Overview

The integrator uses GORM's API to interact with the database. It provides implementations for all required methods, 
allowing the admin panel to perform CRUD operations without worrying about the underlying database logic.

### Key Methods

#### `GetPrimaryKeyValue`

Retrieves the primary key value from a model instance.

```go
func (i *Integrator) GetPrimaryKeyValue(model interface{}) (interface{}, error)
```

- **How It Works**:
    - Uses reflection to access the primary key field.
    - Parses the model using GORM's statement parser to identify the primary key.
- **Usage**:
    - Internally used by the admin panel to identify records.

#### `GetPrimaryKeyType`

Retrieves the type of the primary key field.

```go
func (i *Integrator) GetPrimaryKeyType(model interface{}) (reflect.Type, error)
```

- **How It Works**:
    - Similar to `GetPrimaryKeyValue`, but returns the type instead.
- **Usage**:
    - Useful for validating input and constructing queries.

#### `FetchInstances`

Fetches all instances of a model.

```go
func (i *Integrator) FetchInstances(model interface{}) (interface{}, error)
```

- **How It Works**:
    - Uses `db.Find` to retrieve all records.
    - Creates a slice of model instances using reflection.
- **Usage**:
    - Displays lists of records in the admin panel.

#### `FetchInstancesOnlyFields`

Fetches instances selecting only specified fields.

```go
func (i *Integrator) FetchInstancesOnlyFields(
    model interface{}, fields []string,
) (interface{}, error)
```

- **How It Works**:
    - Constructs a select clause with the provided fields.
    - Uses reflection to ensure the fields exist in the model.
- **Usage**:
    - Optimizes queries by fetching only necessary data.

#### `FetchInstancesOnlyFieldWithSearch`

Fetches instances based on a search query.

```go
func (i *Integrator) FetchInstancesOnlyFieldWithSearch(
    model interface{}, fields []string, query string, searchFields []string,
) (interface{}, error)
```

- **How It Works**:
    - Constructs a where clause with `LIKE` conditions.
    - Searches across the specified `searchFields`.
- **Usage**:
    - Enables search functionality in the admin panel.

#### `DeleteInstance`

Deletes an instance by primary key.

```go
func (i *Integrator) DeleteInstance(
    model interface{}, instanceID interface{},
) error
```

- **How It Works**:
    - Builds a delete query using the primary key.
- **Usage**:
    - Allows deletion of records from the admin panel.

#### `FetchInstanceOnlyFields`

Fetches a single instance selecting only specified fields.

```go
func (i *Integrator) FetchInstanceOnlyFields(
    model interface{}, id interface{}, fields []string,
) (interface{}, error)
```

- **How It Works**:
    - Retrieves a single record by primary key.
    - Selects only specified fields for efficiency.
- **Usage**:
    - Used when viewing or editing a single record.

#### `FetchInstance`

Fetches a single instance.

```go
func (i *Integrator) FetchInstance(
    model interface{}, instanceID interface{},
) (interface{}, error)
```

- **How It Works**:
    - Similar to `FetchInstanceOnlyFields`, but selects all fields.
- **Usage**:
    - Retrieves all data for a single record.

#### `CreateInstance`

Creates a new instance in the database.

```go
func (i *Integrator) CreateInstance(instance interface{}) error
```

- **How It Works**:
    - Uses `db.Create` to insert the record.
- **Usage**:
    - Handles form submissions for creating new records.

#### `UpdateInstance`

Updates an existing instance.

```go
func (i *Integrator) UpdateInstance(
    instance interface{}, primaryKey interface{},
) error
```

- **How It Works**:
    - Uses `db.Save` to update the record.
    - Identifies the record using the primary key.
- **Usage**:
    - Processes updates from the admin panel.

#### `CreateInstanceOnlyFields`

Creates a new instance including only specified fields.

```go
func (i *Integrator) CreateInstanceOnlyFields(
    instance interface{}, fields []string,
) error
```

- **How It Works**:
    - Uses `db.Select(fields).Create` to insert the record.
- **Usage**:
    - Allows for partial inserts when not all fields are provided.

#### `UpdateInstanceOnlyFields`

Updates an existing instance including only specified fields.

```go
func (i *Integrator) UpdateInstanceOnlyFields(
    instance interface{}, fields []string, primaryKey interface{},
) error
```

- **How It Works**:
    - Uses `db.Model().Select(fields).Updates` to update specific fields.
- **Usage**:
    - Enables partial updates to records.

### Reflection and GORM Statements

The integrator heavily relies on Go's `reflect` package and GORM's `Statement` struct to parse models and extract 
metadata such as field names and primary keys.

- **Reflection**:
    - Used to dynamically interact with model types and values.
- **GORM Statements**:
    - Provide schema information about models.
    - Help in identifying database column names and primary keys.

### Error Handling

All methods return detailed errors to help diagnose issues during database operations. Proper error handling ensures 
that any problems are surfaced to the admin panel for appropriate user feedback.

## Customizing the GORM Integration

While the default GORM integrator should suffice for most use cases, you might need to customize it to handle specific 
scenarios.

### Extending the Integrator

You can extend the `Integrator` struct to add custom methods or override existing ones.

```go
type CustomGormIntegrator struct {
    *admingorm.Integrator
}

func NewCustomGormIntegrator(db *gorm.DB) *CustomGormIntegrator {
    return &CustomGormIntegrator{
        Integrator: admingorm.NewIntegrator(db),
    }
}

// Override methods or add new ones as needed
```

### Implementing Custom Logic

Override methods to implement custom logic, such as additional query filters, soft deletes, or custom error handling.

**Example: Adding Soft Delete Support**

```go
func (i *CustomGormIntegrator) DeleteInstance(
    model interface{}, instanceID interface{},
) error {
    // Custom logic for soft delete
    // Update the 'deleted_at' timestamp instead of hard deleting
    modelValue := reflect.ValueOf(model).Elem()
    deletedAtField := modelValue.FieldByName("DeletedAt")
    if !deletedAtField.IsValid() {
        return errors.New("model does not have a DeletedAt field")
    }
    deletedAtField.Set(reflect.ValueOf(time.Now()))
    return i.DB.Save(model).Error
}
```

### Registering the Custom Integrator

When initializing the admin panel or registering an app, pass your custom integrator:

```go
customOrmIntegrator := NewCustomGormIntegrator(db)

panel, err := admin.NewPanel(
    customOrmIntegrator, webIntegrator, permissionFunc, nil,
)
// ...
```

## Contributing to the GORM Integration

We welcome contributions to improve the GORM integration. Whether it's fixing bugs, adding new features, or improving 
documentation, your help is appreciated.

### How to Contribute

For detailed guidelines, please refer to our [Contributing Guide](Contributing.md).

### Reporting Issues

If you encounter any issues or have suggestions, please open an issue on GitHub in the 
[admin-gorm repository](https://github.com/go-advanced-admin/orm-gorm/issues).

## Conclusion

The GORM integration provides a powerful and flexible way to manage your application's data through the Go Advanced 
Admin Panel. By understanding how the integrator works and how to customize it, you can tailor the admin panel to fit 
your specific needs.

For further assistance or to explore more advanced features, refer to the additional documentation or reach out to the 
community.

---

> The code examples provided in this guide are based on the GORM integration code and demonstrate typical usage 
> scenarios. Adjustments might be necessary based on your specific application setup.

> This section of the documentation was written with the help of [Infuzu AI](https://infuzu.com)'s tools.
{style="note"}
