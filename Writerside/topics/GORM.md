# GORM

## Configuration
Ensure GORM is installed:
```bash
go get gorm.io/gorm
go get gorm.io/driver/sqlite
```

## Model Registration
Define your models using GORM conversions:
```go
type User struct {
    ID       uint   `gorm:"primarykey"`
    Username string
    Email    string
}
```

Register the model with the admin panel:
```go
app, err := panel.RegisterApp("UserManagement", "User Management", nil)
if err != nil {
    log.Fatalf("Failed to register app: %v", err)
}

_, err = app.RegisterModel(&User{}, nil)
if err != nil {
    log.Fatalf("Failed to register model: %v", err)
}
```

## Querying and Filtering Data
The admin panel leverages GORM's querying capabilities. You can customize queries by [implementing the `ORMIntegrator` interface methods](Building-a-Custom-ORM-Integration.md).