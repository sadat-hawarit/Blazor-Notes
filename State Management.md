The _AppState pattern_ adds an object to the DI container that you will use to coordinate state between related components. Because the _AppState_ object is managed by the DI container, it can outlive the components and hold on to state even when the UI changes. Another benefit of the _AppState pattern_ is that it leads to greater separation between presentation (components) and business logic.

First we create a class in the Client Project root directory - and register it as a scoped service in the DI container. In Blazor WebAssembly applications, services are registered in the `Program` class. Add the service just before the call to `await builder.Build().RunAsync();`
```cs
builder.Services.AddScoped<OrderState>();
```


In Blazor WebAssembly, we need to persist state across browser sessions.

Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.

To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory. State persistence isn't automatic. You must take steps when developing the app to implement stateful data persistence.

Data persistence is typically only required for high-value state that users expended effort to create. In the following examples, persisting state either saves time or aids in commercial activities:

- Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost. A user loses state in this scenario if they navigate away from the form and return later.
- Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained. A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.

An app can only persist _app state_. UIs can't be persisted, such as component instances and their render trees. Components and render trees aren't generally serializable. To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.

Ways:
1. Server Side Storage
2. URL
3. Browser Storage: Local/Session Storage
4. In Memory State Container Service. AddScoped/AddSingleton