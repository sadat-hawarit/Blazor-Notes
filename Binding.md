```razor
    <input @bind="InputValue" />
    <input value="@InputValue"
        @onchange="@((ChangeEventArgs __e) => InputValue = __e?.Value?.ToString())" />
    </label>
```
@bind:event
@bind:after
@bind:get
@bind:set
@bind:format
@bind-Value
@bind-**Property**
we can also bind lambda expressions

You can bind parameters through any number of nested components, but you must respect the one-way flow of data:

- Change notifications _flow up the hierarchy_.
- New parameter values _flow down the hierarchy_.

A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated, as shown in the following example.

`Pages/Parent2.razor`:

razor

```
@page "/parent-2"

<h1>Parent Component</h1>

<p>Parent Message: <b>@parentMessage</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<NestedChild @bind-ChildMessage="parentMessage" />

@code {
    private string parentMessage = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentMessage = $"Set in Parent {DateTime.Now}";
    }
}
```

In the following `NestedChild` component, the `NestedGrandchild` component:

- Assigns the value of `ChildMessage` to `GrandchildMessage` with `@bind:get` syntax.
- Updates `GrandchildMessage` when `ChildMessageChanged` executes with `@bind:set` syntax.

`Shared/NestedChild.razor`:

razor

```
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Message: <b>@ChildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <NestedGrandchild @bind-GrandchildMessage:get="ChildMessage" 
        @bind-GrandchildMessage:set="ChildMessageChanged" />
</div>

@code {
    [Parameter]
    public string? ChildMessage { get; set; }

    [Parameter]
    public EventCallback<string> ChildMessageChanged { get; set; }

    private async Task ChangeValue()
    {
        await ChildMessageChanged.InvokeAsync(
            $"Set in Child {DateTime.Now}");
    }
}
```

`Shared/NestedGrandchild.razor`:

razor

```
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Message: <b>@GrandchildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string? GrandchildMessage { get; set; }

    [Parameter]
    public EventCallback<string> GrandchildMessageChanged { get; set; }

    private async Task ChangeValue()
    {
        await GrandchildMessageChanged.InvokeAsync(
            $"Set in Grandchild {DateTime.Now}");
    }
}
```

```razor
@page "/form-example-10"
@inject IJSRuntime JS
@inject ILogger<FormExample10> Logger

<h1>Stream form data with JS interop</h1>

<EditForm Model="@exampleModel" OnSubmit="@HandleSubmit">
    <p>
        <label>
            &lt;textarea&gt; value streamed for assignment to 
            <code>TextAreaValue (&lt;= 50,000 characters)</code>:
            <textarea @ref="largeTextArea" />
        </label>
    </p>

    <button type="submit">Submit</button>
</EditForm>

<p>
    TextAreaValue length: @exampleModel.TextAreaValue1.Length
</p>

@code {
    private ExampleModel2 exampleModel = new();
    private ElementReference largeTextArea;

    private async Task HandleSubmit()
    {
        exampleModel.TextAreaValue = await GetTextAsync();

        Logger.LogInformation("TextAreaValue length: {Length}", 
            exampleModel.TextAreaValue.Length);
    }

    public async Task<string> GetTextAsync()
    {
        try
        {
            var streamRef = 
                await JS.InvokeAsync<IJSStreamReference>("getText", largeTextArea);
            var stream = await streamRef.OpenReadStreamAsync(maxAllowedSize: 50_000);
            var streamReader = new StreamReader(stream);

            return await streamReader.ReadToEndAsync();
        }
        catch (JSException jsException)
        {
            if (jsException.InnerException is 
                    ArgumentOutOfRangeException outOfRangeException &&
                outOfRangeException.ActualValue is not null &&
                outOfRangeException.ActualValue is long actualLength &&
                actualLength == 0)
            {
                return string.Empty;
            }

            throw;
        }
    }
}

```