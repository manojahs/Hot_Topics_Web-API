# Hot_Topics_Web-API
----------------------------
```

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












```


