# Hot_Topics_Web-API
----------------------------
```
Folder structure
------------------
Controller
Appsettings.json
LaunchSettings.json
Program.cs
WeatherForecast.cs

⚙️ 1. appsettings.json
📘 Purpose
Holds application configuration data — like connection strings, API keys, logging settings, and custom configuration values your code uses at runtime.

{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=MyAppDB;Trusted_Connection=True;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning"
    }
  },
  "Jwt": {
    "Key": "MySecretKey",
    "Issuer": "myapp.com"
  }
}

🚀 2. launchSettings.json

Used only by Visual Studio / dotnet run during development to configure how the app starts — like which environment, port number, or URL to launch.

{
  "profiles": {
    "MyApp": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": true,
      "launchUrl": "swagger",
      "applicationUrl": "https://localhost:5001;http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}







| Method                | Purpose                                               |
| --------------------- | ----------------------------------------------------- |
| `ConfigureServices()` | Register services (DI container)                      |
| `Configure()`         | Define middleware pipeline for handling HTTP requests |





| Feature                | Stateless Protocol         | Stateful Protocol             |
| ---------------------- | -------------------------- | ----------------------------- |
| **Session memory**     | None (each request is new) | Maintains session info        |
| **Request dependency** | Independent requests       | Requests depend on previous   |
| **Server complexity**  | Low                        | High (tracks session/state)   |
| **Performance**        | Typically faster           | Can be slower (more overhead) |
| **Scalability**        | High                       | Lower (more memory required)  |
| **Failure handling**   | Easier (reconnect & retry) | Harder (state might be lost)  |


| Feature                  | JWT (Token-Based)                                                 | Session (Cookie-Based)                                   |
| ------------------------ | ----------------------------------------------------------------- | -------------------------------------------------------- |
| **Storage (Server)**     | Stateless: No data stored on server                               | Stateful: Session data stored on server                  |
| **Storage (Client)**     | Token stored in browser (localStorage, sessionStorage, or cookie) | Session ID stored in cookie                              |
| **Scalability**          | Highly scalable                                                   | Less scalable (requires sticky sessions or shared cache) |
| **Token Content**        | Self-contained (includes user data)                               | Only a reference (session ID)                            |
| **Authentication Check** | Verifies JWT signature                                            | Looks up session data in server store                    |
| **Revocation**           | Harder (requires blacklist)                                       | Easy (delete session from server)                        |
| **Used in**              | APIs, SPAs, mobile apps                                           | Traditional websites                                     |


https://chatgpt.com/c/681a3186-59b0-8013-ac96-6265eeed7028

Explain JWT and what it contains
----------------------------------

header.payload.signature

header
-------
Tells what algorithm is used to sign the token and its type.

{
  "alg": "HS256",     // HMAC SHA-256
  "typ": "JWT"        // Token type
}

Payload
--------
Contains the actual claims (user information and metadata). Common fields include:

| Claim        | Description                            |
| ------------ | -------------------------------------- |
| `sub`        | Subject (usually user ID)              |
| `name`       | User's name                            |
| `iat`        | Issued At (Unix timestamp)             |
| `exp`        | Expiration Time (Unix timestamp)       |
| `role`       | User roles (optional)                  |
| `aud`, `iss` | Audience, Issuer (optional but useful) |

{
  "sub": "1234567890",
  "name": "Alice",
  "role": "Admin",
  "iat": 1715000000,
  "exp": 1715003600
}

Signature
------------

Used to verify that the token hasn't been tampered with.
It holds the secret Key

Purpose:
-------------
Ensures the token came from the trusted server
If even 1 character is changed, the signature won’t match

JWT(json web token) vs OAuth (Open authorization)
---------------------------------------------------

| Feature        | JWT                            | OAuth                                |
| -------------- | ------------------------------ | ------------------------------------ |
| Type           | Token format                   | Authorization framework              |
| Used for       | Authentication & authorization | Authorization (especially delegated) |
| Example        | API login with token           | “Login with Google”                  |
| Controls what? | Identity inside the app        | Which apps can access your data      |
| Provides       | Token containing user claims   | Access token (often a JWT)           |

JWT
---
Primarily used in authentication and authorization. After a user logs in, the server issues a JWT to the client to use in future requests.

OAuth
--------
OAuth is a framework/protocol for delegated authorization — it allows third-party apps to access a user’s data without sharing their password.

Minimal Api
--------------
Minimal APIs are a lightweight way to build HTTP APIs in .NET 6 and later without the overhead of the full MVC (Model-View-Controller) or Razor Pages framework.
Instead of creating controllers, actions, attributes, startup classes, you just map endpoints directly in Program.cs.

app.MapGet("/", () => "Hello, World!");
// Define a parameterized GET endpoint
app.MapGet("/greet/{name}", (string name) => $"Hello, {name}!");

// Define a POST endpoint
app.MapPost("/add", (int a, int b) => a + b);

Middleware
------------
Middleware is a piece of code that sits in the request pipeline.
Each middleware can:

Do something with the incoming request.
Call the next middleware in the pipeline.
Do something with the outgoing response.
It’s like a chain of responsibility.

2 Types of Middlware.
1)Inline Middleware
2)Custom Middleware  

1) Inline Middleware
-----------------------
Defined directly inside Program.cs using app.Use, app.Run,app.next etc.
Good for small logic

app.MapGet("/", () => "Hello World!");
app.Run();
app.UseMapController()  //It will call the controller

2) Custom Middleware
----------------------
Using Invoke or InvokeAsync
You can create custom middleware using RequestDelegate.

public class MyCustomMiddleware
{
    private readonly RequestDelegate _next;

    public MyCustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // Before request
        Console.WriteLine($"Request: {context.Request.Method} {context.Request.Path}");

        await _next(context); // Call the next middleware

        // After response
        Console.WriteLine($"Response Status: {context.Response.StatusCode}");
    }
}

program.cs

builder.UseMiddleware<MyCustomMiddleware>();



app.UseMyCustomMiddleware();

Common Use Cases
--------------------
Logging
Exception handling
Authentication
Response compression
CORS
Custom headers

Handling exception in web api
-----------------------------
1. Try–Catch Inside Controllers (basic)
2. Global Exception Handling Middleware ✅ (Recommended)
Create custom middleware to catch all unhandled exceptions.
3. Use Built-in UseExceptionHandler Middleware
        app.UseExceptionHandler("/error"); // global handler route
4. Using Exception Filters


Q3. What are the different lifetimes in Dependency Injection (DI)

Transient → new instance per request.
Scoped → one instance per HTTP request.
Singleton → one instance for the entire application lifetime.

services.AddTransient<IEmailService, EmailService>();
services.AddScoped<IRepository, Repository>();
services.AddSingleton<ILogger, Logger>();

Dependency Injection (DI) is a design pattern used to achieve loose coupling between classes.
Instead of a class creating its dependencies, they are injected into it from the outside.

// Step 1: Define abstraction
public interface IMessageService
{
    void Send(string message);
}

// Step 2: Implement services
public class EmailService : IMessageService
{
    public void Send(string message) => Console.WriteLine("Email: " + message);
}

public class SmsService : IMessageService
{
    public void Send(string message) => Console.WriteLine("SMS: " + message);
}

// Step 3: Inject dependency via constructor
public class Notification
{
    private readonly IMessageService _messageService;

    public Notification(IMessageService messageService)
    {
        _messageService = messageService;  // injected
    }

    public void Notify(string msg)
    {
        _messageService.Send(msg);
    }
}

builder.Services.AddScoped<IMessageService, EmailService>();

Routing
-------------

1)Convention-based → central route table, default MVC style.

app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
});


2)Attribute Routing → attributes on controllers/actions, flexible.

endpoints.MapControllers();

[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
        return Ok($"Product ID: {id}");
    }
}

3)Endpoint Routing → modern unified system, also used in Minimal APIs.

app.MapGet("/hello/{name}", (string name) => $"Hello {name}");


Migration in dot net core
--------------------------

dotnet ef migrations add AddEmailToUser
dotnet ef database update

Rollback
--------------
dotnet ef database update MigrationName

Rollback everything
----------------
dotnet ef database update 0

IEnumerable or IQuerable Difference
---------------------------------------
IEnumerable is for in-memory collection traversal; it queries all data and then filters in memory.
List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };
IEnumerable<int> query = numbers.Where(x => x > 2);

foreach (var num in query)
{
    Console.WriteLine(num);  // filters in memory
}


IQueryable builds an expression tree and executes queries at the database/server level, so only required data is fetched.

IQueryable<Employee> query = dbContext.Employees.Where(e => e.Salary > 5000);
foreach (var emp in query)
{
    Console.WriteLine(emp.Name);  // filters in SQL, only matching rows come from DB
}

| Feature         | **Synchronous**                           | **Asynchronous**                         |
| --------------- | ----------------------------------------- | ---------------------------------------- |
| **Execution**   | One after another (blocking)              | Can run independently (non-blocking)     |
| **Waiting**     | Caller must wait                          | Caller can continue doing other work     |
| **Return Type** | Normal return value (`void`, `int`, etc.) | `Task`, `Task<T>`, or `ValueTask`        |
| **Use Case**    | CPU-bound, quick operations               | I/O-bound, long-running operations       |
| **Performance** | Can cause UI freezing, slow response      | More responsive apps, better scalability |

Why do we need versioning and how to use it
--------------------------------------------------
1. To Avoid Breaking Existing Clients

When your API is used by multiple clients (mobile apps, web apps, other services, etc.), any change in API structure (like response format, URL, or parameters) can break those clients.

✅ Versioning allows you to release new functionality (like /api/v2/products)
without breaking old clients still using /api/v1/products.

using Microsoft.AspNetCore.Mvc;

namespace MyApi.Controllers.v1
{
    [ApiController]
    [ApiVersion("1.0")]
    [Route("api/v{version:apiVersion}/[controller]")]
    public class ProductsController : ControllerBase
    {
        [HttpGet]
        public IActionResult GetV1()
        {
            return Ok("Response from API version 1.0");
        }
    }
}

using Microsoft.AspNetCore.Mvc;

namespace MyApi.Controllers.v2
{
    [ApiController]
    [ApiVersion("2.0")]
    [Route("api/v{version:apiVersion}/[controller]")]
    public class ProductsController : ControllerBase
    {
        [HttpGet]
        public IActionResult GetV2()
        {
            return Ok("Response from API version 2.0");
        }
    }
}

GET /api/v1/products → returns version 1 data
GET /api/v2/products

Other Ways to Version APIs

URL path versioning (most common)
/api/v1/products

Query string versioning
/api/products?api-version=1.0

Header-based versioning
Set a custom header:
api-version: 1.0



```


