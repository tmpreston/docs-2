# Controller

### Action

```fsharp
type Action =
    | Index
    | Show
    | Add
    | Edit
    | Create
    | Update
    | Patch
    | Delete
    | DeleteAll
    | All
```

---

## Controller helpers

Module with high level helper functions that are usually used in controller actions.

---

### json

Returns to the client content serialized to JSON.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### xml

Returns to the client content serialized to XML.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### text

Returns to the client content as string.

**Type:** `HttpContext -> string -> HttpFuncResult`

### render

Returns to the client rendered template.

**Type:** `HttpContext -> string -> HttpFuncResult`

### file

Returns to the client a static file.

**Type:** `HttpContext -> string -> HttpFuncResult`

### sendDownload

Sends file with given path.

**Type:** `HttpContext -> string -> HttpFuncResult`

### sendDownloadBinary

Sends file as binary blob.

**Type:** `HttpContext -> byte [] -> HttpFuncResult`

### redirect

Sends redirect response.

**Type:** `HttpContext -> string -> HttpFuncResult`

---

### getJson<'a>

Gets model from body as JSON.

**Type:** `HttpContext -> Task<'a>`

### getXml<'a>

Gets model from body as XML.

**Type:** `HttpContext -> Task<'a>`

### getForm<'a>

Gets model from urelencoded body.

**Type:** `HttpContext -> Task<'a>`

### getQuery<'a>

Gets model from query string.

**Type:** `HttpContext -> Task<'a>`

### getModel<'a>

Get model based on `HttpMethod` and `Content-Type` of request.

**Type:** `HttpContext -> Task<'a>`

### loadModel<'a>

Loads model populated by `fetchModel` pipeline.

**Type:** `HttpContext -> Option<'a>`

### getPath

Gets path of the request relative to the current `router`.

**Type:** `HttpContext -> string`

### getUrl

Gets the request URL.

**Type:** `HttpContext -> string option`

### getConfig<'a>

Gets configuration.

**Type:** `HttpContext -> 'a`

---

## Response helpers

Module with lower level functions for returning certain responses from the action.

---

### continue

Returns `100 Continue`.

**Type:** `HttpContext -> HttpFuncResult`

### switchingProto

Returns `101 Switching Protocols`.

**Type:** `HttpContext -> HttpFuncResult`

---

### ok

Returns `200 OK`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### created

Returns `201 Created`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### accepted

Returns `202 Accepted`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

---

### badRequest

Returns `400 Bad Request`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### unauthorized

Returns `401 Unauthorized`. Requires `scheme` and `relam`.

**Type:** `HttpContext -> string -> string -> 'a -> HttpFuncResult`

### forbidden

Returns `403 Forbidden`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### notFound

Returns `404 Not Found`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### methodNotAllowed

Returns `405 Method Not Allowed`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### notAcceptable

Returns `406 Not Acceptable`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### conflict

Returns `409 Conflict`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### gone

Returns `410 Gone`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### unuspportedMediaType

Returns `415 Unsupported Media Type`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### unprocessableEntity

Returns `422 Unprocessable Entity`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### preconditionRequired

Returns `428 Precondition Required`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### tooManyRequests

Returns `429 Too Many Requests `.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

---

### internalError

Returns `500 Internal Server Error`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### notImplemented

Returns `501 Not Implemented`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### badGateway

Returns `502 Bad Gateway`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### serviceUnavailable

Returns `503 Service Unavailable`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

### gatewayTimeout

Returns `504 Gateway Timeout`.

**Type:** `HttpContext -> 'a -> HttpFuncResult`

---


## Controller builder

Computation expression used to create Saturn controllers - abstraction representing REST-ish endpoint for serving HTML views or returning data. It supports:

* a set of predefined actions that are automatically mapped to the endpoints following standard conventions
* embedding sub-controllers for modeling one-to-many relationships 
* versioning 
* adding plugs for a particular action which in principle provides the same mechanism as attributes in ASP.NET MVC applications
* defining a common error handler for all actions
* defining a not-found action

The result of the computation expression is a standard Giraffe `HttpHandler`, which means that it's easily composable with other parts of the ecosytem.

**Example:**

```fsharp
let commentController userId = controller {
    index (fun ctx -> (sprintf "Comment Index handler for user %i" userId ) |> Controller.text ctx)
    add (fun ctx -> (sprintf "Comment Add handler for user %i" userId ) |> Controller.text ctx)
    show (fun (ctx, id) -> (sprintf "Show comment %s handler for user %i" id userId ) |> Controller.text ctx)
    edit (fun (ctx, id) -> (sprintf "Edit comment %s handler for user %i" id userId )  |> Controller.text ctx)
}

let userControllerVersion1 = controller {
    version 1
    subController "/comments" commentController

    index (fun ctx -> "Index handler version 1" |> Controller.text ctx)
    add (fun ctx -> "Add handler version 1" |> Controller.text ctx)
    show (fun (ctx, id) -> (sprintf "Show handler version 1 - %i" id) |> Controller.text ctx)
    edit (fun (ctx, id) -> (sprintf "Edit handler version 1 - %i" id) |> Controller.text ctx)
}

let userController = controller {
    subController "/comments" commentController

    plug [All] (setHttpHeader "user-controller-common" "123")
    plug [Index; Show] (setHttpHeader "user-controller-specialized" "123")

    index (fun ctx -> "Index handler no version" |> Controller.text ctx)
    add (fun ctx -> "Add handler no version" |> Controller.text ctx)
    show (fun (ctx, id) -> (sprintf "Show handler no version - %i" id) |> Controller.text ctx)
    edit (fun (ctx, id) -> (sprintf "Edit handler no version - %i" id) |> Controller.text ctx)
}
```

---

### index

Operation that should render (or return in case of API controllers) a list of data.

Mapped to `GET "/"` endpoint

**Input:** `HttpContext -> HttpFuncResult`

### show

Operation that should render (or return in case of API controllers) a single entry of data.

Mapped to `GET "/:id"` endpoint

**Input:** `HttpContext * 'Key -> HttpFuncResult`

### add

Operation that should render a form for adding a new item.

Mapped to `GET "/add"` endpoint

**Input:** `HttpContext -> HttpFuncResult`

### edit

Operation that should render a form for editing an existing item.

Mapped to `GET "/:id/edit"` endpoint

**Input:** `HttpContext * 'Key -> HttpFuncResult`

### create

Operation that creates a new item.

Mapped to `POST "/"` and endpoint

**Input:** `HttpContext -> HttpFuncResult`

### update

Operation that updates an existing item.

Mapped to `POST "/:id"` and `PATCH "/:id"` endpoint

**Input:** `HttpContext * 'Key -> HttpFuncResult`

### delete

Operation that deletes an existing item.

Mapped to `DELETE "/:id"` endpoint

**Input:** `HttpContext * 'Key -> HttpFuncResult`

### delete_all

Operation that deletes all items.

Mapped to `DELETE "/"` endpoint

**Input:** `HttpContext -> HttpFuncResult`

---

### not_found_handler

Define not-found handler for the controller.

**Input:** `HttpContext -> HttpFuncResult`

### error_handler

Define error handler for the controller.

**Input:** `HttpContext * Exception -> HttpFuncResult`

### subController

Add subcontroller

Forward to subcontroller all calls to `/:id/:controller_name` endpoint.

**Input:** `string * ('a -> HttpHandler)`

### version

Define the controller version. Adds checking of `x-controller-version` header.

**Input:** `int`

### plug

Plugs given `HttpHandler` for some [`Action`](#action)s in the controller.

**Input:** `Action list * HttpHandler`
