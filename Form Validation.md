## Server Side Validation

Compare
```cs
 using System.ComponentModel.DataAnnotations;
 public class Customer  
 {  
     public string   Name      { get; set; }  
     public DateTime EntryDate { get; set; }  
     public string   Password  { get; set; }  
     [Compare("Customer.Password", ErrorMessage = "The fields Password and PasswordConfirmation should be equals")]  
     public string   PasswordConfirmation { get; set; }  
 }
```

DataType
```cs
public class Customer  
{  
    public string Name { get; set; }  
    public DateTime EntryDate { get; set; }  

    [DataType(DataType.Password)]  
    public string Password { get; set; }  

    public string PasswordConfirmation { get; set; }  
}
```

```cs
public class Customer  
{   
[StringLength(maximumLength: 50  , MinimumLength = 10,  
    ErrorMessage = "The property {0} should have {1} maximum characters and {2} minimum characters")]  
     public string Name { get; set; }  

     public DateTime EntryDate { get; set; }  
     public string Password { get; set; }  
     public string PasswordConfirmation { get; set; }  
 }
```

Required
```cs
 public class Customer  
 {  

     [Required (ErrorMessage = "{0} is a mandatory field")]  
     public string Name { get; set; }  

     public DateTime EntryDate { get; set; }  
     public string Password { get; set; }  
     public string PasswordConfirmation { get; set; }  
 }
```
MaxLength MinLength
```cs
 public class Customer  
 {   
     [MaxLength(50, ErrorMessage = "The {0} can not have more than {1} characters")]  
     public string Name { get; set; }  

     public DateTime EntryDate { get; set; }  
     [DataType(DataType.Password)]  
    public string Password { get; set; }  
     [Compare("Customer.Password", ErrorMessage = "The fields Password and PasswordConfirmation should be equals")]  
     public string PasswordConfirmation { get; set; }  

    [MaxLength(2, ErrorMessage = "The property {0} can not have more than {1} elements")]  
     public int[] EjArrayInt { get; set; }  
 }
```

Range
```cs
 public class Customer  
 {  
     public string Name { get; set; }  

     [Range(typeof(DateTime), "01/01/1900", "01/01/2014",  
     ErrorMessage = "Valid dates for the Property {0} between {1} and {2}")]  
     public DateTime EntryDate { get; set; }  

     public string Password { get; set; }  
     [Compare("Customer.Password", ErrorMessage = "The fields Password and PasswordConfirmation should be equals")]  
     public string PasswordConfirmation { get; set; }  

     [Range(0, 150, ErrorMessage = "The Age should be between 0 and 150 years")]  
     public int Age { get; set; }  
 }
```

CustomValidation
First create a class
```cs
- public class CustomerWeekendValidation  
- {  
-     public static ValidationResult WeekendValidate(DateTime date)  
-     {  
-         return date.DayOfWeek == DayOfWeek.Saturday || date.DayOfWeek == DayOfWeek.Sunday  
-             ? new ValidationResult("The wekeend days aren't valid")  
-             : ValidationResult.Success;  
-     }  
- }
```
Then add like this:
```cs
- public class Customer  
- {  
-     public string Name { get; set; }  

-     [CustomValidation(typeof(CustomerWeekendValidation), nameof(CustomerWeekendValidation.WeekendValidate))]  
-     public DateTime EntryDate { get; set; }  

-     public string Password { get; set; }  
-     public string PasswordConfirmation { get; set; }  
-     public int Age { get; set; }  
- }
```
Creating a custom keyword  for data annotation
Create a class inheriting ValidationAttribute
```cs
- public class ControlDateTimeAttribute : ValidationAttribute  
- {  
-     private DayOfWeek[] NotValidDays;  
-     private bool        ThrowExcepcion;  

-     public ControlDateTimeAttribute(params DayOfWeek[] notValidDays)  
-     {  
-         ThrowExcepcion = false;  
-         NotValidDays   = notValidDays;  
-     }  

-     public ControlDateTimeAttribute(bool throwExcepcion, params DayOfWeek[] notValidDays)  
-     {  
-         ThrowExcepcion = throwExcepcion;  
-         NotValidDays   = notValidDays;  
-     }  

-     public override bool IsValid(object value)  
-     {  
-         DateTime fecha;  

-         if (!DateTime.TryParse(value.ToString(), out fecha))  
-         {  
-             if (ThrowExcepcion)  
-                 throw new ArgumentException(  
-                     "The ControlDateTimeAttribute, only validate DateTime Types.");  
-             else  
-                 return false;  
-         }  

-         return NotValidDays.Contains(fecha.DayOfWeek);  
-     }  
- }
```

Now use the Custom annotation
```cs
- public class Customer  
- {  
-     public string Name { get; set; }  

-     [ControlDateTime(DayOfWeek.Monday, DayOfWeek.Tuesday, DayOfWeek.Wednesday,ErrorMessage = "The {0} isn't valid")]  
-     public DateTime EntryDate { get; set; }  

-     public string Password { get; set; }  
-     public string PasswordConfirmation { get; set; }  
-     public int Age { get; set; }  
- }
```


## Client Side Validation
**EditForm** component bound to a model that uses Data Annotation

```razor 
<EditForm Model="@exampleModel" OnSubmit="@HandleSubmit">
```
**OnSubmit** can be replaces with **OnValidSubmit** which processes assigned event handler if the form is valid when submitted by the user. There is also **OnInvalidSubmit** to assign event handler when invalid fields are submitted. 
**ValidationSummary** component is added to display validation messages when the form is invalid on form submission. It simply renders an HTML `<ul>` containing any validation messages from the `EditContext`
**EditContext** tracks metadata about the edit process, including which fields have been modified and the current validation messages.
The **DataAnnotationsValidator** hooks into events on the `EditContext` and executes `DataAnnotations` rules. If you wanted to use a different validation system other than `DataAnnotations`, you'd swap **DataAnnotationsValidator** for something else.

```cs
<EditForm EditContext="@editContext" ...>

@code {
    private ExampleModel exampleModel = new() { ... };
    private EditContext? editContext;

    protected override void OnInitialized()
    {
        editContext = new(exampleModel);
    }
}
```

Larger Example:
```cs
@page "/form-example-2"
@using Microsoft.Extensions.Logging
@inject ILogger<FormExample2> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        ©1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        Logger.LogInformation("HandleValidSubmit called");

        // Process the valid form
    }
}
```

To improve on this, you can replace the low-level HTML input elements with Blazor's built-in input components. They know how to hook more deeply into the `EditContext`:

- When they are edited, they notify the `EditContext` immediately so it can refresh validation status.
- They also receive notifications about validity from the `EditContext`, so they can highlight themselves as either valid or invalid as the user edits them.

Go back to `AddressEditor.razor` once again. Replace each of the `<input>` elements with a corresponding `<InputText>` and also change `@bind` to `@bind-Value`. For example,

```html
<div class="form-field">
    <label>Name:</label>
    <div>
        <InputText @bind-Value="Address.Name" />
        <ValidationMessage For="@(() => Address.Name)" />
    </div>
</div>
```