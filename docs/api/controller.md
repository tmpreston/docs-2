# Controller

## Controller builder

Computation expression used to create Saturn controllers - abstraction representing REST-ish enpoint for serving HTML views or returning data. It supports:
* set of predefined actions that are automatically mapped to the enpoints following standard conventions
* embedding sub-controllers for modeling one-to-many relationships 
* versioning 
* adding plugs for particular action which in principle provides same mechanism as attributes in ASP.NET MVC applications. 
* defining common error handler for all actions
* defining not-found action

Result of the computation expression is standard Giraffe's `HttpHandler`which means that it's easily composable with other parts of the ecosytem.

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

### index

Operation that should render (or return in case of API controllers) list of data

Mapped to `GET "/"` endpoint

**Input:** `HttpContext -> HttpFuncResult`

### show

Operation that should render (or return in case of API controllers) single entry of data

Mapped to `GET "/:id"` endpoint

**Input:** `HttpContext * 'Key -> HttpFuncResult`

### add

Operation that should render form for adding new item

Mapped to `GET "/add"` endpoint

**Input:** `HttpContext -> HttpFuncResult`

### edit

Operation that should render form for editing existing item

Mapped to `GET "/:id/edit"` endpoint

**Input:** `HttpContext * 'Key -> HttpFuncResult`

### create

Operation that creates new item

Mapped to `POST "/"` and endpoint

**Input:** `HttpContext -> HttpFuncResult`

### update

Operation that updates existing item

Mapped to `POST "/:id"` and `PATCH "/:id"` endpoint

**Input:** `HttpContext * 'Key -> HttpFuncResult`

### delete

Operation that deletes existing item

Mapped to `DELETE "/:id"` endpoint

**Input:** `HttpContext * 'Key -> HttpFuncResult`

### delete_all

Operation that deletes all items

Mapped to `DELETE "/"` endpoint

**Input:** `HttpContext -> HttpFuncResult`

---

### not_found_handler

Define not-found handler for the controller

**Input:** `HttpContext -> HttpFuncResult`

### error_handler

Define error for the controller

**Input:** `HttpContext * Exception -> HttpFuncResult`

### subController

Adds subcontroller

Forward to subcontroller all calls to `/:id/:controller_name` endpoint

**Input:** `string * ('a -> HttpHandler)`

### version

Define version of controller. Adds checking of `x-controller-version` header

**Input:** `int`

### plug

Plugs given `HttpHandler` for some actions in the controller.

**Input:** `Action list * HttpHandler`
