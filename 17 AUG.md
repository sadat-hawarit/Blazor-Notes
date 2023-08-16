Using `target="_blank"` without [`rel="noreferrer"`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/rel/noreferrer) and [`rel="noopener"`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/rel/noopener) makes the website vulnerable to [`window.opener`](https://developer.mozilla.org/en-US/docs/Web/API/Window/opener) API exploitation attacks, although note that, in newer browser versions setting `target="_blank"` implicitly provides the same protection as setting `rel="noopener"`.

Security steps that reduce the likelihood of a successful attack are:

- Download files from a dedicated file download area on the server, preferably from a non-system drive. Using a dedicated location makes it easier to impose security restrictions on downloadable files. Disable execute permissions on the file download area.
- Client-side security checks are easy to circumvent by malicious users. Always perform client-side security checks on the server, too.
- Don't receive files from users or other untrusted sources and then make the files available for immediate download without performing security checks on the files.

## Long Polling (Server-to-Client only)

Long Polling is a server-to-client half-transport, so it is always paired with HTTP Post. It requires a connection already be established using the `POST [endpoint-base]/negotiate` request.

Long Polling requires that the client poll the server for new messages. Unlike traditional polling, if there is no data available, the server will simply wait for messages to be dispatched. At some point, the server, client or an upstream proxy will likely terminate the connection, at which point the client should immediately re-send the request. Long Polling is the only transport that allows a "reconnection" where a new request can be received while the server believes an existing request is in process. This can happen because of a time out. When this happens, the existing request is immediately terminated with status code `204 No Content`. Any messages which have already been written to the existing request will be flushed and considered sent. In the case of a server side timeout with no data, a `200 OK` with a 0 `Content-Length` will be sent and the client should poll again for more data.

A Poll is established by sending an HTTP GET request to `[endpoint-base]` with the following query string parameters

#### [](https://github.com/dotnet/aspnetcore/blob/main/src/SignalR/docs/specs/TransportProtocols.md#version-1-1)Version 1

- `id` (Required) - The Connection Token of the destination connection.

#### [](https://github.com/dotnet/aspnetcore/blob/main/src/SignalR/docs/specs/TransportProtocols.md#version-0-1)Version 0

- `id` (Required) - The Connection ID of the destination connection.

When data is available, the server responds with a body in one of the two formats below (depending upon the value of the `Accept` header). The response may be chunked, as per the chunked encoding part of the HTTP spec.

If the `id` parameter is missing, a `400 Bad Request` response is returned. If there is no connection with the ID specified in `id`, a `404 Not Found` response is returned.

When the client has finished with the connection, it can issue a `DELETE` request to `[endpoint-base]` (with the `id` in the query string) to gracefully terminate the connection. The server will complete the latest poll with `204` to indicate that it has shut down.

A console warning appears if Long Polling is utilized:

> Failed to connect via WebSockets, using the Long Polling fallback transport. This may be due to a VPN or proxy blocking the connection.



## A field initializer cannot reference the non-static field, method, or property

The error message “A field initializer cannot reference the non-static field, method, or property” occurs when you try to use an instance variable to initialize another instance variable. This is not allowed because the compiler can rearrange the order of initialization, and there is no guarantee that the first instance variable will be initialized before the second one. This could result in a `NullReferenceException`.

To fix this error, you can either initialize the second instance variable with a constant value or set up its value in the constructor of the class. For example, instead of writing:

```csharp
private Reminders reminder = new Reminders();
private dynamic defaultReminder = reminder.TimeSpanText[TimeSpan.FromMinutes(15)];
```

You can write:

```csharp
private Reminders reminder = new Reminders();
private dynamic defaultReminder;

public SomeOtherClass()
{
    defaultReminder = reminder.TimeSpanText[TimeSpan.FromMinutes(15)];
}
```

This way, you ensure that `reminder` is initialized before `defaultReminder`


## Logger (Browser Console)
```razor
﻿@page "/"
@using Microsoft.Extensions.Logging
@inject ILogger<Index> Logger

<p>
    <button @onclick="LogMessages">Log Messages</button>
</p>

@code{
    private void LogMessages()
    {
        Logger.LogDebug(1, "This is a debug message."); // Not logged
        Logger.LogInformation(3, "This is an information message."); // Logs in Format1
        Logger.LogWarning(5, "This is a warning message."); // Logs in Format2
        Logger.LogError(7, "This is an error message.");  // Logs in Format3
        Logger.LogTrace(5!, "This is a trace message."); // Not logged

        using (Logger.BeginScope("L1"))
        {
            Logger.LogInformation(3, "INFO: ONE scope.");
        }

        using (Logger.BeginScope("L1"))
        {
            using (Logger.BeginScope("L2"))
            {
                Logger.LogInformation(3, "INFO: TWO scopes.");
            }
        }

        using (Logger.BeginScope("L1"))
        {
            using (Logger.BeginScope("L2"))
            {
                using (Logger.BeginScope("L3"))
                {
                    Logger.LogInformation(3, "INFO: THREE scopes.");
                }
            }
        }
    }
}
```

## Markup String
```razor
@page "/markup-string-example"

@((MarkupString)myMarkup)

@code {
    private string myMarkup =
        "<p class=\"text-danger\">This is a dangerous <em>markup string</em>.</p>";
}
```

## Parameter Child
```razor
@page "/parameter-parent-2"

<ParameterChild Title="@title" />

<ParameterChild Title="@GetTitle()" />

<ParameterChild Title="@DateTime.Now.ToLongDateString()" />

<ParameterChild Title="@panelData.Title" />

@code {
    private string title = "From Parent field";
    private PanelData panelData = new();

    private string GetTitle()
    {
        return "From Parent method";
    }

    private class PanelData
    {
        public string Title { get; set; } = "From Parent object";
    }
}
```

## Render Fragment Child
```razor
@page "/render-fragment-parent"

<h1>Render child content</h1>

<RenderFragmentChild>
    Content of the child component is supplied
    by the parent component.
</RenderFragmentChild>
```
## Render Named Tuple Parent
```razor
@page "/render-named-tuple-parent"

<h1>Render Named Tuple Parent</h1>

<RenderNamedTupleChild Data="@data" />

@code {
    private (int TheInteger, string TheString, bool TheBoolean) data = 
        new(999, "I aim to misbehave.", true);
}
```

```razor
@page "/render-tuple-parent"

<h1>Render Tuple Parent</h1>

<RenderTupleChild Data="@data" />

@code {
    private (int, string, bool) data = new(999, "I aim to misbehave.", true);
}
```

## Image Stream
```razor
@page "/show-image-2"
@inject HttpClient Http
@inject IJSRuntime JS

<h1>Stream Image Data Example</h1>

<p>
    <img id="image" />
</p>

<button @onclick="SetImageAsync">
    Set Image
</button>

@code {
    private async Task<Stream> GetImageStreamAsync()
    {
        return await Http.GetStreamAsync(
            "https://avatars.githubusercontent.com/u/9141961");
    }

    private async Task SetImageAsync()
    {
        var imageStream = await GetImageStreamAsync();
        var dotnetImageStream = new DotNetStreamReference(imageStream);
        await JS.InvokeVoidAsync("setImage", "image", dotnetImageStream);
    }
}
```

## Form Example
```razor
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
        &copy;1966-2019 CBS Studios, Inc. and 
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

```cs
editContext.SetFieldCssClassProvider(new CustomFieldClassProvider());
```

## Partial Class
Partial Classes and Methods
[Partial Classes and Methods - C# Programming Guide | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)

```cs
namespace BlazorSample.Pages.index;

public partial class CounterPartialClass
{
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

## Cascading Value
In simple terms, cascading values and parameters are a way to pass data from a parent component to its child components in Blazor. This allows you to share data between components without having to manually pass it down through each level of the component hierarchy. Instead, you can define a cascading value or parameter in an ancestor component, and any descendent component can access that value without having to receive it as a parameter. This can make it easier to manage and share data between components in a complex application.

```razor
<main>
    <div class="top-row px-4">
        <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
    </div>

    <CascadingValue Value="@theme">
        <article class="content px-4">
            @Body
        </article>
    </CascadingValue>
    <button @onclick="ChangeToDarkTheme">Dark mode</button>
</main>

@code {
    private ThemeInfo theme = new() { ButtonClass = "btn-success" };

    private void ChangeToDarkTheme()
    {
        theme = new() { ButtonClass = "btn-darkmode-success" };
    }
}
```

```razor
@using BlazorSample.UIInterfaces
@implements ITab

<li>
    <a @onclick="ActivateTab" class="nav-link @TitleCssClass" role="button">
        @Title
    </a>
</li>

@code {
    [CascadingParameter]
    public TabSet? ContainerTabSet { get; set; }

    [Parameter]
    public string? Title { get; set; }

    [Parameter]
    public RenderFragment? ChildContent { get; set; }

    private string? TitleCssClass => 
        ContainerTabSet?.ActiveTab == this ? "active" : null;

    protected override void OnInitialized()
    {
        ContainerTabSet?.AddTab(this);
    }

    private void ActivateTab()
    {
        ContainerTabSet?.SetActiveTab(this);
    }
}
```
 
## Read Environment
```razor
@page "/read-environment"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

## Event Arguments
- Focus events: **FocusEventArgs**
- Mouse events: **MouseEventArgs**
- Drag events: **DragEventArgs**
- Keyboard events: **KeyboardEventArgs**
- Input events: **ChangeEventArgs**/**EventArgs**
- Clipboard events: **ClipboardEventArgs**/**EventArgs**
- Touch events: **TouchEventArgs**
- Pointer events: **PointerEventArgs**
- Media events: **EventArgs**
- Progress events: **ProgressEventArgs**

## Drag and Drop
[HTML Drag and Drop API](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API)

## Notification
[ReceiveNotifications.razor at blazor-samples](https://github.com/dotnet/blazor-samples/blob/main/7.0/BlazorSample_WebAssembly/Pages/synchronization-context/ReceiveNotifications.razor)
`#region` and `#endregion` are preprocessor directives in C# that allow you to define a block of code that can be collapsed or expanded in the code editor. This can be useful for organizing your code and making it easier to read and navigate.

In the code you provided, `#region snippet1` and `#endregion` define a region named `snippet1` that contains the code between these two directives. In a code editor that supports code folding, you can collapse this region to hide the code within it, or expand it to show the code.

## Background work
```cs
@page "/background-work"
@using System.Threading
@using Microsoft.Extensions.Logging
@implements IDisposable
@inject ILogger<BackgroundWork> Logger

<button @onclick="LongRunningWork">Trigger long running work</button>
<button @onclick="Dispose">Trigger Disposal</button>

@code {
    private Resource resource = new();
    private CancellationTokenSource cts = new();

    protected async Task LongRunningWork()
    {
        Logger.LogInformation("Long running work started");

        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod(Logger);
    }

    public void Dispose()
    {
        Logger.LogInformation("Executing Dispose");
        cts.Cancel();
        cts.Dispose();
        resource?.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod(ILogger<BackgroundWork> logger)
        {
            logger.LogInformation("BackgroundResourceMethod: Start method");

            if (disposed)
            {
                logger.LogInformation("BackgroundResourceMethod: Disposed");
                throw new ObjectDisposedException(nameof(Resource));
            }

            // Take action on the Resource

            logger.LogInformation("BackgroundResourceMethod: Action on Resource");
        }

        public void Dispose()
        {
            disposed = true;
        }
    }
}
```

This code defines a Blazor component that allows the user to trigger a long-running background task or dispose of the component’s resources. The component uses several `@using` directives to import namespaces, an `@implements` directive to indicate that the component implements the `IDisposable` interface, and an `@inject` directive to inject an `ILogger<BackgroundWork>` instance into the component.

The component’s markup defines two buttons, one for triggering the long-running background task and one for disposing of the component’s resources. The `@onclick` directive is used to bind the `click` event of each button to the appropriate method in the component’s code.

In the `@code` block, the component defines several private fields, including a `Resource` object, a `CancellationTokenSource` object, and two methods: `LongRunningWork` and `Dispose`. The `LongRunningWork` method is an asynchronous method that simulates a long-running background task by using the `Task.Delay` method to introduce a delay of 5 seconds. The method also checks if the task has been cancelled by calling the `ThrowIfCancellationRequested` method on the cancellation token, and calls a method on the `Resource` object.

The `Dispose` method is used to dispose of the component’s resources when it is no longer needed. It cancels any ongoing background tasks by calling the `Cancel` method on the cancellation token source, disposes of the cancellation token source and the `Resource` object, and logs a message to indicate that disposal is complete.

The component also defines a private nested class named `Resource`, which implements the `IDisposable` interface. This class represents a resource that is used by the component and needs to be disposed of when the component is no longer needed. The class defines a `BackgroundResourceMethod` method that simulates some action being taken on the resource, and a `Dispose` method that sets a flag to indicate that the resource has been disposed.


mapboxgl

```js
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

mapboxgl.accessToken = 'pk.eyJ1IjoiZ3VhcmRyZXgiLCJhIjoiY2tvZnBkZmlqMGtyZTJ3bnJvdjJ0bWNhNiJ9.zvSwQMBflS5EjgC3dp4cyg';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```