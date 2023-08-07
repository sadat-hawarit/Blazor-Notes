
Routing & Navigation: NavigationManager

Layout & Components

Directives: if, foreach, for, code, page, inject, implements, bind, lambda functions
Data Binding, Event Binding

Dependency Injection: inject, scoped service or singleton service: class

JavaScript Interoperability: IJSRuntime, InvokeAsync<>, InvokeVoidAsync

Task, ValueTask: Performance improves by avoiding unnecessary Task object process and memory utilization by avoiding unnecessary creation of objects on Heap. Limitation: Multiple or Concurrent cant be done.

Client Side form validation
Server Side form validation: DataAnnotation 
Required, MaxLength, MinLength, StringLength:Minimum,Maximum, Compare, DataType, RegularExpression, Range, CustomValidation ,ErrorMessage


State Management:
Ways:
1. Server Side Storage
2. URL
3. Browser Storage: Local/Session Storage
4. In Memory State Container Service. AddScoped/AddSingleton (AppState Pattern)
//TODO how to configure session/local: 3rd party nuget package

Dependency Injection:
- AddTransient: Every Request
- AddScoped: Every HTTP
- AddSingleton: 

PollForUpdates
//TODO: Notify from server
Implement Dispose

Parameter & CascadingParameter