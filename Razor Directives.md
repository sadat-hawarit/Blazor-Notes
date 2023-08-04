Razor directives are component markup used to add C# inline with HTML. With directives, developers can define single statements, methods, or larger code blocks.
### Code directives

You can use ```@()``` to add a C# statement inline with HTML. If you require more code, use the `@code` directive to add multiple statements, enclosed by curly brackets.

You can also add an `@functions` section to the template for methods and properties. They're added to the top of the generated class, where the document can reference them.

### Page directive

The `@Page` directive is special markup that identifies a component as a page. You can use this directive to specify a route. The route maps to an attribute route that the Blazor engine recognizes to register and access the page.

@bind binds data to a variable
@if declares an if block
@foreach declares foreach block

