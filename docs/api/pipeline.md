# Pipeline

## Pipeline builder

Computation expression used to combine `HttpHandlers` in a declarative manner.

The result of the computation expression is a standard Giraffe `HttpHandler` which means that it's easily composable with other parts of the Giraffe ecosystem.

**Example:**

```fsharp
let headerPipe = pipeline {
    set_header "myCustomHeader" "abcd"
    set_header "myCustomHeader2" "zxcv"
}

let endpointPipe = pipeline {
    plug fetchSession
    plug head
    plug requestId
}
```

---

### plug 

Enables adding any additional `HttpHandler` to the pipeline.

**Input:** `HttpHandler`

### must_accept

Filters a request by the `Accept` HTTP header. You can use it to check if a client accepts a certain MIME type before returning a response.

**Input:** `string`

### challenge

Challenges authentication with a specified authentication scheme.

**Input:** `string`

### sign_off

Signs off the currently logged in user with a specified authentication scheme.

**Input:** `string`

### requires_auth_policy

Validates if a user satisfies policy requirement, if not the handler will execute the `authFailedHandler` function.

**Input:** `(ClaimsPrincipal -> bool) -> HttpHandler`

### requires_authentication

Validates if a user is authenticated/logged in. If the user is not authenticated then the handler will execute the `authFailedHandler` function.

**Input:** `HttpHandler`

### requires_role

Validates if an authenticated user is in a specified role. If the user fails to be in the required role then the handler will execute the `authFailedHandler` function.

**Input:** `string -> HttpHandler`

### requires_role_of

Validates if an authenticated user is in one of the supplied roles. If the user fails to be in one of the required roles then the handler will execute the `authFailedHandler` function.

**Input:** `string list-> HttpHandler`

### clear_response

Tries to clear the current response. This can be useful inside an error handler to reset the response before writing an error message to the body of the HTTP response object.

### set_status_code

Changes the status code of the `HttpResponse`.

**Input:** `int`

### set_header

Sets or modifies an HTTP header of the `HttpResponse`.

**Input:** `string -> 'a`

### set_body

Sets or modifies the body of the `HttpResponse`. This HTTP handler triggers a response to the client and other HTTP handlers will not be able to modify the HTTP headers afterwards any more.

**Input:** `byte[]`

### text

Sets or modifies the body of the `HttpResponse` by sending a plain text value to the client. This HTTP handler triggers a response to the client and other HTTP handlers will not be able to modify the HTTP headers afterwards any more. It also sets the `Content-Type` HTTP header to `text/plain`.

**Input:** `string`

### json

Sets or modifies the body of the `HttpResponse` by sending a JSON serialized object to the client. It uses JSON serializer configured by Giraffe. This HTTP handler triggers a response to the client and other HTTP handlers will not be able to modify the HTTP headers afterwards any more. It also sets the `Content-Type` HTTP header to `application/json`.

**Input:** `'a`

### xml

Sets or modifies the body of the `HttpResponse` by sending an XML serialized object to the client. This HTTP handler triggers a response to the client and other HTTP handlers will not be able to modify the HTTP headers afterwards anymore. It also sets the `Content-Type` HTTP header to `application/xml`.

**Input:** `'a`

### negotiate

Sets or modifies the body of the `HttpResponse` by inspecting the `Accept` header of the HTTP request and deciding if the response should be sent in JSON or XML or plain text. If the client is indifferent then the default response will be sent in JSON. This HTTP handler triggers a response to the client and other HTTP handlers will not be able to modify the HTTP headers afterwards anymore.

**Input:** `'a`

### negotiate_with

Sets or modifies the body of the `HttpResponse` by inspecting the `Accept` header of the HTTP request and deciding in what mimeType the response should be sent. A dictionary of type `IDictionary<string, obj -> HttpHandler>` is used to determine which `obj -> HttpHandler` function should be used to convert an object into a `HttpHandler` for a given mime type. This HTTP handler triggers a response to the client and other HTTP handlers will not be able to modify the HTTP headers afterwards anymore.

**Input:** `(IDictionary<string, obj -> HttpHandler>) -> 'a`

### html

Sets or modifies the body of the `HttpResponse` with the contents of a single string variable. This HTTP handler triggers a response to the client and other HTTP handlers will not be able to modify the HTTP headers afterwards anymore. Sets the HTTP header `Content-Type` to `text/html`.

**Input:** `string`

### html_file

Sets or modifies the body of the `HttpResponse` with the contents of a physical html file. This HTTP handler triggers a response to the client and other HTTP handlers will not be able to modify the HTTP headers afterwards anymore. This HTTP handler takes a rooted path of a html file or a path which is relative to the ContentRootPath as the input parameter and sets the HTTP header `Content-Type` to `text/html`.

**Input:** `string`

### render_html

A more functional way of generating HTML by composing HTML elements in F# to generate a rich Model-View output.

**Input:** `XmlNode` 

### redirect_to

Uses a 302 or 301 (when permanent) HTTP response code to redirect the client to the specified location. It takes in two parameters, a boolean flag denoting whether the redirect should be permanent or not and the location to redirect to.

**Input:** `bool -> string` 

### route_ports

If your web server is listening to multiple ports then you can use the `routePorts` HttpHandler to easily filter incoming requests based on their port by providing a list of port number and HttpHandler (`(int * HttpHandler) list`).

**Input:** `(int -> HttpHandler) list` 

### use_warbler

If your route is not returning a static response, then you should wrap your function with a warbler. Functions in F# are eagerly evaluated and the warbler will help to evaluate the function every time the route is hit.

**Input:** `HttpHandler` 

---

## Pipeline Helpers

Module containing a couple of more advanced `HttpHandlers` commonly used in Saturn applications.

### acceptJson

Accepts requests with the `Accept` header set to `application/json`.

### acceptXml

Accepts requests with the `Accept` header set to `application/xml`.

### acceptHtml

Accepts requests with the `Accept` header set to `text/html`.

### acceptMultipart

Accepts requests with the `Accept` header set to `multipart/form-data`.

---

### putSecureBrowserHeaders

Adds headers that improve browser security.
It sets the following headers:

  * x-frame-options - set to SAMEORIGIN to avoid clickjacking through iframes unless in the same origin
  * x-content-type-options - set to nosniff. This requires script and style tags to be sent with proper content type
  * x-xss-protection - set to "1; mode=block" to improve XSS protection on both Chrome and IE
  * x-download-options - set to noopen to instruct the browser not to open a download directly in the browser, to avoid HTML files rendering inline and accessing the security context of the application (like critical domain cookies)
  * x-permitted-cross-domain-policies - set to none to restrict Adobe Flash Player’s access to data

### enableCors

Enables CORS protection using provided config. Use `CORS.defaultCORSConfig` for default configuration.

**Input:** `CORSConfig`

### fetchSession

Fetches session from session provider. If it won't be called session will be synchronusly fetched on first usage.

### fetchModel

Tries to get the model from the request and puts the model into `Items.RequestModel`. If it won't be called content can be fetched using `Context.Controller` helpers.
It won't crash the pipelines if fetching failed.
It optionally takes custom culture name as arguments.

**Input:** `string option`

### head

Convert `HEAD` requests to `GET` requests.

### requestId

Pipeline for generating a unique request ID for each request. A generated request ID will be in the format `uq8hs30oafhj5vve8ji5pmp7mtopc08f`.
If a request ID already exists as the `x-request-id` HTTP request header, then that value will be used assuming it is between 20 and 200 characters. If it is not, a new request ID will be generated.
The request ID is put into a `x-request-id` HTTP header and into the `Items` directory of the HttpContext with key `RequestId`.

### requireHeader

Requires the given value for the given request header.

**Input:** `(header:string) -> (value:string)`
