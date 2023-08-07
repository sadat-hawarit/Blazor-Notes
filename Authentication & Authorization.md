[Authorize] in Controller using Microsoft.AspNetCore.Authorization
The client code needs a way to track whether the user is logged in, and if so _which_ user is logged in, so it can influence how the UI behaves. Blazor has a built-in DI service for doing this: the `AuthenticationStateProvider`. Blazor provides an implementation of the `AuthenticationStateProvider` service and other related services and components based on [OpenID Connect](https://openid.net/connect/) that handle all the details of establishing who the user is. These services and components are provided in the Microsoft.AspNetCore.Components.WebAssembly.Authentication package, which has already been added to the client project for you.
In broad terms, the authentication process implemented by these services looks like this:

- When a user attempts to login or tries to access a protected resource, the user is redirected to the app's login page (`/authentication/login`).
- In the login page, the app prepares to redirect to the authorization endpoint of the configured identity provider. The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response. The app provides a login callback to receive the authentication response.
    - If the user isn't authenticated, the user is first redirected to the underlying authentication system (typically ASP.NET Core Identity).
    - Once the user is authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).
- When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.
    - If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.
    - If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.

See also [Secure ASP.NET Core Blazor WebAssembly](https://docs.microsoft.com/aspnet/core/security/blazor/webassembly/) for additional details.

In Program.cs
```cs 
// Add auth services
builder.Services.AddApiAuthorization();
```

By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library uses the routes shown in the following table for representing different authentication states.

|Route|Purpose|
|---|---|
|`authentication/login`|Triggers a sign-in operation.|
|`authentication/login-callback`|Handles the result of any sign-in operation.|
|`authentication/login-failed`|Displays error messages when the sign-in operation fails for some reason.|
|`authentication/logout`|Triggers a sign-out operation.|
|`authentication/logout-callback`|Handles the result of a sign-out operation.|
|`authentication/logout-failed`|Displays error messages when the sign-out operation fails for some reason.|
|`authentication/logged-out`|Indicates that the user has successfully logout.|
|`authentication/profile`|Triggers an operation to edit the user profile.|
|`authentication/register`|Triggers an operation to register a new user.|

```cs
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string? Action { get; set; }
}
```
`RemoteAuthenticationState` is used by the authentication system to preserve state across the redirects, like the return URL. When you derive from this type, any public properties will be JSON serialized as part of the persisted state.