A Blazor app can invoke JavaScript (JS) functions from .NET methods and .NET methods from JS functions. These scenarios are called _JavaScript interoperability_ (_JS interop_).
# JS from .NET
[IJSRuntime](https://learn.microsoft.com/en-us/dotnet/api/microsoft.jsinterop.ijsruntime) is registered by the Blazor framework. To call into JS from .NET, inject the [IJSRuntime](https://learn.microsoft.com/en-us/dotnet/api/microsoft.jsinterop.ijsruntime) abstraction and call one of the following methods:

- [IJSRuntime.InvokeAsync](https://learn.microsoft.com/en-us/dotnet/api/microsoft.jsinterop.ijsruntime.invokeasync)
- [JSRuntimeExtensions.InvokeAsync](https://learn.microsoft.com/en-us/dotnet/api/microsoft.jsinterop.jsruntimeextensions.invokeasync)
- [JSRuntimeExtensions.InvokeVoidAsync](https://learn.microsoft.com/en-us/dotnet/api/microsoft.jsinterop.jsruntimeextensions.invokevoidasync)

For the preceding .NET methods that invoke JS functions:

- The function identifier (`String`) is relative to the global scope (`window`). To call `window.someScope.someFunction`, the identifier is `someScope.someFunction`. There's no need to register the function before it's called.
- Pass any number of JSON-serializable arguments in `Object[]` to a JS function.
- The cancellation token (`CancellationToken`) propagates a notification that operations should be canceled.
- `TimeSpan` represents a time limit for a JS operation.
- The `TValue` return type must also be JSON serializable. `TValue` should match the .NET type that best maps to the JSON type returned.
- A [JS `Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) is returned for `InvokeAsync` methods. `InvokeAsync` unwraps the [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) and returns the value awaited by the [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).

Load JavaScript (JS) code using any of the following approaches:

- [Load a script in `<head>` markup](https://learn.microsoft.com/en-us/aspnet/core/blazor/javascript-interoperability/?view=aspnetcore-7.0#load-a-script-in-head-markup) (_Not generally recommended_)
- [Load a script in `<body>` markup](https://learn.microsoft.com/en-us/aspnet/core/blazor/javascript-interoperability/?view=aspnetcore-7.0#load-a-script-in-body-markup)
- [Load a script from an external JavaScript file (`.js`) collocated with a component](https://learn.microsoft.com/en-us/aspnet/core/blazor/javascript-interoperability/?view=aspnetcore-7.0#load-a-script-from-an-external-javascript-file-js-collocated-with-a-component)
- [Load a script from an external JavaScript file (`.js`)](https://learn.microsoft.com/en-us/aspnet/core/blazor/javascript-interoperability/?view=aspnetcore-7.0#load-a-script-from-an-external-javascript-file-js)
- [Inject a script before or after Blazor starts](https://learn.microsoft.com/en-us/aspnet/core/blazor/javascript-interoperability/?view=aspnetcore-7.0#inject-a-script-before-or-after-blazor-starts)
JavaScript (JS) initializers execute logic before and after a Blazor app loads. JS initializers are useful in the following scenarios:

- Customizing how a Blazor app loads.
- Initializing libraries before Blazor starts up.
- Configuring Blazor settings.

```razor
@page "/call-js-example-1"
@inject IJSRuntime JS

<h1>Call JS <code>convertArray</code> Function</h1>

<p>
    <button @onclick="ConvertArray">Convert Array</button>
</p>

<p>
    @text
</p>

<p>
    Quote ©2005 <a href="https://www.uphe.com">Universal Pictures</a>: 
    <a href="https://www.uphe.com/movies/serenity-2005">Serenity</a><br>
    <a href="https://www.imdb.com/name/nm0472710/">David Krumholtz on IMDB</a>
</p>

@code {
    private MarkupString text;

    private uint[] quoteArray = 
        new uint[]
        {
            60, 101, 109, 62, 67, 97, 110, 39, 116, 32, 115, 116, 111, 112, 32,
            116, 104, 101, 32, 115, 105, 103, 110, 97, 108, 44, 32, 77, 97,
            108, 46, 60, 47, 101, 109, 62, 32, 45, 32, 77, 114, 46, 32, 85, 110,
            105, 118, 101, 114, 115, 101, 10, 10,
        };

    private async Task ConvertArray()
    {
        text = new(await JS.InvokeAsync<string>("convertArray", quoteArray));
    }
}

```
# .NET from JS