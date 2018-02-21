# Scope

## Scope builder

Computation expression used to creating routing and combining `HttpHandlers`, `pipelines` and `controllers` together.

Result of the computation expression is standard Giraffe's `HttpHandler`which means that it's easily composable with other parts of the ecosytem.

**Example:**

```fsharp
let topRouter = scope {
    pipe_through headerPipe
    not_found_handler (text "404")

    get "/" helloWorld
    get "/a" helloWorld2
    getf "/name/%s" helloWorldName
    getf "/name/%s/%i" helloWorldNameAge

    //scopes can be defined inline to simulate `subRoute` combinator
    forward "/other" (scope {
        pipe_through otherHeaderPipe
        not_found_handler (text "Other 404")

        get "/" otherHelloWorld
        get "/a" otherHelloWorld2
    })

    // or can be defined separatly and used as HttpHandler
    forward "/api" apiRouter

    // same with controllers
    forward "/users" userController
}
```

---

### get

Adds handler for `GET` request.

**Input:**: `string * HttpHandler`

### getf

Adds handler for `GET` request using formater.

**Input:**: `PrintfFormat<_,_,_,_'f> * ('f -> HttpHandler)`

### post

Adds handler for `POST` request.

**Input:**: `string * HttpHandler`

### postf

Adds handler for `POST` request using formater.

**Input:**: `PrintfFormat<_,_,_,_'f> * ('f -> HttpHandler)`

### put

Adds handler for `PUT` request.

**Input:**: `string * HttpHandler`

### putf

Adds handler for `PUT` request using formater.

**Input:**: `PrintfFormat<_,_,_,_'f> * ('f -> HttpHandler)`

### delete

Adds handler for `DELETE` request.

**Input:**: `string * HttpHandler`

### deletef

Adds handler for `DELETE` request using formater.

**Input:**: `PrintfFormat<_,_,_,_'f> * ('f -> HttpHandler)`

### patch

Adds handler for `PATCH` request.

**Input:**: `string * HttpHandler`

### patchf

Adds handler for `PATCH` request using formater.

**Input:**: `PrintfFormat<_,_,_,_'f> * ('f -> HttpHandler)`

### forward

Forwards calls to different `scope`. Modifies the `HttpRequest.Path` to allow subrouting.

**Input:**: `string * HttpHandler`

### pipe_through

Adds pipeline to the list of pipelines that will be used for every request

**Input:**: `HttpHandler`

### not_found_handler

Adds not-found handler for current scope

**Input:**: `HttpHandler`