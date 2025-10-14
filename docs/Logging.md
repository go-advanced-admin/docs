# Logging

Effective logging is crucial for monitoring, debugging, and auditing activities within your admin panel.
The **go-advanced-admin** provides a flexible logging system that you can configure to suit your needs. This guide will
walk you through configuring logging levels, using the default log store, and integrating a custom log store such as a
database-backed log store.

---

- [Overview](#overview)
- [Configuring the Log Store Level](#configuring-the-log-store-level)
- [Using the Default Log Store](#using-the-default-log-store)
- [Building a Custom Log Store](#building-a-custom-log-store)
  - [Implementing the Required Interface](#implementing-the-required-interface)
  - [Handling Log Messages](#handling-log-messages)
  - [Registering the Custom Log Store](#registering-the-custom-log-store)
- [Contributing Your Log Store](#contributing-your-log-store)
- [References](#references)

---

## Overview

The logging system in **go-advanced-admin** is designed to be:

- **Configurable**: Adjust the verbosity and types of logs you receive.
- **Extensible**: Use the default in-memory log store or integrate your own.
- **Informative**: Keep track of actions like CRUD operations, user interactions, and system events.

## Configuring the Log Store Level

The **log store level** determines the types of actions that are recorded in the logs. By configuring this level, you
can control the verbosity of your logging system.

### Available Log Levels

The following log levels are available:

- `LogStoreLevelDelete`: Logs delete actions.
- `LogStoreLevelCreate`: Logs create actions.
- `LogStoreLevelUpdate`: Logs update actions.
- `LogStoreLevelInstanceView`: Logs when an instance is viewed.
- `LogStoreLevelListView`: Logs when a list of instances is viewed.
- `LogStoreLevelPanelView`: Logs when the admin panel is accessed.

### Setting the Log Level

You can set the log level when initializing your admin panel configuration:

```go
import (
    "github.com/go-advanced-admin/admin"
    "github.com/go-advanced-admin/admin/internal/logging"
)

func main() {
    config := &admin.Config{
        Name:          "My Admin Panel",
        Prefix:        "admin",
        LogStoreLevel: logging.LogStoreLevelUpdate, // Set the desired log level here
    }

    // Initialize the admin panel with the config
    panel, err := admin.NewPanel(ormIntegrator, webIntegrator, permissionFunc, config)
    if err != nil {
        panic(err)
    }

    // ... rest of your setup
}
```

**Note:** Setting a higher log level includes all the lower levels. For example, setting `LogStoreLevelUpdate` will log
update actions and all actions with higher importance (create and delete).

## Using the Default Log Store

By default, **go-advanced-admin** uses an in-memory log store that retains a limited number of log entries.

### Features of the Default Log Store

- **In-Memory Storage**: Logs are stored in memory, suitable for development and testing.
- **Limited Capacity**: Configurable maximum number of log entries to prevent memory overflow.
- **FIFO Policy**: Oldest logs are discarded when the capacity is exceeded.

### Configuring the Default Log Store

You can adjust the capacity of the default log store during initialization:

```go
import (
    "github.com/go-advanced-admin/admin"
    "github.com/go-advanced-admin/admin/internal/logging"
)

func main() {
    config := &admin.Config{
        Name:          "My Admin Panel",
        Prefix:        "admin",
        LogStore:      logging.NewInMemoryLogStore(500), // Set capacity to 500 entries
        LogStoreLevel: logging.LogStoreLevelPanelView,
    }

    // Initialize the admin panel with the config
    panel, err := admin.NewPanel(ormIntegrator, webIntegrator, permissionFunc, config)
    if err != nil {
        panic(err)
    }

    // ... rest of your setup
}
```

## Building a Custom Log Store

For production environments or advanced logging needs, you might want to store logs in a persistent storage like a
database. You can achieve this by implementing a custom log store.

### Implementing the Required Interface

Your custom log store must implement the `LogStore` interface defined in the `logging` package:

```go
type LogStore interface {
    InsertLogEntry(logEntry *LogEntry) error
    GetLogEntry(id interface{}) (*LogEntry, error)
    GetLogEntries() ([]*LogEntry, error)
}
```

### Handling Log Messages

Implement the methods to handle log entries according to your storage medium.

#### Example: Database-Backed Log Store

Here's a simplified example using GORM to store logs in a database.

```go
import (
    "github.com/go-advanced-admin/admin/internal/logging"
    "gorm.io/gorm"
)

// Define a model that matches the LogEntry structure
type LogEntryModel struct {
    ID          string `gorm:"primaryKey"`
    ActionTime  time.Time
    UserID      string
    UserRepr    string
    ContentType string
    ObjectID    string
    ObjectRepr  string
    ActionFlag  string
    Message     string
}

type DatabaseLogStore struct {
    DB *gorm.DB
}

func (store *DatabaseLogStore) InsertLogEntry(logEntry *logging.LogEntry) error {
    model := LogEntryModel{
        ID:          fmt.Sprint(logEntry.ID),
        ActionTime:  logEntry.ActionTime,
        UserID:      fmt.Sprint(logEntry.UserID),
        UserRepr:    logEntry.UserRepr,
        ContentType: logEntry.ContentType,
        ObjectID:    fmt.Sprint(logEntry.ObjectID),
        ObjectRepr:  logEntry.ObjectRepr,
        ActionFlag:  string(logEntry.ActionFlag),
        Message:     logEntry.Message,
    }
    return store.DB.Create(&model).Error
}

func (store *DatabaseLogStore) GetLogEntry(id interface{}) (*logging.LogEntry, error) {
    var model LogEntryModel
    err := store.DB.First(&model, "id = ?", fmt.Sprint(id)).Error
    if err != nil {
        return nil, err
    }
    return convertModelToLogEntry(&model), nil
}

func (store *DatabaseLogStore) GetLogEntries() ([]*logging.LogEntry, error) {
    var models []LogEntryModel
    err := store.DB.Order("action_time DESC").Find(&models).Error
    if err != nil {
        return nil, err
    }
    entries := make([]*logging.LogEntry, len(models))
    for i, model := range models {
        entries[i] = convertModelToLogEntry(&model)
    }
    return entries, nil
}

func convertModelToLogEntry(model *LogEntryModel) *logging.LogEntry {
    return &logging.LogEntry{
        ID:          model.ID,
        ActionTime:  model.ActionTime,
        UserID:      model.UserID,
        UserRepr:    model.UserRepr,
        ContentType: model.ContentType,
        ObjectID:    model.ObjectID,
        ObjectRepr:  model.ObjectRepr,
        ActionFlag:  logging.LogStoreLevel(model.ActionFlag),
        Message:     model.Message,
    }
}
```

### Registering the Custom Log Store

Once your custom log store is implemented, you can register it in your admin panel configuration:

```go
func main() {
    // Initialize your GORM database connection
    db, err := gorm.Open(...)
    if err != nil {
        panic(err)
    }

    // Migrate the LogEntryModel
    db.AutoMigrate(&LogEntryModel{})

    // Create an instance of your custom log store
    logStore := &DatabaseLogStore{DB: db}

    config := &admin.Config{
        Name:          "My Admin Panel",
        Prefix:        "admin",
        LogStore:      logStore, // Use your custom log store
        LogStoreLevel: logging.LogStoreLevelPanelView,
    }

    // Initialize the admin panel with the config
    panel, err := admin.NewPanel(ormIntegrator, webIntegrator, permissionFunc, config)
    if err != nil {
        panic(err)
    }

    // ... rest of your setup
}
```

## Contributing Your Log Store

If you've built a custom log store that you believe could benefit others, we encourage you to contribute it back to the
community!

### Steps to Contribute

1. **Fork the Repository**: Start by forking the [go-advanced-admin](https://github.com/go-advanced-admin/admin)
   repository.

2. **Add Your Log Store**: Implement your log store in the appropriate package or create a new one.

3. **Write Tests**: Ensure your log store is well-tested.

4. **Update Documentation**: Provide documentation and examples for others to use.

5. **Submit a Pull Request**: Open a pull request with a detailed description of your contribution.

### Contribution Guidelines

Please refer to our [Contribution Guide](Configuration.md) for detailed instructions on how to contribute code, report
issues, and suggest enhancements.

Your contributions help make **go-advanced-admin** better for everyone!

## References

- **Quick Start Guide**: Get started quickly by following our [Quick Start Guide](Quick-Start.md).
- **Permissions**: Learn how to set up permissions in the [Permissions Guide](Permissions.md).
- **Issue Tracker**: Report issues or request features on our
- [GitHub Issues Page](https://github.com/go-advanced-admin/admin/issues).

---

**Next Steps:**

- Explore the [](Advanced-Topics.md) for more customization options.
- Read about [](Templates.md) to customize the UI.

---

>This documentation is part of the **go-advanced-admin** project and is maintained by the community. If you find any
> discrepancies or have suggestions, please [open an issue](https://github.com/go-advanced-admin/admin/issues).

---

Thank you for using **go-advanced-admin**!


> This section of the documentation was written with the help of [Infuzu AI](https://infuzu.com)'s tools.
{style="note"}
