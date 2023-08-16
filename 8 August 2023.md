In **WebAssembly**,
AddTransient
<del>AddScoped</del>->AddSingleton
AddSingleton

### How to configure LocalStorage or SessionStorage with  in Memory State Container Service?
-> There is no built-in way to do this. The official documentation itself suggests third party Nuget packages. I found Blazored Localstorage.
in Program.cs
```cs
builder.Services.AddBlazoredLocalStorageAsSingleton();
```
Asynchronus:
```cs
@inject Blazored.LocalStorage.ILocalStorageService localStorage

@code {

    protected override async Task OnInitializedAsync()
    {
        await localStorage.SetItemAsync("name", "John Smith");
        var name = await localStorage.GetItemAsync<string>("name");
    }

}
```
Synchronus:
```cs
@inject Blazored.LocalStorage.ISyncLocalStorageService localStorage

@code {

    protected override void OnInitialized()
    {
        localStorage.SetItem("name", "John Smith");
        var name = localStorage.GetItem<string>("name");
    }

}
```

### How to send an event from server which blazor can subscribe to?
-> 
**SignalIR** or **System.Net.WebSockets** to push real time updates from server.
SignalIR is the recommended way by the docs. WebSockets can be used to have more control but need more effort than high level SignalIR.

**EventHandler** just works with client-side event updates
Define a service containing and `EventHandler <TReturn>`. 

```cs
public class MyService : IMyService{
  public event EventHandler<string> OnDataUpdated;
  private string _data;
  public void UpdateDate(string newData){
    this._data = newData;
    this.OnDataUpdated?.Invoke(this,newData);
  }
}
```
Inject the service to subscribe it.
```cs
@implements IDisposable
@inject IMyService myService;

@lastUpdate

@functions{
    DateTime lastUpdate;
    protected override void OnInit()
    {
        myService.OnDataUpdated += Handle;
        base.OnInit();
    }
    protected void Handle(object sender, string args)
    {
       lastUpdate = DateTime.Now;        
    }
    public void Dispose()
    {
        myService.OnDataUpdated -= Handle;
    }
}
```

### Component LifeCycle
![[Pasted image 20230808094214.png]]
`SetParametersAsync` is called when a component is first initialized and whenever new parameter values are provided by the parent component.
```cs
public override async Task SetParametersAsync(ParameterView parameters){}
```
`OnInitialized` is a lifecycle method in Blazor components that is called after the initial setting of the component’s parameters. It is called when a component is first initialized and is useful for performing one-time setup for the component
```cs
// Synchronus
protected override void OnInitialized(){}
// Asynchronus
protected override async Task OnInitializedAsync() { await ... }
```
`OnParametersSet` is useful for performing additional operations when the component’s parameters change. For example, you could use these methods to update the component’s state or to retrieve data from a service based on the new parameter values.
```cs
protected override void OnParametersSet(){} 
protected override async Task OnParametersSetAsync(){await ...}
```
`OnAfterRender` is cal.led synchronously after the component has finished rendering. It is useful for performing additional operations after the component has finished rendering.For example, you could use these methods to interact with the DOM or to initialize JavaScript libraries that require the component to be fully rendered before they can be used.
```cs
protected override void OnAfterRender(bool firstRender){}
protected override async Task OnAfterRenderAsync(bool firstRender){await ...}
```
[StateHasChanged](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.components.componentbase.statehaschanged) notifies the component that its state has changed. When applicable, calling StateHasChanged causes the component to be rerendered.
StateHasChanged is called automatically for EventCallback methods.

IDisposable or IAsyncDisposable to implement Dispose() method

### Virtualization

Virtualization is a technique for limiting UI rendering to just the parts that are currently visible. For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.
Instead of 
```cs
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```
we use 
```cs
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```
A detailed example:
```cs
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```
### Lazy Loading
Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item. Use the assembly name with the `.dll` extension. The Blazor framework prevents the assembly from loading at app launch.
```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="{ASSEMBLY NAME}.dll" />
</ItemGroup>
```
Configure router in App.razor
```cs
<Router AppAssembly="@typeof(App).Assembly" AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync"> 
	<Navigating> 
		<div style="padding:20px;background-color:blue;color:white"> 
			<p>Loading the requested page&hellip;</p> 
		</div> 
	</Navigating> 
	<Found Context="routeData"> 
		<RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" /> 
	</Found> 
	<NotFound> 
		<LayoutView Layout="@typeof(MainLayout)"> 
			<p>Sorry, theres nothing at this address.</p> 
		</LayoutView> 
	</NotFound> 
</Router>
```
