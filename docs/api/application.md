# Application

## Application builder

Computation expression used to configure Saturn application. Under the hood it's using ASP.NET application configurations interfaces such as `IWebHostBuilder`, `IServiceCollection`, `IApplicationBuilder` and others. It aims to hide cumbersome ASP.NET application configuration and enable high level, declarative application configuration using feature toggles

### router


Defines top-level router used for the application. It's calling `IApplicationBuilder.UseGiraffe`

**Input:** `HttpHandler`

```fsharp
application {
    ...
    router myRouter
}
```

### pipe_through

Adds pipeline to the list of pipelines that will be used for every request

**Input:** `HttpHandler`

```fsharp
application {
    ...
    pipe_through requestId
}
```

### error_handler

Adds global error handler for exceptions not handled anywhere else. It's using `IApplicationBuilder.UseGiraffeErrorHandler`

**Input:** `Exception -> ILogger -> HttpHandler`

```fsharp
application {
    ...
    error_handler (fun e log -> text e.Message)
}
```

### url

Defines URL on which application will be hosted. Should include port.

**Input:** `string`

```fsharp
application {
    ...
    url "http://0.0.0.0:8085/"
}
```

### memory_cache

Enables in-memory session cache. Required if you used `fetchSession` plug.

```fsharp
application {
    ...
    memory_cache
}
```

### use_gzip

Enables automatic gzip compression

```fsharp
application {
    ...
    use_gzip
}
```

### use_static

Enables using static file hosting. Input path defines `WebRoot` and `ContentRoot` of application

**Input:** `string`

```fsharp
application {
    ...
    use_static "static"
}
```

### use_config

Defines configuration that can be used with `HttpContext.GetConfiguration ()` function. Configuration function is evaluated once, during first request of the application.

**Input:** `unit -> 'a`

```fsharp
application {
    ...
    use_config (fun _ -> "config")
}
```

### force_ssl

Redirect all HTTP request to HTTPS

```fsharp
application {
    ...
    force_ssl
}
```

### use_cors

Enables application level CORS protection. First parameter is name of the policy. Second parameter is configuration builder setting policy options.

**Input:** `string -> (CorsPolicyBuilder -> unit)`

```fsharp
application {
    ...
    use_cors "CORS_policy" (fun builder -> ())
}
```

### use_iis

Enables IIS integration

```fsharp
application {
    ...
    use_iis
}
```

---

### use_jwt_authentication

Enables default JWT authentication. First parameter is private key used to signing. Second one defines issuer of the token.

**Input:** `string -> string`

```fsharp
application {
    ...
    use_jwt_authentication "mySecretKey" "lambdafactory.io"
}
```

### use_jwt_authentication_with_config

Enables JWT authentication with custom configuration

**Input:** `JwtBearerOptions -> unit`

```fsharp
application {
    ...
    use_jwt_authentication_with_config (fun opts -> ())
}
```

### use_cookies_authentication

Enables default cookies authentication

**Input:** `string`

```fsharp
application {
    ...
    use_cookies_authentication "lambdafactory.io"
}
```

### use_cookies_authentication_with_config

Enables cookies authentication with custom configuration

**Input:** `CookieAuthenticationOptions -> unit`

```fsharp
application {
    ...
    use_cookies_authentication_with_config (fun opts -> ())
}
```

### use_github_oauth

Enables default GitHub OAuth authentication

**Input:** `string -> string -> string`

```fsharp
application {
    ...
    use_github_oauth "myClientId" "myClientSecret" "/login"
}
```

### use_github_oauth_with_config

Enables GitHub OAuth authentication with custom configuration

**Input:** `OAuthOptions -> unit`

```fsharp
application {
    ...
    use_github_oauth (fun opts -> ())
}
```

### use_custom_oauth

Enables custom OAuth authentication

**Input:** `string -> (OAuthOptions -> unit)`

```fsharp
application {
    ...
    use_custom_oauth "LinkedIn" (fun opts -> ())
}
```

---

> Functions below enables you to add any configuration with using standard ASP.NET builders

### app_config

Adds custom application configuration step.

**Input:** `IApplicationBuilder -> IApplicationBuilder`

### host_config

Adds custom host configuration step.

**Input:** `IWebHostBuilder -> IWebHostBuilder`

### service_config

Adds custom service configuration step.

**Input:** `IServiceCollection -> IServiceCollection`

### logging

Adds logging configuration

**Input:** `IloggingBuilder -> unit`