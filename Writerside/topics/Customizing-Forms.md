# Customizing Forms

The Go Advanced Admin Panel provides a flexible form framework that allows you to customize forms and even add your own 
custom form field types.

## Table of Contents

- [Introduction](#introduction)
- [Understanding the Form Framework](#understanding-the-form-framework)
- [Customizing Existing Forms](#customizing-existing-forms)
    - [Modifying Field Attributes](#modifying-field-attributes)
    - [Adding or Removing Fields](#adding-or-removing-fields)
    - [Custom Validation](#custom-validation)
- [Adding Custom Form Field Types](#adding-custom-form-field-types)
    - [Steps to Add a Custom Field Type](#steps-to-add-a-custom-field-type)
- [Example: Creating a Custom Field](#example-creating-a-custom-field)
    - [Implementing a Rich Text Editor Field](#implementing-a-rich-text-editor-field)
- [Registering Custom Fields in Models](#registering-custom-fields-in-models)
- [Additional Resources](#additional-resources)

---

## Introduction

Custom forms can enhance data entry and improve user experience by providing more suitable input mechanisms for your 
data types. This guide will help you customize forms and create custom form field types.

## Understanding the Form Framework

The form framework is based on the `form.Field` interface, which defines methods for:

- **Rendering HTML**: Generating the HTML representation of the form field.
- **Type Conversion**: Converting between Go types and HTML types.
- **Validation**: Ensuring that input data meets certain criteria.

## Customizing Existing Forms

### Modifying Field Attributes

You can modify attributes such as labels, placeholders, and validation rules directly in your model definitions or by 
customizing the form fields.

### Adding or Removing Fields

Control which fields appear in your forms by using struct tags in your models.

- **Exclude a Field from a Form**:

  ```go
  type User struct {
      ID       uint   `gorm:"primarykey" admin:"addForm:exclude"`
      Username string
      Email    string
  }
  ```

### Custom Validation

Add custom validation functions to your form fields to enforce specific rules.

- **Register a Validation Function**:

  ```go
  field.RegisterValidationFunctions(func(value interface{}) ([]error, error) {
      // Your validation logic here
  })
  ```

## Adding Custom Form Field Types

If the built-in field types do not meet your requirements, you can create custom field types by implementing the 
`form.Field` interface.

### Steps to Add a Custom Field Type

1. **Define a New Field Type**: Create a struct that embeds `fields.BaseField` and implements the `form.Field` 
interface.

   ```go
   type RichTextField struct {
       fields.BaseField
       // Additional properties if needed
   }
   ```

2. **Implement Required Methods**:

    - `HTML() (string, error)`: Return the HTML representation of the field.
    - `GoTypeToHTMLType(value interface{}) (form.HTMLType, error)`: Convert a Go value to an HTML-compatible value.
    - `HTMLTypeToGoType(value form.HTMLType) (interface{}, error)`: Convert an HTML value back to a Go value.
    - `GetValidationFunctions() []form.FieldValidationFunc`: Return any validation functions.

3. **Use the Custom Field**: Register the field in your form or model where needed.

## Example: Creating a Custom Field

### Implementing a Rich Text Editor Field

1. **Define the Field**:

   ```go
   type RichTextField struct {
       fields.BaseField
   }

   func (f *RichTextField) HTML() (string, error) {
       // Return HTML for a rich text editor, e.g., using a JavaScript plugin
       html := fmt.Sprintf(
           `<textarea name="%s">%s</textarea>`, f.Name, template.HTMLEscapeString(f.InitialValue.(string)), 
       )
       return html, nil
   }

   func (f *RichTextField) GoTypeToHTMLType(value interface{}) (form.HTMLType, error) {
       // Convert Go value to HTML string
       strValue, ok := value.(string)
       if !ok {
           return "", errors.New("value must be a string")
       }
       return form.HTMLType(strValue), nil
   }

   func (f *RichTextField) HTMLTypeToGoType(value form.HTMLType) (interface{}, error) {
       // Convert HTML string to Go value
       return string(value), nil
   }

   func (f *RichTextField) GetValidationFunctions() []form.FieldValidationFunc {
       // Return any validation functions if needed
       return nil
   }
   ```

2. **Register the Field in a Form**:

   ```go
   formInstance.AddField("Content", &RichTextField{})
   ```

## Registering Custom Fields in Models

To use custom fields within your models:

1. **Implement `AdminFormFieldInterface`** in your model:

   ```go
   func (p *Post) AdminFormField(name string, isEdit bool) form.Field {
       if name == "Content" {
           return &RichTextField{}
       }
       return nil
   }
   ```

2. **Return the Custom Field**: Provide your custom field for the specified field name.

## Additional Resources

- [Form Framework Documentation](Forms.md)


> This section of the documentation was written with the help of [Infuzu AI](https://infuzu.com)'s tools.
{style="note"}
