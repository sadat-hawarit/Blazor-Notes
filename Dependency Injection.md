Dependency injection (DI) is a technique for achieving loose coupling between objects and their collaborators, or dependencies. Most often, classes will declare their dependencies via their constructor, allowing them to follow the Explicit Dependencies Principle. This approach is known as "constructor injection".

To implement dependency injection, we need to configure a DI container with classes that is participating in DI. DI Container has to decide whether to return a new instance of the service or provide an existing instance. In startup class, we perform this activity on ConfigureServices method.

The lifetime of the service depends on when the dependency is instantiated and how long it lives. And lifetime depends on how we have registered those services.

The below three methods define the lifetime of the services,

1. _AddTransient_  
    Transient lifetime services are created each time they are requested. This lifetime works best for lightweight, stateless services.  
     
2. _AddScoped_  
    Scoped lifetime services are created once per request.  
     
3. _AddSingleton_  
    Singleton lifetime services are created the first time they are requested (or when ConfigureServices is run if you specify an instance there) and then every subsequent request will use the same instance.
Transient service always returns a new instance even though it’s the same request, that is why operation Ids are different for first instance and second instance for both the requests (Request 1 and Request 2).

In the case of Scoped service, a single instance is created per request and the same instance is shared across the request. That is why operation Ids are the same for first instance as well as second instance of Request 1. But if we click on refresh button or load the UI on different tab of a browser (which is nothing but Request 2), new ids are generated.

In the case of Singleton service, only one instance is created and shared across applications. If we click on refresh button or load the UI on a different tab of a browser (which is nothing but Request 2), those ids will remain the same.

|   |   |   |
|---|---|---|
|Service Type|In the scope of a given HTTP request|Across different HTTP requests|
|**Transient**|New Instance|New Instance|
|**Scoped**|Same Instance|New Instance|
|**Singleton**|Same Instance|Same Instance|
![[Pasted image 20230807095020.png]]
![[Pasted image 20230807095027.png]]
