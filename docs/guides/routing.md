# Routing

Routes are how Saturn connect all the HTTP requests to the different actions. Think of route as the URL of the application. The root is just yoursite.com but you may have an route for your about page such as yoursite.com/about

After creating a Saturn project from the template, you might have a `Router.fs` file like this

```fsharp
module Router

open Saturn
open Giraffe.Core
open Giraffe.ResponseWriters


let browser = pipeline {
    plug acceptHtml
    plug putSecureBrowserHeaders
    plug fetchSession
    set_header "x-pipeline-type" "Browser"
}

let defaultView = scope {
    get "/" (htmlView Index.layout)
    get "/index.html" (redirectTo false "/")
    get "/default.html" (redirectTo false "/")
}

let browserRouter = scope {
    not_found_handler (htmlView NotFound.layout) //Use the default 404 webpage
    pipe_through browser //Use the default browser pipeline

    forward "" defaultView //Use the default view
}

//Other scopes may use different pipelines and error handlers

// let api = pipeline {
//     plug acceptJson
//     set_header "x-pipeline-type" "Api"
// }

// let apiRouter = scope {
//     error_handler (text "Api 404")
//     pipe_through api
//
//     forward "/someApi" someScopeOrController
// }

let router = scope {
    // forward "/api" apiRouter
    forward "" browserRouter
}
```

First, ignoring the commented out code, take a look at the `router` function. Inside is the `forward "" defaultView` line. This means that `browserRouter` will handle all the routes. You can also said that root of the application is the `scope` of the `browserRouter` function.

    If you follow the [how to start guide](how-to-start.md) then you would have added `forward "/hello" HelloView` to the router function. This tells the application to use `HelloView` to handles the route in the the "/hello" scope.

In the `browserRouter` function, there are three lines. `not_found_handler (htmlView NotFound.layout)` tell browserRouter to displays a not found page if the user enter a route that the application does not handles. `pipe_through browser` tell browserRouter to use the `browser` pipeline defined above. You don't have to worry about pipeline now but think of it as like a bunch of setting on how the website will deliver the pages. Lastly, `forward "" defaultView` is like `forward "" defaultView` from `router`. `defaultView` will handle the routes at the root of the `browserRouter` scope.

    Since the root of the root of the application is still just the root. You can also instead put the content of `defaultView` in place of `forward "" defaultView`

Finally, we get to the part where the application is told how to handle the routes. Inside `defaultView` there are three line of code. The first line, `get "/" (htmlView Index.layout)`, tell the application to display `Index.layout` at the root of the application. Breaking it down further, `get` correspond to the HTTP verb GET so when you type in a link, the browser is try to GET the page. The first parameter of `get` is "/" so basicly when getting the root, the get function will return something. The second parameter is `(htmlView Index.layout)` so the `get` function return a html page specified by Index.layout. Likewise, `get "/index.html" (redirectTo false "/")`, if your site was yoursite.com, when navigating to yoursite.com/index.html, the application will redirect to yoursite.com which will be handled by the previous line in the code.