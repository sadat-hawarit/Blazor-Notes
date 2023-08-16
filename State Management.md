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

1. **Create a Shared State Library:**
    
    Create a new class library project in your preferred development environment (Visual Studio, Visual Studio Code, etc.).
    
2. **Define the Shared State:**
    
    In the shared state library, define your in-memory state container. This could be a class that holds the state you want to share. For example:
    
    ```csharp
    public class SharedAppState
    {
        public string SharedValue { get; set; }
    }
    ```
    
3. **Install the Shared State Library:**
    
    Build and package your shared state library as a NuGet package, or if the projects are closely related, you can reference the library directly.
    
4. **Reference the Shared State Library:**
    
    In both of your Blazor projects, add a reference to the shared state library. You can do this by adding the shared library's NuGet package or by directly referencing the project if it's in the same solution.
    
5. **Use the Shared State:**
    
    In your Blazor components, you can inject the shared state container using dependency injection. For example, in your component code:
    
    ```csharp
    @page "/page1"
    
    @inject SharedAppState SharedState
    
    <h3>Page 1</h3>
    <p>Shared Value: @SharedState.SharedValue</p>
    ```
    
    And in another component:
    
    ```csharp
    @page "/page2"
    
    @inject SharedAppState SharedState
    
    <h3>Page 2</h3>
    <p>Shared Value: @SharedState.SharedValue</p>
    ```
    
6. **Updating the Shared State:**
    
    To update the shared state, you can inject the `SharedAppState` instance into the appropriate parts of your application and modify the properties as needed. Changes made to the shared state in one component will be reflected in other components that are also using the same instance of the shared state.
    

Remember to manage any synchronization issues that may arise when multiple components in different projects are simultaneously updating the shared state.