# Forms

The **go-advanced-admin** panel includes a flexible and powerful forms framework that allows you to handle user input 
efficiently. This framework is used extensively within the admin panel for creating and editing instances of your 
models, but it can also be used standalone in other parts of your application.

This guide covers everything you need to know about configuring forms, writing custom fields, integrating forms with 
your models, and using the forms framework independently.

---

- [Overview](#overview)
- [Form Field Types](#form-field-types)
    - [TextField](#textfield)
    - [IntegerField](#integerfield)
    - [FloatField](#floatfield)
    - [BooleanField](#booleanfield)
    - [ChoiceField](#choicefield)
    - [MultipleChoiceField](#multiplechoicefield)
    - [DateField](#datefield)
    - [EmailField](#emailfield)
    - [URLField](#urlfield)
- [Validation](#validation)
    - [Field-Level Validation](#field-level-validation)
    - [Form-Level Validation](#form-level-validation)
- [Customization](#customization)
    - [Customizing Fields](#customizing-fields)
    - [Creating Custom Fields](#creating-custom-fields)
- [Integrating Forms with Models](#integrating-forms-with-models)
    - [Customizing Add and Edit Forms](#customizing-add-and-edit-forms)
    - [Using Tags for Field Configuration](#using-tags-for-field-configuration)
    - [Implementing the `AdminFormFieldInterface`](#implementing-the-adminformfieldinterface)
- [Using Forms Standalone](#using-forms-standalone)
    - [Creating a Standalone Form](#creating-a-standalone-form)
    - [Rendering the Form](#rendering-the-form)
    - [Processing Form Data](#processing-form-data)
- [Next Steps](#next-steps)

---

## Overview

The forms framework provides a way to define forms with various field types, handle validation, and render forms in 
HTML. It is designed to be extensible, allowing you to create custom fields and validation logic as needed.

Key features include:

- **Multiple Field Types**: A variety of built-in field types to capture different kinds of user input.
- **Validation**: Both field-level and form-level validation to ensure data integrity.
- **Customization**: Ability to customize fields and forms, including creating custom field types.
- **Integration with Models**: Seamless integration with your data models for creating and editing records.
- **Standalone Usage**: Use the forms framework independently in other parts of your application.

---

## Form Field Types

The forms framework comes with several built-in field types to handle common data inputs. Each field type has its own 
properties and validation logic.

### TextField

Used for single-line text input.

**Properties:**

- `Placeholder`: Placeholder text displayed in the input field.
- `MaxLength`: Maximum number of characters allowed.
- `MinLength`: Minimum number of characters required.
- `Required`: Whether the field is mandatory.
- `Regex`: A regular expression pattern that the input must match.

**Example:**

```go
textField := &fields.TextField{
    Placeholder: utils.StringPtr("Enter your name"),
    MaxLength:   utils.UintPtr(50),
    Required:    true,
}
```

### IntegerField

Used for integer numbers.

**Properties:**

- `MinValue`: Minimum acceptable value.
- `MaxValue`: Maximum acceptable value.
- `Required`: Whether the field is mandatory.

**Example:**

```go
integerField := &fields.IntegerField{
    MinValue: utils.IntPtr(1),
    MaxValue: utils.IntPtr(100),
    Required: true,
}
```

### FloatField

Used for floating-point numbers.

**Properties:**

- `MinValue`: Minimum acceptable value.
- `MaxValue`: Maximum acceptable value.
- `Required`: Whether the field is mandatory.

**Example:**

```go
floatField := &fields.FloatField{
    MinValue: utils.Float64Ptr(0.0),
    MaxValue: utils.Float64Ptr(100.0),
    Required: true,
}
```

### BooleanField

Used for boolean values (true/false).

**Properties:**

- `Required`: Whether the field is mandatory.

**Example:**

```go
booleanField := &fields.BooleanField{
    Required: false,
}
```

### ChoiceField

Used for single selection from a list of choices.

**Properties:**

- `Choices`: A list of `Choice` structs, each with a `Value` and `Label`.
- `Required`: Whether the field is mandatory.
- `Placeholder`: Placeholder text displayed when no option is selected.

**Example:**

```go
choiceField := &fields.ChoiceField{
    Choices: []fields.Choice{
        {Value: "admin", Label: "Administrator"},
        {Value: "user", Label: "User"},
    },
    Required: true,
    Placeholder: utils.StringPtr("Select a role"),
}
```

### MultipleChoiceField

Used for multiple selections from a list of choices.

**Properties:**

- `Choices`: A list of `Choice` structs.
- `Required`: Whether at least one selection is required.

**Example:**

```go
multipleChoiceField := &fields.MultipleChoiceField{
    Choices: []fields.Choice{
        {Value: "read", Label: "Read"},
        {Value: "write", Label: "Write"},
        {Value: "delete", Label: "Delete"},
    },
    Required: true,
}
```

### DateField

Used for date input.

**Properties:**

- `MinDate`: Earliest acceptable date.
- `MaxDate`: Latest acceptable date.
- `Required`: Whether the field is mandatory.
- `Placeholder`: Placeholder text.

**Example:**

```go
minDate := time.Now().AddDate(-1, 0, 0) // One year ago
maxDate := time.Now().AddDate(1, 0, 0)  // One year from now

dateField := &fields.DateField{
    MinDate:     &minDate,
    MaxDate:     &maxDate,
    Required:    true,
    Placeholder: utils.StringPtr("Select a date"),
}
```

### EmailField

Used for email addresses.

**Properties:**

- `Required`: Whether the field is mandatory.

**Example:**

```go
emailField := &fields.EmailField{
    Required: true,
}
```

### URLField

Used for URLs.

**Properties:**

- `Required`: Whether the field is mandatory.

**Example:**

```go
urlField := &fields.URLField{
    Required: false,
}
```

---

## Validation

Validation ensures that the data submitted through forms meets your application's requirements. The forms framework 
supports both field-level and form-level validation.

### Field-Level Validation

Each field type comes with built-in validation functions based on its properties. For example, setting `Required: true` 
on a `TextField` will automatically validate that the field is not empty.

**Custom Field Validation**

You can add custom validation functions to a field:

```go
textField.RegisterValidationFunctions(func(value interface{}) ([]error, error) {
    strValue, ok := value.(string)
    if !ok {
        return nil, errors.New("value must be a string")
    }
    if len(strValue) < 5 {
        return []error{errors.New("minimum length is 5")}, nil
    }
    return nil, nil
})
```

### Form-Level Validation

Form-level validation functions allow you to validate the form as a whole, possibly checking the relationship between 
different fields.

**Adding Form-Level Validation**

```go
myForm.RegisterValidationFunctions(
    func(values map[string]interface{}) ([]error, error) {
    if values["password"] != values["confirm_password"] {
        return []error{errors.New("passwords do not match")}, nil
    }
    return nil, nil
})
```

---

## Customization

### Customizing Fields

You can customize fields by setting their properties or overriding methods.

**Setting Superseding Attributes**

Superseding attributes allow you to directly set HTML attributes on the field's input element.

```go
textField.SetSupersedingAttribute("class", utils.StringPtr("form-control"))
textField.SetSupersedingAttribute("id", utils.StringPtr("custom-id"))
```

### Creating Custom Fields

If the built-in field types don't meet your needs, you can create custom fields by implementing the `Field` interface.

**Field Interface**

```go
type Field interface {
    HTML() (string, error)
    GoTypeToHTMLType(value interface{}) (HTMLType, error)
    HTMLTypeToGoType(value HTMLType) (interface{}, error)
    RegisterValidationFunctions(validationFuncs ...FieldValidationFunc)
    GetValidationFunctions() []FieldValidationFunc
    GetName() string
    RegisterName(name string) error
    RegisterInitialValue(value interface{})
    SetSupersedingAttribute(name string, value *string)
    RegisterLabel(label string) error
    GetLabel() string
}
```

**Example Custom Field**

```go
type ColorField struct {
    fields.BaseField
    Required bool
}

func (f *ColorField) HTML() (string, error) {
    // Implement HTML rendering for a color input
}

func (f *ColorField) GoTypeToHTMLType(value interface{}) (form.HTMLType, error) {
    // Convert Go value to HTML input value
}

func (f *ColorField) HTMLTypeToGoType(value form.HTMLType) (interface{}, error) {
    // Convert HTML input value to Go value
}

func (f *ColorField) GetValidationFunctions() []form.FieldValidationFunc {
    // Return any validation functions
}
```

---

## Integrating Forms with Models

The admin panel automatically generates forms for creating and editing instances of your models. You can customize 
these forms using tags in your model definitions or by implementing interfaces.

### Customizing Add and Edit Forms

You can customize the forms used for adding and editing instances by:

- **Using Struct Tags**
- **Implementing the `AdminFormFieldInterface`**

### Using Tags for Field Configuration

The `admin` struct tag allows you to specify how fields are included in forms.

**Example Model with Tags**

```go
type User struct {
    ID       uint   `admin:"listDisplay:include;addForm:exclude"`
    Username string `admin:"required:true;maxLength:30"`
    Email    string `admin:"required:true;email:true"`
    Password string `admin:"required:true;minLength:8"`
}
```

**Common Tag Options:**

- `addForm`: `include` or `exclude` to control inclusion in the add form.
- `editForm`: `include` or `exclude` to control inclusion in the edit form.
- `required`: `true` or `false` to set the field as required.
- `maxLength`: Maximum length for string fields.
- `minLength`: Minimum length for string fields.
- `placeholder`: Placeholder text for input fields.

### Implementing the `AdminFormFieldInterface`

For more advanced customization, implement the `AdminFormFieldInterface` in your model.

**Interface Definition**

```go
type AdminFormFieldInterface interface {
    AdminFormField(name string, isEdit bool) form.Field
}
```

**Example Implementation**

```go
func (u *User) AdminFormField(name string, isEdit bool) form.Field {
    switch name {
    case "Password":
        if isEdit {
            // Return a password field that allows empty input
            return &fields.PasswordField{
                Required: false,
            }
        } else {
            // Return a password field that is required
            return &fields.PasswordField{
                Required: true,
            }
        }
    default:
        return nil // Use default behavior
    }
}
```

---

## Using Forms Standalone

The forms framework can be used outside of the admin panel in your application.

### Creating a Standalone Form

**Define a Form Struct**

```go
type ContactForm struct {
    forms.BaseForm
}

func NewContactForm() *ContactForm {
    form := &ContactForm{}
    form.AddField("name", &fields.TextField{
        Required: true,
    })
    form.AddField("email", &fields.EmailField{
        Required: true,
    })
    form.AddField("message", &fields.TextField{
        Required: true,
    })
    return form
}
```

### Rendering the Form

**Render the Form in a Template**

```go
func renderForm(form form.Form) (string, error) {
    return form.RenderFormAsP(form, nil, nil)
}
```

**Example Usage in Handler**

```go
func ContactHandler(w http.ResponseWriter, r *http.Request) {
    contactForm := NewContactForm()
    if r.Method == "GET" {
        html, err := renderForm(contactForm)
        if err != nil {
            http.Error(w, err.Error(), http.StatusInternalServerError)
            return
        }
        fmt.Fprint(w, html)
    } else if r.Method == "POST" {
        // Handle form submission
    }
}
```

### Processing Form Data

**Handle Form Submission**

```go
func ContactHandler(w http.ResponseWriter, r *http.Request) {
    contactForm := NewContactForm()
    if r.Method == "POST" {
        r.ParseForm()
        formData := r.PostForm
        htmlTypeMap, err := form.ConvertFormDataToHTMLTypeMap(formData)
        if err != nil {
            http.Error(w, err.Error(), http.StatusBadRequest)
            return
        }

        cleanData, err := form.GetCleanData(contactForm, htmlTypeMap)
        if err != nil {
            // Handle validation errors
            return
        }

        // Process the cleanData
        name := cleanData["name"].(string)
        email := cleanData["email"].(string)
        message := cleanData["message"].(string)

        // Send email or save to database
    }
}
```

---

## Next Steps

- **Explore Advanced Topics**: Learn about [Customizing the Admin Panel](customization.md) and [Security Best Practices](security.md).
- **Check Integrations**: See how to integrate with different [ORMs](ORM.md) and [Web Frameworks](Web.md).
- **Contribute**: If you encounter issues or have suggestions, please 
- [open an issue](https://github.com/go-advanced-admin/admin/issues) or submit a pull request.

---

**Feedback**

We value your feedback. If you have any suggestions or find something unclear in this guide, please let us know by 
[opening an issue](https://github.com/go-advanced-admin/admin/issues/new/choose).

---

**Thank you for using go-advanced-admin!**