### Blazor WebAssembly vs Blazor Server
With Blazor WebAssembly, developers can run .NET code in a browser. It's a single-page app framework that uses the WebAssembly open standards without requiring plug-ins or code generation. A Blazor WebAssembly app is restricted to the capabilities of the browser that executes the app, but the app can access full browser functionality via JavaScript interop.
Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app. UI updates are handled over a SignalR connection. The connection Blazor Server uses to communicate with the browser is also used to handle JavaScript interop calls.