### Unhandled exception rendering component: EditForm requires a Model parameter, or an EditContext parameter, but not both.
<InputText @bind-Value="">
the V is upper case.
Also the parent EditForm needs to have either a Model or EditContext property passed down for @bind-Value but not both

### InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.
Confirm that the [EditForm](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.components.forms.editform) assigns a [Model](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.components.forms.editform.model#microsoft-aspnetcore-components-forms-editform-model) **or** an [EditContext](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.components.forms.editform.editcontext#microsoft-aspnetcore-components-forms-editform-editcontext). Don't use both for the same form.

When assigning to [Model](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.components.forms.editform.model#microsoft-aspnetcore-components-forms-editform-model), confirm that the model type is instantiated, as the following example shows:

```cs
private ExampleModel exampleModel = new();
```
### Error: Connection disconnected with error 'Error: Server returned an error on close: Connection closed with an error.' OR System.IO.InvalidDataException: The maximum message size of 32768B was exceeded. The message size can be configured in AddHubOptions.

The maximum incoming SignalR message size permitted for hub methods is limited by the [HubOptions.MaximumReceiveMessageSize](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.signalr.huboptions.maximumreceivemessagesize#microsoft-aspnetcore-signalr-huboptions-maximumreceivemessagesize) (default: 32 KB). SignalR messages larger than [MaximumReceiveMessageSize](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.signalr.huboptions.maximumreceivemessagesize#microsoft-aspnetcore-signalr-huboptions-maximumreceivemessagesize) throw an error. The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.

When SignalR logging isn't set to [Debug](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.logging.loglevel) or [Trace](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.logging.loglevel), a message size error only appears in the browser's developer tools console:

> Error: Connection disconnected with error 'Error: Server returned an error on close: Connection closed with an error.'.

When [SignalR server-side logging](https://learn.microsoft.com/en-us/aspnet/core/signalr/diagnostics?view=aspnetcore-7.0#server-side-logging) is set to [Debug](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.logging.loglevel) or [Trace](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.logging.loglevel), server-side logging surfaces an [InvalidDataException](https://learn.microsoft.com/en-us/dotnet/api/system.io.invaliddataexception) for a message size error.

`appsettings.Development.json`:

JSON

```
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      ...
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

Error:

> System.IO.InvalidDataException: The maximum message size of 32768B was exceeded. The message size can be configured in AddHubOptions.

One approach involves increasing the limit by setting [MaximumReceiveMessageSize](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.signalr.huboptions.maximumreceivemessagesize#microsoft-aspnetcore-signalr-huboptions-maximumreceivemessagesize) in `Program.cs`. The following example sets the maximum receive message size to 64 KB:

C#

```
builder.Services.AddServerSideBlazor()
    .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

Increasing the SignalR incoming message size limit comes at the cost of requiring more server resources, and it increases the risk of [Denial of service (DoS) attacks](https://learn.microsoft.com/en-us/aspnet/core/blazor/security/server/threat-mitigation?view=aspnetcore-7.0#denial-of-service-dos-attacks). Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.

A better option for reading large payloads is to send the content in smaller chunks and process the payload as a [Stream](https://learn.microsoft.com/en-us/dotnet/api/system.io.stream). This can be used when reading large JavaScript (JS) interop JSON payloads or if JS interop data is available as raw bytes. For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the [`InputFile` component](https://learn.microsoft.com/en-us/aspnet/core/blazor/file-uploads?view=aspnetcore-7.0), see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/main/samples/aspnetcore/blazor/BinarySubmit) and the [Blazor `InputLargeTextArea` Component Sample](https://github.com/aspnet/samples/tree/main/samples/aspnetcore/blazor/InputLargeTextArea).


### System.InvalidOperationException: 'The current thread is not associated with the Dispatcher. Use InvokeAsync() to switch execution to the Dispatcher when triggering rendering or component state.'

To solve this we use InvokeAsync() to switch execution to the Dispatcher:
```cs
@page "/counter-state-2"
@using System.Timers
@implements IDisposable

<h1>Counter with <code>Timer</code> disposal</h1>

<p>
    Current count: @currentCount
</p>

@code {
    private int currentCount = 0;
    private Timer timer = new(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }


    private void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    public void Dispose() => timer.Dispose();
}
```
- `OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification. Therefore, `OnTimerCallback` must call [StateHasChanged](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.components.componentbase.statehaschanged) because Blazor isn't aware of the changes to `currentCount` in the callback.
- The component implements [IDisposable](https://learn.microsoft.com/en-us/dotnet/api/system.idisposable), where the [Timer](https://learn.microsoft.com/en-us/dotnet/api/system.timers.timer) is disposed when the framework calls the `Dispose` method.