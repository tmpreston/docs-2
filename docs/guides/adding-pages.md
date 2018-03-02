# Adding Pages

## Creating the View

Saturn use the [Giraffe View Engine](https://github.com/giraffe-fsharp/Giraffe/blob/master/DOCUMENTATION.md#giraffe-view-engine) to create its pages.
Let create a new folder inside your project folder in src called "Hello".
Inside the folder, create a new file called ``HelloViews.fs``, this file will contain the functions to create the page.
Insert the following in the file.

```fsharp
namespace Books

open Giraffe.GiraffeViewEngine
open Saturn

module Views =
```

this will allow you to use the Giraffe View engine to create your page and allow you to use Saturn features

Now we want a function to create a basic page saying hello

```fsharp
  let index (ctx : HttpContext) =
    div [] [
        h2 [] [rawText "Hello from Saturn!"]
    ]
```

## Creating the controller

In the same folder where ``HelloViews.fs`` is created, create a new file called ``HelloController.fs``

```fsharp
namespace Hello

open Microsoft.AspNetCore.Http
open Saturn

module Controller =

    let indexAction (ctx : HttpContext) =
        Controller.renderXml ctx (Views.index ctx)
```

After that add the resources to your controller

```fsharp
    let resource = controller {
        index indexAction
        show showAction
```

## Adding it to Router

Inside ``Router.fs``, add the following to the end of your browserRouter function

```fsharp
    forward "/hello" Hello.Controller.resource
```

Now run the program and go to ``http://localhost:8085/hello`` and you will see a page like the following

## Sending parameter to your page

What if you want the page to display your name? You need to add a new function in your ``HelloViews.fs``

```fsharp
  let show (ctx : HttpContext) (name : string) =
    div [] [
        h2 [] [rawText ("Hello! " + name)]
    ]
```

And create a new action that call it in ``HelloController.fs``

```fsharp
    let showAction (ctx : HttpContext, name : string) =
        Controller.renderXml ctx (Views.show ctx name)
```

And finally update your ``resource`` function like so

```fsharp
    let resource = controller {
        index indexAction
        show showAction
    }
```

Now run the program and go to ``http://localhost:8085/hello/yourname`` and you will see a page like the following