The _AppState pattern_ adds an object to the DI container that you will use to coordinate state between related components. Because the _AppState_ object is managed by the DI container, it can outlive the components and hold on to state even when the UI changes. Another benefit of the _AppState pattern_ is that it leads to greater separation between presentation (components) and business logic.

First we create a class in the Client Project root directory - and register it as a scoped service in the DI container. In Blazor WebAssembly applications, services are registered in the `Program` class. Add the service just before the call to `await builder.Build().RunAsync();`
```cs
builder.Services.AddScoped<OrderState>();
```
