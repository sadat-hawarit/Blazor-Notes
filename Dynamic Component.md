## `<DynamicComponent>`
```cs
<DynamicComponent 
	Type="@componentType" 
	Parameters="@parameters" /> 
@code 
{ 
	private Type componentType = ...; 
	private IDictionary<string, object> parameters = ...; 
}
```
---

## Use the [Instance](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.components.dynamiccomponent.instance#microsoft-aspnetcore-components-dynamiccomponent-instance) property
```cs
<DynamicComponent 
Type="@typeof({COMPONENT})" 
@ref="dc" />

<button @onclick="Refresh">
	Refresh
</button>

@code {
    private DynamicComponent? dc;
    private Task Refresh()
    {
        return (dc?.Instance as IRefreshable)?.Refresh();
    }
}
```
