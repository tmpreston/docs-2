# Controller

## Controller builder

Computation expression used to create Saturn controllers - abstraction representing REST-ish enpoint for serving HTML views or returning data. It supports:
* set of predefined actions that are automatically mapped to the enpoints following standard conventions
* embedding sub-controllers for modeling one-to-many relationships 
* versioning 
* adding plugs for particular action which in principle provides same mechanism as attributes in ASP.NET MVC applications. 
* defining common error handler for all actions
* defining not-found action

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
