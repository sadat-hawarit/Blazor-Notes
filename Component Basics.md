@page directive defines the routes. More in [Routing & Navigation](obsidian://open?vault=Blazor&file=Routing%20%26%20Navigation)
@layout directive defines which layout to use. More in [Layout](obsidian://open?vault=Blazor&file=Layout)
@inject directive handles dependency injection. More in [Dependency Injection](obsidian://open?vault=Blazor&file=Dependency%20Injection)
@implements directive indicates which interface to implement in the code. 
@code directive adds functions and codes
In code, properties are added with [Parameter] attribute

A basic component example:
Counter.razor
```razor
@page "/counter"

<h1>Counter</h1>

<p role="status">Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    [Parameter]
    public int IncrementAmount { get; set; } = 1;

    private void IncrementCount()
    {
        currentCount += IncrementAmount;
    }
}
```
Index.razor
```
@page "/"

<h1>Hello, world!</h1>

<Counter IncrementAmount="10" />
```
Counter component was called from Index.razor with IncrementAmount passed down on it.

### Data Binding
Within Razor components, you can data bind HTML elements to C# fields, properties, and Razor expression values. Data binding allows two-way synchronization between HTML and Microsoft .NET.

Data is pushed from HTML to .NET when a component is rendered. Components render themselves after event-handler code executes, which is why property updates are reflected in the UI immediately after an event handler is triggered.

You can use `@bind` markup to bind a C# variable to an HTML object. You define the C# variable by name as a string in the HTML. You can see an example of data binding in the following exercise.